# PandWise — jouw complete woningdossier en woningwaarderapport

> **Model 2.17.0 · december 2026**  
> Één standalone HTML-bestand. Geen backend, geen API-sleutel, geen opslag.

PandWise combineert openbare registers tot een feitencheck voor een woning: advertentiedata naast BAG, BRK, CBS, WOZ, Funderingscijfer en Klimaatcijfer — alles in één pagina die je rechtstreeks vanuit de browser draait.

---

## Inhoud

- [Werking](#werking)
- [Tabbladen](#tabbladen)
- [Databronnen](#databronnen)
- [Scoremodellen](#scoremodellen)
- [Beperkingen en compliance](#beperkingen-en-compliance)
- [Installatie en gebruik](#installatie-en-gebruik)
- [Ontwikkeling](#ontwikkeling)
- [Licentie](#licentie)

---

## Werking

Voer een adres of Funda-link in. PandWise bevraagt live de publieke PDOK-services en combineert de resultaten tot een dossier met signalen, vergelijkingen en scores. Vul optioneel woningdata uit de advertentie in (vraagprijs, m², staat); het dossier berekent dan ook afwijkingen, een indicatieve waarde en bied- en verkoopstrategie.

Alle HTTP-verzoeken gaan **rechtstreeks vanuit de browser** naar de open registers. Er is geen tussenserver. Er worden geen gegevens opgeslagen of verzonden.

---

## Tabbladen

### Dossier
Het hoofdtabblad met zeven secties:

| Sectie | Inhoud |
|--------|--------|
| 01 / Signalen | Wat afwijkt, wat aandacht vraagt en wat in orde is — rood/geel/groen |
| 02 / Vergelijking | Oppervlakte, perceel en bouwjaar uit de advertentie naast BAG en BRK |
| 03 / Indicatie | Buurt-WOZ per m² doorgerekend naar dit object, met bandbreedte naar staat binnen/buiten |
| 04 / WOZ en markt | Funda-zoekknoppen, WOZ-waarden per peildatum en vergelijking met vraagprijs en CBS-mediaan |
| 05 / Object | Kadastrale aanduiding, perceeloppervlak en BAG-registratie |
| 06 / Buurt | CBS-buurtcijfers (bevolking, woningen, WOZ, energie, criminaliteit) en bouwjaren omgeving |
| Kaart | Luchtfoto en kadastrale kaart van de locatie |

### Adviseursrapport, vergelijking en advertentietekst
- **Adviseursrapport:** knop bij elk dossier. Voorblad met adviseur, kantoor en klant (in te vullen onder de woningdata), samenvatting met de kerncijfers, daarna dossier, funderingscijfer en klimaatcijfer. Print via `window.open()`, dus ook bruikbaar in een iframe.
- **Vergelijking:** met "+ Vergelijk" voeg je tot drie adressen toe en zie je ze naast elkaar. De lijst staat alleen in het geheugen; er wordt niets opgeslagen.
- **Advertentietekst-check:** de geplakte Funda-tekst wordt op trefwoorden gecontroleerd (o.a. erfpacht, asbest, VvE, casco, vocht, funderingsherstel). Het resultaat verschijnt als signaal. Dit is een trefwoordcontrole en geen beoordeling van de woning.
- **Geluid:** RIVM-geluidskaart (Lden, alle bronnen) als modelwaarde op het adrespunt.
- **Verduurzamen:** sectie 07 met energielabel en verwijzingen naar ISDE en Warmtefonds. Bedragen zijn bewust niet opgenomen.

### Funderingscijfer aanpasbaar (2.17.0)
- In de tussenstap staat naast het energielabel een keuzelijst **Funderingscijfer**: standaard het berekende cijfer uit de registers (met titel en label), of een zelf gekozen cijfer van 8 tot 2. Het aangepaste cijfer telt mee in de waarde (rij D5), staat in het dossier met "handmatig aangepast (berekend: X)" en krijgt een melding in het funderingstabblad. Terugzetten kan via «Gegevens aanpassen» en kiezen voor "Berekend". Het bouwjaarverschil-signaal en de alternatieve score verdwijnen zolang het cijfer handmatig is.
- De kop van de tussenstap is «Pas aan wat volgens jou anders is, daarna volgt de woningwaarde», met de toelichting «wijzig ze als het anders is of anders gaat worden».

### Energielabel en fundering in de waarde (2.16.0)
- **Energielabel geschat uit het bouwjaar** aan de hand van de Bouwbesluit-periodes (EPC-eisen en eerdere isolatie-eisen), met een tabel "Hoe schatten we het energielabel?" in het klimaattabblad. Een geregistreerd of zelf ingevuld label gaat altijd voor; verbouwing en isolatie sinds de bouw zijn niet meegenomen. Het label staat als blokje in het dossier (met bron) en is aan te passen in de tussenstap en in het klimaattabblad.
- **Tellen mee in de waarde (aannames, niet gekalibreerd):** label t.o.v. label D, ongeveer enkele procenten per stap (A tot A+++ +3,5 tot +5%, B +2%, C +0,5%, E -2%, F -4%, G -6%); een geschat label telt voor de helft. Het funderingscijfer (2 tot 8) corrigeert van +1% bij 8, neutraal bij 7 en tot -15% bij 2. De tabel toont D4 (energielabel) en D5 (fundering).
- De indicatie ververst direct na aanpassen van het label of het bouwjaar in het funderingstabblad.

### Voor adviseurs (2.15.0)
- **Knop "Voor adviseurs"** in het menu (en de footer) opent een venster met bedrijfsnaam, naam adviseur, adres, postcode en plaats, e-mailadres, telefoonnummer, website, KvK- of AFM-nummer en logo (upload of https-link), met een live voorbeeld.
- **In het rapport:** het voorblad toont logo en contactgegevens en elk rapport krijgt onderaan "Opgesteld door …". Gegevens bovenaan de tool staan in een balk met aanklikbare telefoon-, e-mail- en websitelinks (niet in de afdruk).
- **Insluiten met eigen gegevens:** het venster genereert een iframe-code met `embed=1` en de parameters `adviseur`, `kantoor`, `adres`, `email`, `tel`, `web`, `kvk` en `logo` (alleen https; een geüpload logo past niet in de code). Parameters worden gevalideerd (e-mail, telefoon en https-links) en altijd als tekst weergegeven, nooit als HTML.
- **Geen opslag:** de gegevens staan alleen in het venster en in de code die de adviseur zelf kopieert. Een logo via een https-link wordt rechtstreeks van de server van de adviseur geladen. PandWise geeft algemene informatie en geen advies; de adviseur blijft zelf verantwoordelijk voor adviesdossier en vermeldingen zoals de AFM-vergunning.

### Laadscherm, navigatie, footer en vijf scenario's (2.13.0 tot 2.14.2)
- **Laadscherm bij Analyseren:** een draaiende zandloper, een voortgangsbalk, wisselende berichten en een live lijst van 14 bronnen (bezig, klaar of niet beschikbaar). Tekst: "Dit kan tot een minuut duren". Respecteert `prefers-reduced-motion`.
- **Navigatie rechtsboven:** Home (terug naar de beginstand), Over ons en Disclaimer (uitklapvensters). Footer met menu, bronnen, "Waarom gratis?", modelversie en de disclaimerregel; niet in het rapport of bij `?embed=1`. De disclaimertekst is algemene informatie en geen juridisch advies; laat hem toetsen.
- **Vijf scenario's gelijk aan de keuzelijst Staat:** slecht, matig, redelijk, goed en uitstekend. Het scenario dat bij de gekozen staat hoort (binnen en buiten gelijk) is gemarkeerd; bij gemengde keuzes staat de eigen invoer eronder. Hetzelfde in de berekeningstabel en het rapport (de hero past weer op pagina 1).

### Perceelcorrectie, standaardstaat en rapportopmaak (2.12.0 en 2.12.1)
- **Perceelcorrectie voor woningen met eigen grond:** waarde ~ (perceel ÷ typisch perceel in de omgeving) ^ 0,08, begrensd op ±10%. Het typische perceel is de mediaan van kadastrale percelen van 40 tot 2000 m² binnen circa 200 m (BRK). Niet voor appartementen en meergezinspanden. Het is een expertaanname en niet gekalibreerd; de tabel toont de correctie en de reden als hij niet wordt toegepast.
- **Standaardstaat:** binnen en buiten staan standaard op **uitstekend** (constante `STAAT_STANDAARD`). Dat is een aanname zonder data en geeft een hogere uitkomst dan de neutrale standaard "redelijk"; de toelichting bij de waarde zegt dat.
- **Het uitlegblok "Wat doet PandWise?"** verdwijnt zodra je op Analyseren drukt en komt terug bij "Nieuwe berekening".
- **Rapportopmaak:** scenariokaarten passen binnen het kader, kopjes blijven bij hun inhoud, de berekeningstabel heeft één kolom als BAG- en advertentie-oppervlak gelijk zijn en een bredere labelkolom, de registergegevens staan in twee kolommen, plak- en zoekinstructies komen niet meer in het rapport, het niet bevestigde waardeblok wordt weggelaten en foutmeldingen zijn leesbaar. Teksten over perceel en het aanpassen van gegevens zijn bijgewerkt.

### Tussenstap, woningwaarde en links (2.10.0 tot 2.11.0)
- **Tussenstap vóór de waarde:** na het dossier controleert de bezoeker eerst woonoppervlakte, perceeloppervlakte, bouwjaar, woningtype, staat binnen en buiten, optioneel de vraagprijs en de laatste WOZ-waarde met peildatum. Pas na "Toon indicatieve woningwaarde" verschijnen de waarde en sectie 03; via "Gegevens aanpassen" ga je terug. Het perceel wordt getoond en vergeleken met de BRK, maar telt nog niet mee in de waarde (niet gekalibreerd).
- **Indicatieve woningwaarde:** één getal bovenaan, het midden bij de gekozen staat, met bandbreedte en de drie scenario's eronder. Het label zegt expliciet "geen taxatie".
- **Links:** de laatst bekende WOZ-waarde staat in het WOZ-waardeloket (`wozwaardeloket.nl/?locatie=3825BG%2038`) en kadasterinformatie in de BAG Viewer (`bagviewer.kadaster.nl/?searchQuery=3825bg+38&objectId=…&geometry.x=…&geometry.y=…`, met RD-coördinaten uit de Locatieserver).
- **Robuuster:** als de gemiddelde woonoppervlakte van de hele buurt niet op te halen is, valt PandWise terug op een steekproef van circa 300 m rond het adres (lagere betrouwbaarheid). Is er toch geen waarde te berekenen, dan staat er een melding met reden en een knop "Opnieuw proberen" in plaats van niets.
- **Uitlijning:** knoppenbalk gegroepeerd, rapportknoppen niet meer als tabs gestijld, KPI-blokjes breken geen bedragen meer af en het waardeblok beslaat de volle breedte.

### Homepage (2.9.1)
Voor het eerste dossier toont de homepage alleen het zoekveld en een blok "Wat doet PandWise?" (waarde-indicatie, fundering, klimaat en geluid, signalen, checklist en bronnen, rapport). Het blok "Woningdata uit woningadvertentie" is op de homepage verborgen en verschijnt onder het dossier, waar je het invult en op "Dossier bijwerken" klikt. Met `?embed=1` blijft het uitlegblok verborgen.

### Tweede anker, woningtype en bouwjaarverschil (2.9.0)
- **Anker 2: eigen WOZ van het pand.** Als er een WOZ-reeks is (geplakt of uit het loket) rekent PandWise de laatste WOZ met de CBS-prijsindex door naar nu en zet die naast de buurtmethode. Het verschil (in %) staat in de KPI en de tabel en weegt mee in de betrouwbaarheid (meer dan 10% verschil: -0,5; meer dan 20%: -1 en +2 procentpunt onzekerheid).
- **Woningtype kiezen:** een keuzelijst "Woningtype" overschrijft de automatische herkenning; het controlegetal gebruikt dat type (met de juiste CBS-koppeling voor 2-onder-1-kap).
- **Bugfix woningtype en buurtpanden:** de BAG-WFS-opvraag gebruikte de verkeerde coördinatenvolgorde (lon,lat in plaats van lat,lon bij EPSG:4326) en haalde maximaal 60 panden op. Daardoor werd het woningtype vrijwel nooit herkend en ontbraken de buurtbouwjaren. Nu 250 panden in de juiste volgorde.
- **Bouwjaarverschil:** bij 50 jaar of meer verschil tussen BAG en advertentie toont PandWise beide funderingscijfers (KPI en melding in het funderingstabblad).

### Bronnen, checklist, insluiten (2.8.0)
- **Bronnen en status (sectie 09):** per dossier een lijst van 16 bronnen met "opgehaald", "schatting" of "niet beschikbaar" en een link naar de bron. Dit is de stand op het moment van opvragen.
- **Checklist voor de bezichtiging (sectie 08):** algemene punten, aangepast aan bouwjaar, woningtype, erfpacht en geluid. Staat ook in het kort rapport en het adviseursrapport.
- **Insluiten:** onderaan de pagina staat een iframe-code. Parameters in de query string: `embed=1` (verbergt de kop), `q=adres` en `auto=1` (start direct), `adviseur`, `kantoor` en `logo` (https-link). Een API en white label met eigen domein zijn er nog niet.
- **Waarom gratis / Over PandWise:** onderaan de pagina. Bedrijfsgegevens staan in de constante `BEDRIJF` bovenin het script; lege velden worden niet getoond.

### Delen op LinkedIn en WhatsApp
`index.html` bevat Open Graph- en Twitter-meta (titel, beschrijving en `og-image.jpg`, 1200 × 630, circa 80 kB) voor een nette voorvertoning. De afbeelding staat los in de repo omdat crawlers geen inline afbeelding lezen. Scrapers negeren het deel na `#`, dus een gedeelde link met `#q=…` toont dezelfde voorvertoning. Platforms cachen de voorvertoning: ververs LinkedIn via de Post Inspector; WhatsApp ververst vanzelf of na het toevoegen van een nieuwe queryparameter (`?v=2`).

### Drie waarden naar staat (2.7.0)
De indicatie toont naast de eigen invoer drie scenario's met de staat binnen en buiten gelijk: **slecht**, **redelijk** en **goed**, elk met bandbreedte en midden. De scenario's staan in de KPI bovenaan en als rijen in de berekeningstabel (op BAG-oppervlakte en op advertentie-m²).

### Rapportopmaak (2.6.0)
- Voorblad op één pagina met luchtfoto (PDOK), adviseur, kantoor, klant en optioneel logo (png of jpg, alleen in het geheugen).
- **Kort rapport:** voorblad, samenvatting, signalen en indicatie (circa 5 pagina's); het volledige adviseursrapport bevat daarnaast alle secties, fundering en klimaat.
- Uitgangspunten en beperkingen staan aan het eind. Kop en voet met paginanummer. Lege waarden worden weggelaten.
- Het energielabel in de samenvatting wordt alleen als feit getoond bij EP-online of handmatige invoer; anders als "onbekend; schatting uit bouwjaar".

### Gebouw en verdieping (2.5.0)
- **Bouwlagen en gebouwhoogte** uit de 3D BAG (TU Delft, CORS open); zonder `b3_bouwlagen` wordt de hoogte gedeeld door 2,8 m. Voor het testpand: 8 bouwlagen.
- **Woningtype-fallback:** bij 3 of meer verblijfsobjecten in het pand wordt "Appartement / meergezinspand" gebruikt als het type niet uit de pandgeometrie komt.
- **Verdieping en type** (bijv. galerijflat) is een vrij veld voor het rapport. Galerij of portiek is niet uit open data af te leiden.
- Gebouwspecifieke fundering (FunderMaps) is niet openbaar bereikbaar en niet opgenomen.

### Plak-oplossingen (2.4.0)
Voor gegevens zonder open bron plakt de gebruiker zelf tekst van een openbare pagina; PandWise leest die uit en bewaart niets buiten het geheugen.
- **WOZ-tabel** uit het WOZ-waardeloket: peildatum en waarde worden als handmatige WOZ-waarden overgenomen (de WOZ-loket-API is vanuit de browser geblokkeerd door CORS).
- **Verkoophistorie** (Funda of andere bron): datum, status en prijs; toont of de woning de afgelopen 5 jaar te koop heeft gestaan, hoe vaak, of een aanbod is ingetrokken en het prijsverloop.
- **Aanbod in de buurt** (zoekresultaten): adres, vraagprijs en m² naar een tabel met prijs per m², mediaan en vergelijking met de indicatie voor het pand. Vraagprijzen zijn geen transactieprijzen.
- De plakvelden komen niet in het afgedrukte rapport. Historie, WOZ en adressen zijn gegevens van bewoners: gebruik ze alleen voor het doel van de klant.

### Waarde-indicatie (2.3.0)
- **Controlegetal per woningtype:** verwachte gemiddelde prijs van dit woningtype in de gemeente (CBS 85791NED landelijk per type, geschaald met het regioniveau uit CBS 83625NED). Het telt niet mee in de berekening. Eerder verwees de code naar een tabel (85773NED) zonder gemeente- of typekolommen, waardoor het controlegetal nooit werd getoond; dat is hersteld.
- **Betrouwbaarheid indicatie** (1 tot 5): beoordeelt de consistentie van de gebruikte gegevens (CBS-index aanwezig, woningtype bekend, BAG en advertentie in lijn, oppervlak en midden niet extreem). Het is geen nauwkeurigheidsclaim en niet gekalibreerd.
- **Neutrale standaard voor de staat** (3 = bewoonbaar en normaal onderhouden). De eerdere standaard (4) gaf +5% opwaartse bias.
- **Uitgangspunten en beperkingen** in het adviseursrapport.
- Benchmark: één Calcasa-taxatie (galerijflat Oosterbeek, 95 m²) tegenover de indicatie: taxatie € 424.000, midden € 456.000 (+7,5%), band € 410.000 tot € 500.000. Eén meting is geen validatie.

### Funderingscijfer
Score 2–8 op basis van bouwjaar, bodemtype (BRO), maaiveldhoogte (AHN4), RVO-aandachtsgebied en grondwaterstand (BRO WDM). Toont funderingstype, RVO-status, grondwaterbeordeling, buurtvergelijking en herleidbare rekentabel. Bouwjaarcorrectie mogelijk.

### Klimaatcijfer
Gewogen score 1–10 op basis van vijf risicodimensies:

| Dimensie | Weging | Bronnen |
|----------|--------|---------|
| Fundering | 30% | Funderingscijfer (zie hierboven) |
| Overstroming | 25% | Regioprofiel + overstroomik.nl + maaiveldhoogte |
| Hitte | 20% | Klimaateffectatlas (RIVM/PBL) of regioprofiel |
| Wateroverlast | 15% | Klimaateffectatlas of regioprofiel |
| Droogte | 10% | Klimaateffectatlas of regioprofiel |

Energielabel (EP-online) en bergingspotentieel worden apart getoond en tellen **niet** mee in het cijfer. Het model rondt af op halven en toont een bandbreedte die groeit naarmate meer bronnen op een schatting berusten.

### Biedstrategie
Herleidbare biedopbouw: marktcorrectie (CBS-prijsindex), staat- en vraagprijsaanpassing, ontbindende voorwaarden, biedscenario's en vragen aan de verkopend makelaar.

### Verkoopstrategie
Netto-opbrengstberekening bij meerdere verkoopprijzen: courtage, overige kosten, energielabel-effect en vergelijking met WOZ en buurtmediaan.

---

## Databronnen

Alle bronnen zijn publiek en vrij toegankelijk (geen API-sleutel vereist).

| Bron | Wat | URL |
|------|-----|-----|
| PDOK Locatieserver | Adres, coördinaten, BAG-ID | api.pdok.nl |
| BAG WFS (Kadaster) | Bouwjaar, oppervlakte, gebruiksdoel, panden omgeving | service.pdok.nl |
| BRK Kadastrale Kaart | Perceeloppervlak, kadastrale aanduiding | service.pdok.nl |
| CBS Wijken en Buurten | Buurtcijfers: WOZ, bevolking, energie, criminaliteit | opendata.cbs.nl |
| CBS Prijsindex (86247/86059NED) | Kwartaalindex bestaande koopwoningen per COROP/provincie | opendata.cbs.nl |
| CBS 85773NED | Prijsindex bestaande koopwoningen en landelijke gemiddelde verkoopprijs (geen mediaan, geen gemeentecijfers) | opendata.cbs.nl |
| AHN4 | Maaiveldhoogte (LiDAR, 0,5 m raster) | service.pdok.nl |
| BRO Bodemkaart (TNO/DINOloket) | Grondsoort ter plaatse | service.pdok.nl |
| BRO Grondwaterspiegeldiepte (WDM) | GHG, GLG en grondwatertrap (50×50 m raster) | service.pdok.nl |
| RVO Funderingsviewer | Indicatieve aandachtsgebieden funderingsproblematiek | service.pdok.nl |
| EP-online (RVO) | Geregistreerd energielabel | public.ep-online.nl |
| WOZ-waardeloket (Kadaster) | WOZ-waarden per peildatum (geen open API, knoppen openen het loket) | wozwaardeloket.nl |
| CBS 85791NED en 83625NED | Gemiddelde verkoopprijs per woningtype (landelijk) en per gemeente, voor het controlegetal | opendata.cbs.nl |
| RIVM geluidskaart (Lden alle bronnen) | Geluidsbelasting op het adrespunt (modelwaarde, 10 m raster) | data.rivm.nl |
| Klimaateffectatlas (RIVM/PBL) | Locatiespecifieke rasterwaarden hitte, wateroverlast, droogte | service.pdok.nl |
| Overstroomik.nl (Rijkswaterstaat) | Overstromingsscenario's per adres | overstroomik.nl |
| KNMI'23-klimaatscenario's | Richting 2030/2050 (W+-scenario, peildatum oktober 2023) | knmi.nl |

**Fallbacks:** Als een live bron geen waarde retourneert, valt PandWise terug op regionale schattingen op basis van postcode of provincie. Dit wordt zichtbaar gemarkeerd als `regioprofiel` of `schatting`.

---

## Scoremodellen

### Funderingscijfer (schaal 2–8)
Gebaseerd op de logica van [Funderingscijfer.nl](https://funderingscijfer.nl). Startwaarde op basis van bouwjaar; correcties voor bodemtype, maaiveldhoogte (NAP), RVO-aandachtsgebied en grondwaterstand (GLG). De correcties zijn herleidbaar in een uitklapbare rekentabel.

### Klimaatcijfer (schaal 1–10)
Gewogen gemiddelde van vijf dimensies met een **zwakste-schakel-begrenzing**: een ernstig risico in een zwaarwegende dimensie (fundering, overstroming) kan het cijfer met maximaal 2 punten boven die dimensie houden. Afronding op halven; bandbreedte groeit met het aantal schattingen.

> **Let op:** de weging van de dimensies is een expertinschatting van de financiële impact en is **niet empirisch gekalibreerd** aan schade- of hersteldata. Het cijfer is een indicatie, geen actuarieel model.

### Model versie
`MODEL_VERSIE` en `MODEL_DATUM` staan bovenin het script. Verhoog de versie bij elke wijziging in de scorelogica; het versienummer verschijnt in elk printrapport zodat rapporten later herleidbaar zijn.

---

## Beperkingen en compliance

**Geen taxatie of waardebepaling.** Dit dossier is een feitencheck op basis van openbare registers. Het is geen waardebepaling in de zin van NRVT of NWWI en geen advies in de zin van de Wft. De indicatieve waarde is een rekenkundige benadering op basis van buurt-WOZ en staat; geen vervanging voor een officieel taxatierapport.

**Geen funderingsrapport.** Het Funderingscijfer is een indicatie op basis van bouwjaar en bodemtype. Alleen funderingsonderzoek door een gecertificeerd bureau (KCAF) of het bouwdossier bij de gemeente geeft uitsluitsel.

**Geen klimaatadvies.** Het Klimaatcijfer combineert openbare bronnen en expertinschattingen. Grondwaterwaarden zijn langjarige modelgemiddelden op een raster van 50×50 m en geen actuele peilbuismetingen. Geen vervanging voor professioneel wateradvies.

**WOZ is een fiscale waarde.** De WOZ-waarde wordt vastgesteld naar de peildatum 1 januari van het jaar vóór het belastingjaar en loopt ruim een jaar achter op de markt.

**Funda heeft geen open API.** PandWise biedt knoppen die een Funda-zoekopdracht openen in de browser; vraagprijzen worden niet automatisch opgehaald. Werkelijke koopsommen zijn alleen via de betaalde Kadaster-koopsominformatie beschikbaar.

---

## Installatie en gebruik

PandWise is één zelfstandig HTML-bestand zonder afhankelijkheden.

### Lokaal
```bash
git clone https://github.com/jeroen-o/woningdossier.git
cd woningdossier
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux
```

### GitHub Pages
1. Ga naar **Settings → Pages** in de repo
2. Source: `main` branch, map `/` (root)
3. De pagina is beschikbaar op `https://jeroen-o.github.io/woningdossier/`

### Google Sites embed
```html
<iframe src="https://jeroen-o.github.io/woningdossier/"
        width="100%" height="900" frameborder="0"></iframe>
```
> **Let op:** gebruik `window.open()` voor printen; `window.print()` werkt niet vanuit een Google Sites-iframe. PandWise doet dit al correct.

---

## Ontwikkeling

### Structuur
Alles staat in één `index.html` (~210 kB). De twee scoremodules zijn als zelfstandige IIFE's opgezet:

```
Fund = (() => { ... })()   // Funderingscijfer — schaal 2–8
Klim = (() => { ... })()   // Klimaatcijfer — schaal 1–10
```

### Tests
De jsdom-integratietests zijn te vinden in `ttest*.js` (niet opgenomen in de repo). Een minimale testrun:

```bash
npm install jsdom
node -e "
const {JSDOM}=require('jsdom');
const dom=new JSDOM(require('fs').readFileSync('index.html','utf8'),
  {runScripts:'dangerously',url:'https://example.com/'});
// Voeg fetch-mock toe en bevestig dat dossier wordt opgebouwd
"
```

### Scorewijziging doorvoeren
1. Pas de logica aan in `Fund` of `Klim`
2. Verhoog `MODEL_VERSIE` (bijv. `'2.1.0'`) en zet `MODEL_DATUM` op vandaag
3. Test met een bekend adres en vergelijk het cijfer met de vorige versie
4. Commit met een duidelijk versienummer in de commit-message

### 2.18.x – SEO, GEO, mobiel en rapportuitlijning
- **SEO:** titel/description, canonical, hreflang, Open Graph/Twitter, JSON-LD (WebSite, WebApplication, FAQPage), `robots.txt`, `sitemap.xml`; `?embed=1` krijgt `noindex`.
- **GEO:** `llms.txt` met samenvatting, bronnen, beperkingen en FAQ; zichtbare FAQ op de homepage die overeenkomt met de JSON-LD; AI-crawlers expliciet toegestaan in `robots.txt`.
- **Mobiel:** geen horizontale overflow meer op 390 px (lange kop in tussenstap, grids met `min-width:0`, tabellen scrollbaar).
- **Rapport:** voorblad past op één pagina (compacter contactblok en foto), brontabellen BAG/BRK blijven twee kolommen, scorekaarten breken niet over pagina's.

### 2.21.0 – Bouwjaar- en woningtypecorrectie
- **Bouwjaar (D6):** object t.o.v. het mediaanbouwjaar van de omgeving, 0,4% per decennium, maximaal ±4%; halve correctie als het energielabel al meetelt (voorkomt dubbeltelling). Alleen bij minstens 10 panden in de omgeving.
- **Woningtype (D7):** object t.o.v. de woningmix van de buurt (CBS-aandeel eengezins/meergezins). Aanname: appartement per m² 10% duurder dan een eengezinswoning, maximaal ±8%. Alleen als het woningtype is vastgesteld.
- Beide zijn aannames en niet gekalibreerd; ze staan in `INDIC` en in de berekeningstabel (D6 en D7).

### 2.21.1 – Scheve verdeling van de buurt
- Het buurtgemiddelde woonoppervlak wordt vergeleken met de BAG-mediaan. Wijken ze meer dan 15% af, dan geldt de verdeling als scheef: de betrouwbaarheid daalt een halve punt en de modelonzekerheid stijgt met 1%. De maatcorrectie blijft op het gemiddelde rekenen, omdat de buurt-WOZ per m² ook een gemiddelde is (er is geen mediaan-WOZ beschikbaar).
- README gecorrigeerd: het CBS biedt geen mediaan verkoopprijs.

### 2.22.0 en 2.22.1 – Overige inpandige ruimte telt mee in de waarde
- Het rekenoppervlak van het aangepaste woonoppervlak is woonoppervlak plus 25% van de overige inpandige ruimte (garage, berging). Reden: de buurtwaarde per m² is gedeeld door het gemiddelde BAG-gebruiksoppervlak, en dat telt zulke ruimte al mee. Het gewicht 0,25 (`INDIC.overigGewicht`) is een aanname en niet gekalibreerd; bij leeg of 0 m² verandert er niets. De BAG-berekening (zonder aangepast oppervlak) is ongewijzigd.

### 2.23.0 – Asymmetrische perceelcorrectie
- Perceel **kleiner** dan het typische perceel van de omgeving: ongewijzigd, waarde ~ (perceel ÷ typisch) ^ 0,08, korting maximaal −10%.
- Perceel **groter** dan typisch: waarde ~ (perceel ÷ typisch) ^ 0,20, toeslag maximaal +20% (was ^ 0,08 en maximaal +10%).
- Aanleiding: een eerste vergelijking van 117 woningen (zonder appartementen) met Funda-vraagprijzen liet zien dat het model bij kavels boven 300 m² ongeveer 14% onder de vraagprijs zat, bij kleinere kavels niet. De nieuwe waarden zijn aannames, niet gekalibreerd (vraagprijzen zijn geen transactieprijzen) en moeten met een nieuw monster worden gecontroleerd. Constanten in `INDIC` (`perceelElasticiteitBoven`, `perceelCapBoven`).

### 2.24.0 en 2.24.1 – Toeslag voor vrijstaande woningen
- Een vrijstaande woning krijgt een toeslag van +12% (`INDIC.vrijToeslag`) ten opzichte van het buurtgemiddelde, dat vooral tussenwoningen bevat. Hoek-, 2-onder-1-kap- en tussenwoningen krijgen geen toeslag.
- Onderbouwing: 41 vrijstaande woningen uit drie steekproeven met Funda-vraagprijzen zaten zonder toeslag mediaan 18,5% onder de vraagprijs (90%-interval 11,5 tot 22,5%). De afwijking is het kleinst bij een toeslag van 12 tot 15%; 12% is gekozen omdat de laatste, kleinste steekproef (n=6) bij 15% te hoog uitkwam. Een toeslag afgesteld op twee steekproeven (7%) verbeterde de derde (gerichte) steekproef van een gemiddelde afwijking van 20,0% naar 16,7%.
- De toeslag is een aanname, niet gekalibreerd (vraagprijzen zijn geen transactieprijzen), staat als regel D8 in de berekeningstabel en is niet gecorrigeerd voor buurten die uit vrijstaande woningen bestaan.

### Bekende beperkingen om op te letten
- **KEA-laagnamen:** de Klimaateffectatlas-laagnamen (`hitte_stress_2050`, etc.) zijn gebaseerd op de PDOK-service van september 2026. Controleer via [PDOK WMS Capabilities](https://service.pdok.nl/rivm/klimaateffectatlas/wms/v1_0?SERVICE=WMS&REQUEST=GetCapabilities) als scores onverwacht op regioprofiel terugvallen.
- **Regioprofielen:** de klimaatregioprofielen zijn expertwaarden per postcodegebied, geen gemeten data. Ze zijn bedoeld als vangnet voor locaties zonder live KEA-data.
- **CBS-verkoopprijzen:** het CBS publiceert alleen gemiddelden (geen mediaan): de gemiddelde WOZ-waarde per buurt en de gemiddelde verkoopprijs per woningtype (85791NED) en per gemeente (83625NED). Gemeenten met weinig transacties kunnen een verouderd of ontbrekend kwartaal hebben; PandWise valt dan terug op `TotaleTabel`.

---

## Licentie

MIT — vrij te gebruiken, aan te passen en te verspreiden. Vermeld de oorsprong bij hergebruik.

> Ontwikkeld door [Jeroen Oversteegen](https://github.com/jeroen-o) · Blinqx Verzekering & Hypotheek
