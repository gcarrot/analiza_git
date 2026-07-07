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
