title: Týmový dashboard
date: 2014-02-01 10:50
tags: [dashing, dashboard, continuous integration]
authorId: BST
---

Používáte některý z nástrojů pro [Continous Integration](http://cs.wikipedia.org/wiki/Pr%C5%AFb%C4%9B%C5%BEn%C3%A1_integrace) a dostáváte hromady mailů, které se vám snaží aktivně sdělit, že některý z buildů spadl/obživl/onemocněl? Máte v tom hokej? Čtete dál! ~~Pokud CI nepoužíváte, styďte se!~~
<!-- more -->

Continuous integration je v naší firmě celkem běžná věc, ale stále tomu něco chybělo. Teprve po návštěvě u zákazníka v Londýně mi došlo, že sice dostávám maily, které mi řeknou, že je něco špatně, ale po chvíli absolutně netuším, v jakém stavu náš build server je. Opravil už někdo build? Kolik testů to spadlo? A přitom stačí jen zvednout hlavu. Tým, který jsem byl navštívit, měl na stěně 19" LCD připojené k PC s Linuxem. Používali jednoduchý [software](https://github.com/tuo/jenkins-monitor), který zobrazoval stav 4 buildů. Po návratu jsme si s kolegy řekli, že chceme něco podobného, ovšem trochu víc sexy. Neuplynulo mnoho času a na stole přistálo Raspberry Pi a dva LCD monitory. Podle [šikovného](http://www.inclind.com/journal/development/raspberry-pi-dashing-heroku-dashboard-part-1/detail.htm) návodu jsem na Malinu nainstaloval Raspbian, Ruby a nakonec [Dashing](http://shopify.github.io/dashing/).

### Dashing

Dashing je projekt, který umožnuje vytváření vlastních dashboardů. Backend je napsaný v Ruby, frontend pak využívá hromadu knihoven (gridster, batman), které umožnují přenos dat ze serveru na klientské obrazovky. Konkrétně na přenos dat ze serveru do prohlížeče jsou použity [Server-send Events (SSE)](http://www.html5rocks.com/en/tutorials/eventsource/basics/)

Takto například vypadá jednoduchý job, který jednou za minutu zavolá externí službu a přes SSE pošle data klientům:
```ruby
SCHEDULER.every '1m', :first_in => 0 do |job|
  # zde nasbiram data
  data = call_external_service
  # a zde pres SSE poslu klientum
  send_event('demo', data)
end
```

Druhý způsob, jak dostat na dashboard data je aktualizace zvenčí. Prostě zašlete JSON data na správné URL:
```asciidoc
curl -d '{ "auth_token": "YOUR_AUTH_TOKEN", "current": 100 }' http://localhost:3030/widgets/demo
```

Takto vypadá definice dashboardu s jedním widgetem, který poslouchá naše demo data:
```html
<div class="gridster">
  <ul>
    <li data-row="1" data-col="1" data-sizex="1" data-sizey="1">
      <div data-id="demo" data-view="Number" data-title="Demo"></div>
    </li>
  </ul>
</div>
```



### Náš dashboard

Jelikož používáme Jenkins, měl jsem celkem radost, že už někdo pro Dashing napsal [widget](https://gist.github.com/mavimo/6334816). Trošku jsem ho opravil, abych mohl barevně zdůraznit právě běžící build. Jelikož část týmu je nadále v Londýně, přidali jsme si ještě widget s londýnským časem a nakonec ještě widget s počasím, protože zbylo trochu místa. Zatím poslední přírůstek je oblast, kam může každý poslat libovolnou zprávu, kterou pak vidí celý tým.

{% img /attachments/2014-02-01-teamovy-dashboard/dashboard.jpg %}

Co vidíte na obrázku? Horní řada obsahuje stav čtyř buildů, které se spouští zleva doprava. První je oranžový, což znamená, že build právě běží, druhý je červený, což bohužel znamená, že spadl. V tomto přápadě proto, že neprošly 4 testy. Zbylé dva buildy jsou zelené, tudíž v pořádku. Druhý řádek je celkem jasný: počasí v Praze, čas v Londýně a počty kartiček v [Trellu](http://trello.com). Poslední uzký řádek je již zmíněná zpráva pro tým.
