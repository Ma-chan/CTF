# CSAWCTF　復習
CSAWで全然解けなかったけども、復習のためにwrite upを残しておく.<br>
Cryptoだけ<br>

## bifid 
bifid cipher、古典暗号の換字式暗号の問題.<br>



下記の文字列が記載された2つのtxtファイルが与えられる.<br>

- message.txt<br>
```message.txt
Mr. Jock, TV quiz PhD., bags few lynx
Two driven jocks help fax my big quiz.
Jock nymphs waqf drug vex blitz
Fickle jinx bog dwarves spy math quiz.
Crwth vox zaps qi gym fjeld bunk
Public junk dwarves hug my quartz fox.
Quick fox jumps nightly above wizard.
Hm, fjord waltz, cinq busk, pyx veg
phav fyx bugs tonq milk JZD CREW
Woven silk pyjamas exchanged for blue quartz.
The quick onyx goblin jumps over the lazy dwarf.
Foxy diva Jennifer Lopez wasn’t baking my quiche.
he said 'bcfgjklmnopqrtuvwxyz'
Jen Q. Vahl: bidgum@krw.cfpost.xyz
Brawny gods just flocked up to quiz and vex him.
Emily Q Jung-Schwarzkopf XTV, B.D.
My girl wove six dozen plaid jackets before she quit.
John 'Fez' Camrws Putyx. IG: @kqBlvd
Q-Tip for SUV + NZ Xylem + DC Bag + HW?....JK!
Jumbling vext frowzy hacks pdq
Jim quickly realized that the beautiful gowns are expensive.
J.Q. Vandz struck my big fox whelp
How razorback-jumping frogs can level six piqued gymnasts!
Lumpy Dr. AbcGQVZ jinks fox thew
Fake bugs put in wax jonquils drive him crazy.
The jay, pig, fox, zebra, and my wolves quack!
hey i am nopqrstuvwxzbcdfgjkl
Quiz JV BMW lynx stock derp. Agh! F.
Pled big czar junks my VW Fox THQ
The big plump jowls of zany Dick Nixon quiver.
Waltz GB quick fjords vex nymph
qwertyuioplkjhgfdsazxcvbnm
Cozy lummox gives smart squid who asks for job pen.
zyxwvutsrqponmlkjihgfedcba
Few black taxis drive up major roads on quiet hazy nights.
a quick brown fx jmps ve th lzy dg
Bored? Craving a pub quiz fix? Why, just come to the Royal Oak!
```
- rambling.txt<br>
```rambling.txt
mrocktvquizphdbagsfewlynx
ocknymphswaqfdrugvexblitz
crwthvoxzapsqigymfeldbunk
hmfordwaltzcinqbuskpyxveg
phavfyxbugstonqmilkzdcrew
hesaidbcfgklmnopqrtuvwxyz
enqvahlbidgumkrwcfpostxyz
emilyqungschwarzkopfxtvbd
ohnfezcamrwsputyxigkqblvd
qtipforsuvnzxylemdcbaghwk
umblingvextfrowzyhackspdq
qvandzstruckmybigfoxwhelp
lumpydrabcgqvzinksfoxthew
heyiamnopqrstuvwxzbcdfgkl
quizvbmwlynxstockderpaghf
pledbigczarunksmyvwfoxthq
waltzgbquickfordsvexnymph
qwertyuioplkhgfdsazxcvbnm
zyxwvutsrqponmlkihgfedcba
aquickbrownfxmpsvethlzydg

```


```solve.py

from secretpy import Bifid
from secretpy import CryptMachine

def encdec(machine, enc):
    dec = machine.decrypt(enc)
    print (dec)
    print("----------------------------------")
    return dec

encrypted = "snbwmuotwodwvcywfgmruotoozaiwghlabvuzmfobhtywftopmtawyhifqgtsiowetrksrzgrztkfctxnrswnhxshylyehtatssukfvsnztyzlopsv"
dict = open("plz","r").readlines()[::-1]
for k in dict:
	k = k.strip()
	cipher = Bifid()
	alphabet = []
	for i in k:		
		alphabet.append(i)
	cm = CryptMachine(Bifid(), 5)
	cm.set_alphabet(alphabet)
	encrypted = encdec(cm,encrypted)

```

## modus_operandi


## References

- [CSAW CTF 2020 QUALS | Crypto](https://a-kooli.me/post/csaw-ctf-2020-quals-crypto/#adversarial)
