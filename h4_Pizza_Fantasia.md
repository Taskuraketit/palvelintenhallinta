### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.) Karvinen 2023: Configuration Management of Distributed Systems over Unreliable and Hostile Networks (pdf, ̣mirrors: local, archive.org), vain nämä kohdat:
#### 4.12.1 Size and Complexity of Some DSLs (112. Ominaisuuksien määrä.)

DSL-kielet voivat kasvaa suuriksi ja monimutkaisiksi. **Saltin** DSL sisältää **510 tilafunktiota**, joilla järjestelmänvalvoja hallitsee agenttikoneiden tilaa. Näitä tiloja koskeva dokumentaatio on **yli 20 000 riviä ja 75 000 sanaa**, eli pidempi kuin tyypillinen väitöskirja. Salt ei sisällä suoraan ohjausrakenteita, kuten ehtolauseita tai silmukoita, vaan ne toteutetaan **Jinja2‑mallipohjien** avulla, joilla generoidaan **YAML‑koodia**. Tämä koodi muunnetaan ajon aikana **Pythonin sisäisiksi rakenteiksi**, mikä lisää abstraktiotasoja ja monimutkaisuutta.

**Puppet** tarjoaa **113 valmista funktiota**. Puppet ei myöskään sisällä perinteisiä ohjausrakenteita, vaan käyttää **omia käsitteitään, kuten uusien resurssien ja niiden välisten suhteiden määrittelyä**. Esimerkiksi toistuvien tehtävien sijaan Puppet perustuu virtuaalisten resurssien käsitteeseen, jonka avulla yhteisiä määrittelyjä voidaan hyödyntää useissa tilanteissa.

#### 4.12.2 Use of DSL Functions in Case Configuration (112-115. Mitä oikeasti käytetään.)

Johtavilla konfiguraationhallintatyökaluilla on laajat DSL‑kielet, mutta käytännön tuotantokonfiguraatioissa hyödynnetään vain pientä osaa niiden toiminnoista. Tätä arvioitiin analysoimalla **julkisia Puppet‑konfiguraatioita** Mozilla Release Engineering ‑ympäristöstä sekä Yhdysvaltain hallinnon **USGCB‑baselinea**.

Mozilla‑konfiguraatioissa yleisimmät komennot olivat **tiedostojen (file), pakettien (package), komentojen (exec) ja palveluiden (service) määrittelyt** sekä **ohjausrakenteet case ja if**. Yli **1 %** käyttöosuuden ylittävät komennot kattoivat **87 %** kaikesta käytöstä, mikä osoittaa, että vain harvat funktiot ja rakenteet riittävät suurimpaan osaan tarpeista. Lisäksi käytössä oli jonkin verran sisäisesti määriteltyjä funktioita, kuten palomuurisääntöihin ja rekisteriarvoihin liittyviä.

USGCB‑konfiguraatiossa yleisimmin käytetty funktionaalisuus koostui niin ikään **package‑file‑service‑mallista, exec‑komennosta sekä group‑ ja cron‑funktioista**. Tiedostojen hallinnassa korostui erityisesti **augeas**, jota käytettiin enemmän kuin suoraa file‑määrittelyä. Joitakin perustoimintoja, kuten user‑funktiota, ei käytetty lainkaan.

Analyysin perusteella voidaan todeta, että vaikka Puppet ja muut CM‑työkalut tarjoavat suuren määrän DSL‑funktioita, todelliset tuotantokonfiguraatiot nojaavat pääosin pieneen ja toistuvaan joukkoon yleisiä malleja ja toimintoja.

#### 4.12.3.1 Dependencies Between Main Functions (115-117. Tärkeimmät rakennuspalikat.)




### a) Räpylä. Asenna itse valitsemasi demoni käsin. Jokin muu kuin tunnilla tai kotitehtävissä aiemmin asennettu, eli ei apache, ngninx eikä openssh-server. Kuten aina, testaa lopputulos.

### b) Automaatti. Automatisoi valitsemasi demonin asennus Ansiblella.

### c) Asetus. Muuta asetustiedostoa herralla (master, "control node") ja aja ansible uudestaan. Osoita, että asetukset tulivat käyttöön.

### d) Paikka remonttiin. Riko jotain asetuksia. Voit esimerkiksi poistaa demonin 'sudo apt-get purge foobar' (purge poistaa myös asetustiedostoja), poistaa asennuksen yhteydessä tulevan kansion (sudo rm -r /etc/foobar/ # vaarallista) tms. Ja sitten ajaa ansible-roolisi uudestaan ja todeta, että se korjaa tilanteen.

### e) Idempotentti. Osoita, että tilasi on idempotentti.

### Lähteet

**Karvinen, T. 2024**. Configuration Management of Distributed Systems over Unreliable and Hostile Networks. Luettavissa: https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks. Luettu: 19.4.2026.
