# SECCON 2020 write up
Qiitaとかにまとめてある記事を、読んで理解した事を書いてるだけ.<br>

## this_is_rsa
平文の暗号化に用いたと思われるrubyファイルが与えられる.<br>
rsa暗号を用いて、flagのテキストファイルを暗号化しているらしい.<br>
OpenSSLを暗号化に用いており、OpenSSlの整数オーバーフローの脆弱性を用いて、秘密鍵を入手し、平文を復元する<br>
以下は拡張子rubyのファイル.

- rsa.rb
```ruby:rsa.rb

require 'openssl'

def get_prime
  i = OpenSSL::BN.rand(512).to_s.unpack('H*').hex
  OpenSSL::BN.new(i).prime? ? i : get_prime
end

p = get_prime
q = get_prime
n = p * q
e = 65537
m = File.read('flag.txt').unpack('H*').hex
c = m.pow(e, n)

puts "N = #{n}"
puts "c = #{c}"


```

nの素因数分解でも解けない、RSAの解読法であるWiener攻撃や、Coppersmith攻撃を使っても秘密鍵を取れないので、rubyのOpenSSLの脆弱性を使ってみる.<br>

```
OpenSSL::BN.rand(512).to_s.unpack1('H*').hex
```
が脆弱性で、rand(512)で512bitの擬似乱数生成で1234を返したとすると、.to_sで文字列"1234"となり、.unpack1('H*')で16進数に変換されて、"31323334"となる.<br>
.hexで

- decoder.py
```decoder.py

N = 13234306273608973531555502334446720401597326792644624514228362685813698571322410829494757436628326246629203126562441757712029708148508660279739210512110734001019285095467352938553972438629039005820507697493315650840705745518918873979766056584458077636454673830866061550714002346318865318536544606580475852690351622415519854730947773248376978689711597597169469401661488756669849772658771813742926651925442468141895198767553183304485662688033274567173210826233405235701905642383704395846192587563843422713499468379304400363773291993404144432403315463931374682824546730098380872658106314368520370995385913965019067624762624652495458399359096083188938802975032297056646831904294336374652136926975731836556951432035301855715375295216481079863945383657
c = 9094564357254217771457579638296343398667095069849711922513911147179424647045593821415928967849073271368133854458732106409023539482401316282328817488781771665657515880026432487444729168909088425021111879152492812216384426360971681055941907554538267523250780508925995498013624610554177330113234686073838491261974164065812534037687990653834520243512128393881497418722817552604416319729143988970277812550536939775865310487081108925130229024749287074763499871216498398695877450736179371920963283041212502898938555288461797406895266037211533065670904218278235604002573401193114111627382958428536968266964975362791704067660270952933411608299947663325963289383426020609754934510085150774508301734516652467839087341415815719569669955613063226205647580528

p = q =int("30"*155, 16)
def f(d):
  global p, q
  if d==155:
    return p*q==N
  for a in range(10):
    a <<= d*8
    for b in range(10):
      b <<= d*8
      p += a
      q += b
      if ((p*q)^N)&((1<<(d*8+8))-1)==0:
        if f(d+1):
          return True
      p -= a
      q -= b
f(0)

from Crypto.Util.number import *

d = inverse(65537, (p-1)*(q-1))
flag = pow(c, d, N)

print(bytes.fromhex("%x"%flag).decode())

```


## koharu


- solve.sage
```solve.sage

with open("output.txt") as f:
    line = f.readline()
    p = int(line.split("=")[1])

    PR.<x> = PolynomialRing(GF(p))
    PQ = sage_eval(f.readline().split("=")[1], locals={'x': x})
    f.readline() # pass R
    c = sage_eval(f.readline().split("=")[1], locals={'x': x})

pq = factor(PQ)
P = pq[0][0]
Q = pq[1][0]

print(P)
print(Q)

NP = p ** P.degree()
NQ = p ** Q.degree()
m = ""

from tqdm import tqdm

for f in tqdm(c):
    if power_mod(f, (NP-1)//2, P) == 1 and power_mod(f, (NQ-1)//2, Q) == 1:
        m += "1"
    else:
        m += "0"

m = int(m[::-1],2 )
print(bytes.fromhex(hex(m)[2:]))

```

## urara

- task.sage
```task.sage
from flag import flag

p = random_prime(1 << 1024)
q = random_prime(1 << 1024)
n = p * q

print("n =", n)

# ---

x = int.from_bytes(flag, "big")
y = randint(0, n-1)

a = randint(0, n-1)
b = (y^2 - (x^3 + a*x)) % n

EC = EllipticCurve(Zmod(n), [a, b])

P = EC((x, y))
Q = 2 * P

print("a, b =", [a, b])
print("Q =", Q.xy())

# ---

m = int.from_bytes(flag, "big")
t = randint(0, n-1)

c = power_mod(m + t, 65537, n)
print("t =", t)
print("c =", c)

```

## crypto01 
今回のボス問、回答を読んでも理解し難い.

- challenge.py
```challenge.py

import sys
from sage.all import *
from solve import solve
from flag import flag
from functools import reduce

def encrypt(m, e, n):
    n = int(n)
    size = n.bit_length() // 2
    m_low = m & ((1 << size) - 1)
    m_high = (m >> size)

    b = (m_low**2 - m_high**3) % n
    EC = EllipticCurve(Zmod(n), [0, b])

    return (EC((m_high, m_low)) * e).xy()

def decrypt(c, d, n):
    n = int(n)
    size = n.bit_length() // 2

    c_high, c_low = c
    b = (c_low**2 - c_high**3) % n
    EC = EllipticCurve(Zmod(n), [0, b])
    m_high, m_low = (EC((c_high, c_low)) * d).xy()
    m_high, m_low = int(m_high), int(m_low)

    return (m_high << size) | m_low

def gen_prime(size):
    p = random_prime(1 << size)
    while p % 3 != 2:
        p = random_prime(1 << size)

    q = random_prime(1 << size)
    while q % 3 != 2:
        q = random_prime(1 << size)

    if q > p:
        p, q = q, p

    return int(p), int(q)



# SIZE = 256
# HINTSIZE = 48
# SIZE = 384
# HINTSIZE = 80
SIZE = 512
HINTSIZE = 96
N = 3

flag = int.from_bytes(flag, "big")
assert flag < (1 << SIZE)

masks = [randint(1 << (SIZE-1), 1 << SIZE) for _ in range(N)]
masked_flag = reduce(lambda a, b: a ^ b, masks, flag)


count = 0
ciphertexts = []
while count < N:
    try:
        p, q = gen_prime(SIZE)
        n = p * q

        x = random_prime(int(n ** 0.40))
        y = random_prime(int(sqrt(2 * n // (144 * x*x))))
        zbound = -1 * int(round(((p-q) * (n ** 0.25) * y) / (3 * (p + q))))

        z_ = zbound + ((p + 1)*(q + 1)*y - zbound) % x
        e = ((p + 1) * (q + 1) * y - z_) // x
        d = inverse_mod(e, (p + 1)*(q + 1))

        assert (x*y*x*y < (2 * n // 144))
        assert (gcd(x, y) == 1)

        p_, q_ = solve(n, e, p & ((1 << HINTSIZE) - 1), x, y, SIZE, HINTSIZE)
        if p_ == p and q_ == q:
            d = inverse_mod(e, (p+1)*(q+1))
            c = encrypt(masks[count], e, n)
            assert decrypt(c, d, n) == masks[count]

            ciphertexts.append({
                "n": n,
                "e": e,
                "c": c,
                "hint": p & ((1<<HINTSIZE)-1)
            })
            count += 1

            sys.stderr.write("{}\n".format(count))
    except KeyboardInterrupt:
        break
    except (ZeroDivisionError, OverflowError):
        pass


print("masked_flag = " ,masked_flag)
print("ciphertexts = ", ciphertexts)

```
- decrypto.py
```decrypt.py

def decrypt(c, d, n):
    size = n.bit_length() // 2

    c_high, c_low = c
    b = (c_low**2 - c_high**3) % n
    EC = EllipticCurve(Zmod(n), [0, b])
    m_high, m_low = (EC((c_high, c_low)) * d).xy()
    m_high, m_low = int(m_high), int(m_low)

    return (m_high << size) | m_low


def contfrac(x, y):
    """
    calculate continuated fraction of x/y
    """
    while y:
        a = x // y
        yield a
        x, y = y, x - a * y


def covergents(e, n):
    numerator, denominator = 1, 0
    numerator2, denominator2 = 0, 1
    for x in contfrac(e, n):
        numerator, numerator2 = x * numerator + numerator2, numerator
        denominator, denominator2 = x * denominator + denominator2, denominator

        yield numerator, denominator


def solve(n, e, plow):
    low_size = int(plow).bit_length()
    approx_size = (int(n).bit_length() // 2 - low_size) // 2 - 8
    print("known: {}, approx: {}".format(low_size, approx_size))
    for cov in covergents(e, n):
        x, y = cov

        if x == 0:
            continue

        if x * x > n:
            continue

        u = round(e*y / x - n - 1)
        v = round(sqrt(abs(u**2 - 4 * n)))

        if u < 0:
            continue

        p_ = Integer(u + v) // 2
        size = p_.nbits() - approx_size
        p_ = (p_ >> size) << size

        PR, x = PolynomialRing(Zmod(n), name="x").objgen()
        f = x*2**low_size + plow + p_
        f = f.monic()

        roots = f.small_roots(X=2**(size - low_size), beta=0.5, epsilon=0.05)
        for r in roots:
            p = int(p_ + plow + r * 2 ** low_size)
            if n % p == 0:
                return p, n // p
    return None, None

exec(open("output.txt").read())

# set_verbose(2)

for param in ciphertexts:
    n, e, c, hint = param["n"], param["e"], param["c"], param["hint"]
    p, q = solve(n, e, hint)
    d = inverse_mod(e, (p+1)*(q+1))
    masked_flag = masked_flag ^^ decrypt(c, d, int(n))

print(bytes.fromhex(hex(masked_flag)[2:]))
```


## References
- [SECCON 2020 Online CTF作問者writeup](https://furutsuki.hatenablog.com/entry/2020/10/11/172946)<br>
- [SECCON 2020 Online CTF write-up](https://qiita.com/kusano_k/items/0ec2a09483eaa7b900d1)<br>
- [crypto01の参考文献](https://nitaj.users.lmno.cnrs.fr/kmov1.pdf)
