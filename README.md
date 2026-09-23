# Tunnit ja Pamark – puhelimessa toimiva sovellus

Tämä versio toimii kokonaan selaimessa. Ei omaa palvelinta, Node-asennusta puhelimeen, maksullista julkaisupalvelua eikä sovelluksen käyttäjätilejä. GitHub Pages jakaa vain sovelluksen tiedostot. Kaikki kirjaukset, listapohjat ja XLSX-tiedostot tallennetaan ensin puhelimen selaimen paikalliseen muistiin (IndexedDB).

## GitHub Pages käyttöön

1. Pura **Ajolista-GitHub-Pages.zip** tietokoneella.
2. Lataa ZIP:n **sisältö** repositorion `Temppa82/Tunnit` juureen: `index.html`, `assets`-kansio, `sw.js`, manifesti ja kuvakkeet. Korvaa vanha `index.html` tällä uudella. ZIP-tiedoston lataaminen sellaisenaan ei riitä.
3. GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch**.
4. Valitse **main** ja **/(root)**, sitten **Save**.
5. Odota julkaisua. Osoite on **https://temppa82.github.io/Tunnit/**, kun julkaisu valmistuu. Jos käytät muuta repositorion nimeä, osoitteen loppu vaihtuu.

GitHubiin tuleva valmis paketti ei tarvitse koontikomentoja tai GitHub Actions -asetuksia. Vanhan version Node-palvelinta ei käynnistetä. Sen lähdekoodit voivat jäädä repositorioon, mutta käytössä on uusi valmis index.html. Älä lataa omia listapohjia tai täytettyjä XLSX-tiedostoja julkiseen repositorioon.

## Asenna puhelimeen

Avaa Pages-osoite ensin verkkoyhteydessä ja odota tekstiä **Valmis offline-käyttöön tällä laitteella**.

- Android / Chrome: selaimen valikko → **Asenna sovellus** tai **Lisää aloitusnäyttöön**.
- iPhone / Safari: Jaa → **Lisää Koti-valikkoon**.

Avaa asennettu sovellus kerran verkossa myös sen omasta kuvakkeesta ja tarkista offline-ilmoitus. Valitse listapohjat ja aloita käyttö siellä. Selaimen ja asennetun sovelluksen muistia ei kaikissa puhelimissa jaeta. Tämän jälkeen kirjaukset ja tiedostojen muodostaminen toimivat ilman nettiä. Vain Drive-kirjautuminen ja synkronointi tarvitsevat verkon. ZIP:n index.html:n avaaminen suoraan Tiedostot-sovelluksesta ei asenna PWA:ta.

## Päivittäinen käyttö

1. Kirjoita oma nimi kerran. Valitse **Tunnit** tai **Pamark ajolista**.
2. Täytä kysymykset. Päivämäärä on automaattinen ja muutettavissa. Voit jatkaa myöhemmin ja vaihtaa listojen välillä.
3. Valitse ensimmäisellä tallennuskerralla alkuperäinen XLSX-pohja: tuntilistaan **Tuntilista Teemu.xlsx**, Pamarkiin yrityksen alkuperäinen Pamark-pohja. Pohjat jäävät paikalliseen muistiin.
4. Paina **Tallenna puhelimeen**. Tämä ei ota Google-yhteyttä eikä lähetä tietoja verkkoon.
5. Avaa **Tallennetut listat**, kun haluat viedä XLSX-kopion puhelimen Tiedostot-sovellukseen tai latauksiin. Sovellusmuisti ja Tiedostot-kansio ovat eri asioita.
6. Päivän lopussa avaa **Synkronoi Pamark Driveen** ja paina **Synkronoi**. Tuntilistaa ei lähetetä Driveen.

Esimerkit: 15.9.2026 → `Pamark ajolista syyskuu 1-2 2026.xlsx`; 21.9.2026 → `Pamark ajolista syyskuu 2-2 2026.xlsx`. Jakso määräytyy kirjauksen päivämäärästä.

## Google-yhteys kerran laitetta kohti

Käytä olemassa olevaa Google Cloud -projektiasi. Drive API ja Picker API pitää olla käytössä.

Google Cloudissa lisää OAuth-asiakkaan **Authorized JavaScript origins** -kohtaan `https://temppa82.github.io` (ilman `/Tunnit/`-polkua). Rajaa Picker-avaimen verkkoviittaajat käyttämääsi sivustoon sekä Pickerin tarvitsemaan `https://docs.google.com/*`-osoitteeseen. Huomioi OAuth-sovelluksen testikäyttäjät, jos sovellus on testitilassa.

Sovelluksen **Synkronoi Pamark Driveen → Google-yhteyden asetukset**:

- OAuth-asiakastunnus: aiempi `…apps.googleusercontent.com`-tunnus.
- Google Picker API -avain: olemassa oleva selainavain.
- Google-projektin numero: projektin numeerinen tunniste.

Paina **Tallenna yhteysasetukset → Valmistele Google-yhteys → Kirjaudu Googleen → Valitse jaettu Drive-kansio**. Valitse Ajolistat-kansio. Nämä asetukset tallennetaan vain laitteelle, eikä niitä tarvitse julkaista GitHubissa. OAuth client secret -salaisuutta ei käytetä. Kirjautuminen on ajoittain uusittava.

## Miten muiden kirjaukset säilyvät

Synkronointi hakee jaetun kansion oikean XLSX:n ja etsii oman rivin rekisterinumeron sekä päivämäärän perusteella. Se yhdistää päivän tiedot ladattuun uusimpaan tiedostoon; puhelimen koko paikallista tiedostoa ei lähetetä suoraan vanhan päälle. Alkuperäiset kaavat, ulkoasu ja muiden päivien/autojen solut säilyvät.

Tallennuksessa käytetään Drive-tiedoston versiota (`If-Match`). Jos joku tallensi välissä, sovellus hakee uuden version ja yrittää yhdistämistä uudelleen. Jos samalla autolla ja päivällä on erilainen kirjaus, synkronointi pysähtyy ja oma kirjaus säilyy puhelimessa. Paikallinen korvauspainike ei anna lupaa korvata jaettua ristiriitaista riviä.

Puuttuva jakson tiedosto luodaan automaattisesti. Drivessä tiedostonimi ei ole yksilöllinen: jos kaksi puhelinta luo jakson aivan samanaikaisesti, kansioon voi syntyä kaksi tiedostoa. Sovellus tunnistaa useat samannimiset tiedostot ja pysäyttää synkronoinnin poistamatta tai korvaamatta kumpaakaan. Tarkista ja yhdistä kopioiden sisältö ennen jatkamista. Tavallinen olemassa olevan tiedoston päivitys käyttää version tarkistusta.

Katkon jälkeen odottavat kirjaukset lähetetään uudella Synkronoi-painalluksella. Virheessä niitä ei merkitä onnistuneiksi. Lopuksi sovellus lataa tiedoston uudelleen ja tarkistaa oman kirjauksen. Paikallinen XLSX sisältää omat paikalliset kirjaukset; Drivessä oleva yhteinen XLSX sisältää myös muiden kirjaukset.

## Hyvä tietää

- Säilytä kopio tärkeistä XLSX-listoista myös Tiedostot-sovelluksessa. Selaimen tietojen poistaminen tai sovelluksen poisto voi poistaa paikallisen muistin. Sovellus pyytää pysyvää tallennustilaa, mutta selain päättää sen myöntämisestä.
- Tiedot eivät siirry automaattisesti vanhasta chatgpt.site-osoitteesta GitHub-osoitteeseen.
- Synkronoitavan tiedoston muoto on XLSX. Googlen oma natiivi Sheets-tiedosto on eri tiedostotyyppi, eikä tätä korvata.
- Alkuperäisiä taulukkopohjia ei muuteta. Kulutus- ja hintakentät ovat edelleen paikallisia asetuksia; tässä versiossa ne eivät muuta taulukon laskentaa.
- Koonti, alkuperäisten pohjien säilyminen ja synkronoinnin ristiriitatilanteet testataan paikallisesti. Oikea Google-yhteys sekä Android/iPhone-asennus on vielä varmistettava omilla laitteilla ja tunnuksilla.

## Lähdekoodi kehittäjälle

Erillinen lähdekoodipaketti on muokkaamista varten. Siinä `npm ci` ja `npm run build` muodostavat julkaistavan `dist`-kansion. Käyttäjän ei tarvitse asentaa Nodea tai suorittaa näitä komentoja. Valmis Pages-ZIP sisältää jo koostetun sovelluksen.
