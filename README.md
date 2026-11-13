# PandWise — jouw complete woningdossier en woningwaarderapport

> **Model 2.9.0 · november 2026**  
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
| CBS Mediaan verkoopprijs (85773NED) | Mediaan verkoopprijs per gemeente en woningtype | opendata.cbs.nl |
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

### Bekende beperkingen om op te letten
- **KEA-laagnamen:** de Klimaateffectatlas-laagnamen (`hitte_stress_2050`, etc.) zijn gebaseerd op de PDOK-service van september 2026. Controleer via [PDOK WMS Capabilities](https://service.pdok.nl/rivm/klimaateffectatlas/wms/v1_0?SERVICE=WMS&REQUEST=GetCapabilities) als scores onverwacht op regioprofiel terugvallen.
- **Regioprofielen:** de klimaatregioprofielen zijn expertwaarden per postcodegebied, geen gemeten data. Ze zijn bedoeld als vangnet voor locaties zonder live KEA-data.
- **CBS 85773NED:** de mediaan verkoopprijs is beschikbaar per gemeente en woningtype. Gemeenten met weinig transacties kunnen een verouderd of ontbrekend kwartaal hebben; PandWise valt dan terug op `TotaleTabel`.

---

## Licentie

MIT — vrij te gebruiken, aan te passen en te verspreiden. Vermeld de oorsprong bij hergebruik.

> Ontwikkeld door [Jeroen Oversteegen](https://github.com/jeroen-o) · Blinqx Verzekering & Hypotheek
