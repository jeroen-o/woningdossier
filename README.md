# PandWise — jouw complete woningdossier en woningwaarderapport

> **Model 2.0.0 · september 2026**  
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
