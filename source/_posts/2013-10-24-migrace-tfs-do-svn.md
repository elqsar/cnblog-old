title: Migrace TFS do SVN
date: 2014-01-10 17:25:00
updated: 2014-01-10 17:25:00
tags: [TFS, SVN, migrace, source code]
---
Dřív nebo později to zažije možná každý vývojář, zákazník chce zdrojové kódy včetně historie a to v jiném formátu než právě používáte.

<!-- more -->

Nakonec vám nezbyde než se přizpůsobit. A to i tehdy, když zákazník chce takovou exotiku jako migrovat data z Microsoft TFS serveru do SVN.
Začnete googlovat a najdete pár postupů a nástrojů (například [tfs2svn](http://sourceforge.net/projects/tfs2svn/)), které prohlašují, že vše udělají za vás, ale brzy zjistíte, že podporují jen staré verze TFS, nejsou udržované, nebo mají jiné mouchy.

Stalo se to i nám. A protože neexistoval přímý způsob transferu dat z jednoho centralizovaného systému do druhého, zkusil jsem použít jeden decentralizovaný jako prostředníka. Mám rád GIT, takže volba byla jasná. Zatímco SVN plugin do GIT je součástí základní instalace, TFS plugin si musíte stáhnout a nainstalovat sami.
Plugin najdete snadno na GitHubu ([spraints/git-tfs](https://github.com/spraints/git-tfs)), do GIT si nejjednodušeji nainstalujete pomocí [Chocolatey](http://chocolatey.org/). Migrujete z TFS, takže využití Windows se stejně nevyhnete.

I přestože nejde o složitý postup, má svá úskalí. Snad vám  tento návod pomůže, kdybyste se do podobné situace dostali sami.
Využil jsem mnoho nápadů, které jsem našel po webu. Protože jde ale o vcelku netriviální postup, podělím se o něj, i když autorem těchto myšlenek nejsem.

Začneme tím, že si vytvoříme prázdnou SVN repository, třeba v adresáři **svn** a commitneme do ní první data, připravená v adresáři **initial** (zkušenost při migraci mi ukázala, že použité nástroje vykazují nedeterministické chování, když začnete s úplně prázdnou SVN).
``` bash Vytvoření a nastartování SVN
svnadmin create svn

svn import initial/ file:///C:/path_to_your_project/svn/trunk \
	-m "Initial SVN repository"

svnserve -d -r C:/path_to_your_project
```
Zatímco vytvoření a iniciální commitování do SVN repository můžete dělat v její offline filesystem verzi, pro další použití rozhodně přejděte do serverového módu, vyhnete se dalším problémům způsobených nekompatibilitou některých nástrojů a verzí používané SVN repository.

V případě, že použijete čistou SVN, budete si muset nastavit oprávnění pro zápis, nejjednodušší cesta je nastavit *anon-access = write* v souboru **svnserve.conf** v adresáři **svn/conf**.

Přišel čas na vytažení dat z TFS, tentokrát třeba do adresáře **git**. Tohle chvíli zabere, byli jste varováni. Neuškodí potom optimalizovat GIT DB.
``` bash Checkout dat z TFS do lokální GIT repository
git tfs clone --all -l --with-branches \
	http://your_TFS_server/tfs/DefaultCollection \
	"$/your_project" git

cd git
	
git gc
```

Následně připojíme SVN jako další repository a sesynchronizujeme data
``` bash Připojení SVN repository
git svn init -s --prefix=svn/ svn://localhost/svn

git svn fetch
```

Gratuluji, právě jste se dostali do sitauce, že v GIT máte dva plně oddělené stromy commitů, v jednom vaše data z TFS, v tom druhém jeden commit z SVN. Pokud teď provedete merge těchto tromů a výsledek pushnete do SVN, uloží se vám právě jeden commit s poslední verzí zdrojáků. Jenže zadání je všechna data včetně historie.

{% img /attachments/2013-10-24-migrace-tfs-do-svn/2_trees.png %}

Pomocí standardní funkcionality GIT ale můžete oba stromy spojit tak, že to bude vypadat, jako by šlo o strom jediný. K tomu vám pomůže [Graft Point](https://git.wiki.kernel.org/index.php/GraftPoint). Teď trochu magie

``` bash Vytvoření virtuálního commitu
git rev-list --parents master | grep '^.\{40\}$'

git rev-parse svn/trunk

echo result_of_rev_list result_of_rev_parse >> .git/info/grafts
```

První příkaz projde původní TFS záznamy (nyní v master větvi) a najde hash toho úplně prvního. Druhý příkaz udělá to samé s posledním záznamem v SVN trunk. A ten třetí jen připraví virtuální záznam o tom, že první TFS commit je vlastně následníkem posledního SVN.

{% img /attachments/2013-10-24-migrace-tfs-do-svn/virtual_tree.png %}

Teď už jen zbývá virtuální commit zlegalizovat. A to tak, že necháme celý strom záznamů GIT přefiltrovat. Výsledkem bude i to, že onen virtuální commit bude normálně zahrnut ve stromu commitů. Náš virtuální commit pak už nepotřebujeme. I tahle operace chvíli zabere.

``` bash Legalizace virtuálního commitu
git filter-branch -- ^svn/trunk --all

rm .git/info/grafts
```

A teď už vážně jen ten poslední krok - linearizace záznamů pro účely SVN (vzpomeňte si, jak je SVN neprižná, co se branchování týče) a vlastní commit. A zas a znovu - hodně trpělivosti.

``` bash Finalizace
git svn rebase

git svn dcommit
```

A to je už vážně vše. Vypněte SVN server, uzamkněte zpět anonymní přístup.

Možná vám cestou někde něco selže. Nepropadejte panice, zavolejte nový dcommit a pokračujte dál (tedy po další vlně čekání způsobené automatickým rebase).
Typický problém je například takovýto
```
r31 = 4f431359b2aa088c81823f6daf38a0a558582356 (refs/remotes/svn/trunk)
No changes
9ea633c03c90d014d5006a95bc2e228430dafde0~1 == 9ea633c03c90d014d5006a95bc2e228430dafde0
Unable to extract revision information  from commit ce00defca829acea7cdf8064627f8a60675a41f3~1
```

UPDATE: Bohužel jsem se setkal už i s případem, kdy svn rebase nepomáhá. Nezbylo mi než použít podobný trik jako při propojení původní SVN, tedy vytvořit v řetězu jednotlivých commit záznamů zkratku a onen "prázdný" commit přeskočit (viz grafts). To všechno umí GIT!

Budu rád za vaše komentáře, zda vám návod pomohl, nebo jaké máte zkušenosti s přenosem dat mezi různým repozitáři.

Martin Dobiáš
dobias [@] cngroup.dk
