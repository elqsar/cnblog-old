title: Příběh kartičky
date: 2015/04/18 12:00
tags: [agile, scrum, kanban]
authorId: BST
---

Chtěl bych se vámi podělit o příběh typické kartičky na našem projektu. Říkám-li _kartička_, mám na mysli úkol na naší Scrumban tabuli (používáme [Trello](htttp://trello.com)).

<!-- more -->

###Početí
Karta se obvykle narodí na backlog tabuli, kde dostane do vínku krátký popis, občas snímek obrazovky. Kartu buď založí [**Product Owner (PO)**](#PO) a nebo se tam zjeví jako chyba nahlášená [**Bug Hunterem**](#BH) či testerem.

###Zrození
Při plánování nové iterace nám **PO** naznačí, které kartičky by rád viděl na naší tabuli. Po krátké diskuzi nad obsahem karty dojde k odhadu složitosti - jelikož je náš tým rozdělen kanálem La Manche a plánuje přes videokonferenci, místo karet jednoduše na prstech ukážeme číslo. Stupnice vypadá přibližně takto:

TODO - zlepsit popisky
**1** jednoduchý úkol, krátký SQL skript, změna několik řádků bez velkého vlivu na aplikaci - pověstný pětiminutový úkol
**2** práce, která se může protáhnout na den dva, může zasáhnout více aplikací
**3** nejčastější velikost, několik dnů práce, větší počet testů, nová drobná vlastnost
**5** složitější vlastnost, spolupráce s kolegy vývojáři, ne zcela jasné hranice úkolu, klidně i týden práce
**SPIKE** TODO

Větší úkoly se snažíme rozsekat na menší, už dlouho jsem neviděl kartičku se složitostí 8.

### Předškolní výchova
Pokud se vývojář či vývojářka ocitne bez práce, zabrousí do backlogu a na jeho vrcholu spatří novou kartičku. Sezve si **testera (QA)** (v našem případě testerku), **PO** a promluví o kartě a snaží se nalézt vzájemné porozumění k dalšímu životu karty. Tomuto procesu říkáme [**defuzz**](#defuzz). Z výše uvedených důvodu proběhne defuzz obvykle přes Skype.

### Dospívání
Vývoj, kódování, život, testy. Během práce na kartě dochází k upřesňování požadavků mezi vývojářem, QA a PO.

### Vysoká škola
Je-li vývojář spokojen s prací, pochlubí se s výsledkem QA a PO. Tomuto procesu říkáme **demo**. Nejde o demo tak, jak je známo ve Scrumu, protože karta ještě není zcela hotová. Je-li co ukázat, přes sdílenou obrazovku vývojář předvede, co naprogramoval, k čemuž mají často QA i PO připomínky, dotazují se hraniční podmínky. Doladí se drobnosti jako popisky, drobné vady na kráse. Pokud byly rozdíly v tom, co PO chtěl a vývojář stvořil, velké, doplní se kartička a kolečko se zopakuje.

### Zkouškové období
Všichno jsou spokojení s tím, **co** kartička umí, nyní je třeba zaměřit se na to, **jak** toho dosáhla. Vývojář se sežene jiného kolegu či kolegyni a kouknou se kódu na zoubek, spáchají **párový commit**. Autor krátce vysvětlí jak k problému přistoupil, jak uvažoval a jaké změny provedl. Oponent se v kódu šťourá, škodolibě poukáže na logické nedostatky, zapomenuté debugovací hlášky a nadhodí, že těch 20 řádku se dá nahradit voláním knihovní funkce, kterou už v aplikaci používáme. Zdůrazňuji, že v tomto okamžiku ještě kód není v oficiálním repositáři (používáme GIT, privátní větvě, ať už lokální či vzdálené, jsou zcela na vůli vývojáře).

### Promoce
Vše dobře dopadlo, (zatím) jsou všichni spokojení, kartička se přesouvá do sloupce QA a vývojář se prozatím může vrhnout na novou.

### Hledání práce
Jakmile se kartička dostane k testerce, nastanou vývojáři krušné časy. Karta je mučena, zkoumána a občas i odmítnuta. Je-li nalezena chyba (i to se stává), zodpovědný vývojář ji co nejrychleji opraví.

### Pracovní úřad
Zde se kartička setkává se svými kolegyněmi a čekají a čekají .... čas od času se objeví PO, na kartičku se podívá a přesouvá ji do sloupce Hotovo, není-li k dispozici PO, případně jde-li o drobnou kartu či chybu, přesune ji QA.

### Soutěž krásy
Na konci iterace je kartička předvedena celému týmu, pokud je ovšem do ukazovat (na refactorovaní toho například moc ke kouknání není).

### Práce, daně, smrt
Karta je hotová, dostane se na produkční server. Je-li vše v pořádku, karta je archivována. Pokud se líbí a případně pokud se velmi nelíbí, má své následovníky. Kartička končí, ale její kód žije dál.

### Podivná setkání po promoci
#### Zombie
Je-li kartička součástí něčeho většího a není ji dáno se ihned předvést, je na produkčním serveru skryta - viz [**toggle**](#toggle). Taková karta se  nenadání objevit vývojáři před nosem i po poměrně dlouhé době.
#### Bumerang
S takovou kartou se vývojář potkává vícekrát, často kvůli dlouhé či nepřímé/nepřesné zpětné vazbě od koncových zákazníků nebo PO.

### Vývojová vada
TODO

#### Zeptat se kluku na dalsi typy karet
TODO

## Závěr
Typický životní cyklus karty je něco přes týden, jelikož **release** je fázově posunut za iterací. Píšu typický - jsou karty, které zesnou dříve, než můžou splatit studentskou půjčku a jsou karty, u kterých má člověk pocit, že snad nikdy nezmizí.

##Slovník

<a name="PO"></a>`Product Owner`  
> v našem případě kartu zná, může rozhodnout o případných změnách. Většinu času je k dispozici.

<a name="BH"></a>`Bug Hunter` 
> obětní beránek, na kterého se obracejí QA a PO v případě potíží na živých serverech. Nevděčná to role, jejíž obsazení se mění každou iteraci a která chrání zbytek týmu před před náhodnou lavinou dotazů

<a name="defuzz"></a>`Defuzz`
> TODO

<a name="toggle"></a>`Toggle`
> TODO

 