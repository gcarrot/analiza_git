# Analiza Git repozitorija

  

## Kaj se najbolj spreminja



    `git log --format=format: --name-only --since="1 year ago" | sort | uniq -c | sort -nr | head -20`

  
Ta ukaz prikaže 20 datotek, ki so se v zadnjem letu največkrat

spreminjale. Datoteka na vrhu seznama je skoraj vedno tista, na katero

te ljudje opozorijo: »Ja, tista datoteka ... nihče se je ne upa

dotakniti.«\

**Opomba:** Takšne datoteke so pogosto kompleksne, slabo razumljene ali kritične za delovanje sistema.

  

------------------------------------------------------------------------

  

## Kdo je to zgradil

  

    `git shortlog -sn --no-merges`

  

Seznam vseh sodelujočih, razvrščen po številu commitov.

- Če ena oseba prispeva 60 % ali več, imaš *bus factor* problem.

- Če je ta oseba odšla pred pol leta, je to resen problem.

- Če glavnega avtorja ni med aktivnimi v zadnjih 6 mesecih

(`git shortlog -sn --no-merges --since="6 months ago"`), je to rdeča zastavica.

  

------------------------------------------------------------------------

  

## Kje se kopičijo hrošči (bugi)

  

    `git log -i -E --grep="fix|bug|broken" --name-only --format='' | sort | uniq -c | sort -nr | head -20`

  

Filtriran seznam commitov, povezanih z napakami.

- Primerjaj s seznamom pogosto spreminjanih datotek.

- Datoteke na obeh seznamih predstavljajo največje tveganje.

  

------------------------------------------------------------------------

  

## Ali projekt napreduje ali umira

  

    `git log --format='%ad' --date=format:'%Y-%m' | sort | uniq -c`

  

Število commitov po mesecih skozi celotno zgodovino.

- Enakomeren ritem = zdrav projekt

- Nenaden padec = verjetno odhod člana ekipe

- Dolgoročen upad = izguba zagona

- Periodični skoki = delo v batchih (release cikli)

  

------------------------------------------------------------------------

  

## Kako pogosto ekipa "gasi požare"

  

    `git log --oneline --since="1 year ago" | grep -iE 'revert|hotfix|emergency|rollback'`

  


Pogostost hitrih popravkov.

- Nekaj na leto = normalno

- Pogosto = težave v deploy procesu

- 0 rezultatov = stabilnost ali slabi commit opisi


------------------------------------------------------------------------

## Povprečno število commitov na dan

  `git log --since="1 year ago" --date=short --format='%ad' | sort | uniq -c`


Prikaže intenzivnost razvoja skozi leto.
Enakomeren razvoj je običajno znak zdrave ekipe.
Veliki vrhovi tik pred release-i lahko pomenijo:
- prepozno integracijo,
- premalo sprotnega testiranja,
- večje tveganje za regresije.


------------------------------------------------------------------------
## Kateri direktoriji se največ spreminjajo
  `git log --name-only --format=format: --since="1 year ago" | cut -d/ -f1 | sort | uniq -c | sort -nr`


Analiza po modulih oziroma direktorijih.

Če en modul predstavlja večino sprememb:
- je poslovno najbolj aktiven,
- vsebuje največ tehničnega dolga,
- ali pa je slabo razdeljen na manjše komponente.
------------------------------------------------------------------------
## Dnevi z največ spremembami
`git log --date=format:'%A' --format='%ad' | sort | uniq -c`


Prikaže, kateri dnevi v tednu vsebujejo največ commitov.

Primeri interpretacije:

- največ commitov v petek → večje tveganje za nestabilne release,
- največ commitov v ponedeljek → razvoj po planu sprinta,
- veliko commitov med vikendom → možna preobremenjenost ekipe.
------------------------------------------------------------------------
## Povprečno število commitov na razvijalca
`git shortlog -sn --since="1 year ago"`

Omogoča primerjavo aktivnosti članov ekipe.

Pomembno:

- manj commitov ne pomeni manj dela,
- nekateri delajo večje featureje,
- nekateri opravljajo reviewe, arhitekturo ali podporo.

Ukaz je uporaben predvsem za odkrivanje ekstremov.

------------------------------------------------------------------------
## Kateri deli kode so najstarejši
`git log --reverse --stat`

Prikaže zgodovino od prvega commita dalje.

Najstarejši deli sistema pogosto:

- vsebujejo največ poslovne logike,
- imajo najmanj testov,
- se jih razvijalci najmanj dotikajo.
To so običajno najbolj občutljivi deli aplikacije.
------------------------------------------------------------------------
## Datoteke z največ izbrisanimi vrsticami
`git log --numstat --since="1 year ago"`

Omogoča analizo dodanih in odstranjenih vrstic.

Veliko brisanja pomeni:

- pogosto prepisovanje kode,
- nestabilne zahteve,
- refaktoriranje.
Če ista datoteka stalno raste in se nato briše, je verjetno potrebna arhitekturna prenova.

------------------------------------------------------------------------
## Najstarejši odprti branchi
`for branch in $(git for-each-ref --format='%(refname:short)' refs/heads/); do
echo "$(git log -1 --format='%ci' $branch) $branch"
done | sort`

Pokaže branche po datumu zadnjega commita.

Če branch več mesecev miruje:
- je verjetno pozabljen,
- vsebuje zastarelo funkcionalnost,
- bo merge bistveno težji.
------------------------------------------------------------------------
## Koliko branchov ostaja nezdruženih
`git branch --no-merged`


Prikaže branche, ki še niso združeni v trenutno vejo.

Veliko starih branchov lahko pomeni:

- pozabljene funkcionalnosti,
- nedokončan razvoj,
- slabo urejen Git proces.

Priporočljivo je redno čiščenje zastarelih branchov.
------------------------------------------------------------------------
## Kdo največ pregleduje zgodovino (Blame)
`git blame imeDatoteke.cs`

Prikaže, kdo je nazadnje spreminjal posamezno vrstico.

Uporabno za:

- razumevanje zgodovine kode,
- iskanje avtorja določene funkcionalnosti,
- ugotavljanje izvora napake.
Ni namenjen iskanju "krivca", ampak razumevanju konteksta.
------------------------------------------------------------------------
## Najdaljši čas brez commitov

`git log --date=short --pretty=format:%ad`

Izpis datumov commitov omogoča analizo obdobij brez razvoja.

Daljše prekinitve lahko pomenijo:
- zamrznjen razvoj,
- čakanje na naročnika,
- menjavo ekipe,
- večji razvoj na zasebnih branchih.
  
------------------------------------------------------------------------
## Datoteke z največ različnimi avtorji
`git log --format='%an' --name-only | awk 'NF{if(prev!=""){print prev,$0}else{prev=$0}}'`

(Za bolj natančno analizo je priporočljivo uporabiti namensko skripto.)

Ideja je ugotoviti, katere datoteke spreminja največ različnih razvijalcev.

Veliko različnih avtorjev lahko pomeni:

- pomemben del sistema,
- nejasno odgovornost,
- slabo modularnost.
Če isto datoteko redno spreminja večina ekipe, gre pogosto za arhitekturno ozko grlo.
------------------------------------------------------------------------

## Velikost commitov
`git log --shortstat --since="1 year ago"`
Prikaže število dodanih in izbrisanih vrstic za vsak commit.

Veliki commiti (več tisoč vrstic) so pogosto znak:

- več različnih sprememb v enem commitu,
- težjega code review-ja,
- večjega tveganja pri odpravljanju napak.
Manjši, pogostejši commiti so praviloma bolj pregledni in lažje sledljivi.
------------------------------------------------------------------------

