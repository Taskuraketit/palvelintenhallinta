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

Konfiguraationhallinnan näkökulmasta keskeiset DSL‑funktiot perustuvat muutamaan ydintoimintoon ja niiden välisiin riippuvuuksiin. Yleisimpiä toimintoja ovat **package‑file‑service, user, group ja exec**, joita käytetään erityisesti demonien ja sovellusten konfigurointiin. Näistä muodostuvat keskeiset kategoriat: demonien asennus (package, file, service), sovellusten asennus (package, file), käyttäjähallinta (user, group) sekä tiedostojen käsittely (file, directory, symlink).

Järjestelmän tulee olla **idempotentti, eli se tekee muutoksia vain silloin, kun järjestelmä ei vielä vastaa haluttua tilaa**. Idempotenssi voidaan toteuttaa yksinkertaisilla **tarkistuksilla**, kuten tutkimalla tiedoston olemassaoloa tai sisältöä ennen muutoksen tekemistä. Useimmat CM‑funktiot päätyvät ajamaan natiivin komennon agenttijärjestelmässä, esimerkiksi paketin asennuksen kautta. Osa toiminnoista, kuten file, on perusteltua toteuttaa suoraan **ilman ulkopuolisia ohjelmia**, koska tiedostoilla on useita hallittavia ominaisuuksia.

Riippuvuusanalyysi osoittaa, että **vain harvat funktiot vaikuttavat suoraan järjestelmään**. Keskeisimmät ovat **exec ja file**, joiden päälle muut resurssit kuten **package, service, user ja group** voidaan rakentaa. Myös korkeamman tason toiminnot, kuten **cron‑ajastukset**, voidaan toteuttaa näiden perusfunktioiden avulla. Käytännön tuotantokonfiguraatioista havaitaan, että valtaosa hallinnasta perustuu juuri näihin perustoimintoihin, ja lisäabstraktiot voivat lisätä järjestelmän monimutkaisuutta ilman selkeää hyötyä.


### a) Räpylä. Asenna itse valitsemasi demoni käsin. Jokin muu kuin tunnilla tai kotitehtävissä aiemmin asennettu, eli ei apache, ngninx eikä openssh-server. Kuten aina, testaa lopputulos.

Päätin valita **failb2ban**-demonin, joka tarkistaa lokitiedostot (/ var / log / apache / error_log) ja blokkaa haitallista toimintaa osoittavat IP-osoitteet, kuten liian monta väärää salasanaa (*brute force -hyökkäykset*) ja haavoittuvuuksien etsimistä. [Naranjo, D.y. s.a.](https://blog.desdelinux.net/fi/fail2ban-on-erinomainen-vaihtoehto-torjua-palvelimen-raakavoimat/).

Tuttuun tapaan aloitin pakettien päivityksellä ja asentamisella. Asennuskomennossa '-y' estää varmistusten kysymisen asennuksen aikana.

````bash
sudo apt-get update
sudo apt-get install fail2ban -y
````

Todennetaan seuraavaksi, että fail2ban on asentunut ja toimii.

**Paketin olemassaolon tarkistus**

````bash
dpkg -l | grep fail2ban
````

<img width="1262" height="59" alt="image" src="https://github.com/user-attachments/assets/1c05f9cd-c4af-4ccc-b05a-8a85f389c374" />

**Palvelun tilan tarkistus**

````bash
systemctl status fail2ban
````

<img width="898" height="77" alt="image" src="https://github.com/user-attachments/assets/32135233-741f-4333-bce3-c0a12bb03407" />

**Konfiguraation kevyt testaus**

````bash
sudo fail2ban-client ping
````

pong-vastaus kertoo, että fail2ban toimii.
<img width="488" height="37" alt="image" src="https://github.com/user-attachments/assets/86c9b2ff-2030-479f-8298-c4c07d3de36d" />


### b) Automaatti. Automatisoi valitsemasi demonin asennus Ansiblella.

**Luodaan ensin fail2ban-rooli**. Käyttämällä komennossa '-p'-osaa luodaan kerralla koko hakemistopolku alikansioineen.

````bash
cd bansibles/
mkdir -p roles/fail2ban/tasks
tree
````

<img width="629" height="304" alt="image" src="https://github.com/user-attachments/assets/7088b6e6-9560-4883-995f-b6d44f0b06a2" />


**Luodaan main.yml-tiedosto tasks-kansioon**

````bash
micro roles/fail2ban/tasks/main.yml
````

Tiedoston sisältö:

<img width="796" height="332" alt="image" src="https://github.com/user-attachments/assets/b3eb4744-a4c4-47a5-9970-c5a3891a4515" />

**Kytketään rooli mukaan site.yml:ään**

````bash
micro site.yml
````

Lisätään fail2ban-rooli edellisten jatkoksi:

<img width="279" height="238" alt="image" src="https://github.com/user-attachments/assets/0378bd8f-1859-46e6-967b-5431e78c1125" />


Laitetaan fail2ban pois päältä, jotta voidaan paremmin todentaa Ansiblen (asentaneen ja) käynnistäneen sen. Tarkistetaan vielä sen tila lopuksi.

````bash
sudo systemctl stop fail2ban
sudo systemctl disable
systemctl status fail2ban
````

<img width="1082" height="227" alt="image" src="https://github.com/user-attachments/assets/53b0169f-9990-4525-94ca-e1a2a8dff738" />


Statuksesta nähdään, että paketti on edelleen asennettuna, mutta palvelu ei ole käynnissä eikä käynnisty automaattisesti.

**Ajetaan seuraavaksi Ansible-playbook**
````bash
ansible-playbook -i hosts.ini site.yml
````

Komento tuotti seuraavanlaisen virheilmoituksen:

<img width="1262" height="473" alt="image" src="https://github.com/user-attachments/assets/2177f2dc-7353-4d1f-93d0-5568117b91e5" />

Koska en tarvitse nginx:ää tässä tehtävässä, kommentoin rivin pois site.yml-tiedostosta.

````bash
micro site.yml
````

Muokattu versio:
<img width="306" height="243" alt="image" src="https://github.com/user-attachments/assets/60bd8f07-85af-452d-87dc-8cd891370dd0" />


**Ajetaan ansible-playbook uudelleen**

````bash
ansible-playbook -i hosts.ini site.yml
````

Tällä kertaa kaikki näytti menevän läpi oikein.

<img width="1262" height="260" alt="image" src="https://github.com/user-attachments/assets/b6a286cd-f9c2-4203-96e2-a23999a8d657" />

Tarkistetaan vielä fail2banin tila:

````bash
systemctl status fail2ban
sudo fail2ban-client ping
````

<img width="890" height="76" alt="image" src="https://github.com/user-attachments/assets/582e606f-2463-4e46-a52a-bb2e3bb70b1d" />

<img width="586" height="39" alt="image" src="https://github.com/user-attachments/assets/82b7e89d-568d-4fd6-9c15-ff1296f73549" />

Nämä testit osoittavat, että asennus Ansiblen kautta toimii odotetulla tavalla.

### c) Asetus. Muuta asetustiedostoa herralla (master, "control node") ja aja ansible uudestaan. Osoita, että asetukset tulivat käyttöön.

### d) Paikka remonttiin. Riko jotain asetuksia. Voit esimerkiksi poistaa demonin 'sudo apt-get purge foobar' (purge poistaa myös asetustiedostoja), poistaa asennuksen yhteydessä tulevan kansion (sudo rm -r /etc/foobar/ # vaarallista) tms. Ja sitten ajaa ansible-roolisi uudestaan ja todeta, että se korjaa tilanteen.

### e) Idempotentti. Osoita, että tilasi on idempotentti.

### Lähteet

**Karvinen, T. 2024**. Configuration Management of Distributed Systems over Unreliable and Hostile Networks. Luettavissa: https://westminsterresearch.westminster.ac.uk/item/w7vvz/configuration-management-of-distributed-systems-over-unreliable-and-hostile-networks. Luettu: 19.4.2026.

**Naranjo, D.y. s.a.** Fail2Ban on erinomainen vaihtoehto torjua palvelimen hyökkäykset. Luettavissa: https://blog.desdelinux.net/fi/fail2ban-on-erinomainen-vaihtoehto-torjua-palvelimen-raakavoimat/. Luettu: 19.4.2026.
