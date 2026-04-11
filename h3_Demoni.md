### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.)

#### 💻 Karvinen 2026: Apache installed with Ansible - quick notes

Ansiblen avulla voidaan asentaa Apache 2 tai muu webbipalvelin automaattisesti. Webbisivun saa näkymään localhostilla ja sitä voi muokata normaalikäyttäjän oikeuksin. 

Vaiheet lyhyesti ovat
1) **packagen asennus** (sudo apt-get install apache2)
2) **tarvittavien tiedostojen muokkaus** (sudoedit /etc/apache2/...)
3) **servicen käynnistys eli kansankielellä demonin potkaisu** (sudo systemctl restart apache2)

Artikkelissa löytyy myös esimerkkisisällöt hakemistorakenteeseen **roles/apache2/** sekä tiedostoihin **tasks/main.yml**, **handlers/main.yml* sekä **files/example.com.conf**.

🔔 **Omia mietteitä:**
- webbipalvelimen ja webbisivun pystytys ovat keskeisiä taitoja linux-ympäristön ylläpidossa, koska kyseessä on yksi linuxin perustoiminnoista
- jos aikoo operoida enemmänkin linuxin kanssa, on tämä taito hyvä osata suoraan selkärangasta -> harjoitus tekee mestarin tässäkin!

#### 💻 Ansible Community Documentation: Handlers: running operations on change
  - Handlers: running operations on change (johdantokappale pääotsikon alta)
  - Notifying handlers

Handlerit An­siblessa ovat tehtäviä, jotka suoritetaan vain silloin, kun jokin toinen tehtävä aiheuttaa muutoksen kohdejärjestelmässä (notify). Ne ratkaisevat tilanteen, jossa tietty toimenpide (esim. palvelun uudelleenkäynnistys) on tarpeen vain muutoksen jälkeen, ei jokaisella ajokerralla.

🔔 **Omia mietteitä:**
- Aihetta lukiessa mieleeni heräsi kysymys: Kannattaako kaikki restartit tehdä handlerilla vai onko tilanteita, joissa välitön restart on parempi vaihtoehto?

Kysyin tätä Microsoft Copilotilta ja se vastasi seuraavasti:

> Useimmiten restartit kannattaa tehdä handlerilla, mutta on poikkeuksia.
>
> - **Handler** on oikea valinta, kun restart tarvitaan vain **jos konfiguraatio muuttuu** (yleisin ja suositeltu tapa).
> - **Välitön restart** voi olla perusteltu, jos:
> 
>   - seuraava tehtävä edellyttää heti uutta tilaa (esim. testaus heti muutoksen jälkeen)
>   - muutos ei kestä playn loppuun odottamista
>   - kyse on kertaluonteisesta tai ohjatusta toimenpiteestä
>
> **Yleisohje: käytä handleria oletuksena**, ja tee restart heti vain harkitusti ja perustellusta syystä.

    
#### 💻 'ansible-doc service':
  - johdantokappale (MODULE alta)
  - enabled
  - name
  - state
  - EXAMPLES

ansible.builtin.service‑moduulia käytetään palveluiden hallintaan etäkohteissa (esim. käynnistys, pysäytys, uudelleenkäynnistys). Se tukee useita init‑järjestelmiä, kuten systemd, SysV, upstart, OpenRC ym.

Moduuli toimii välikerroksena (proxy) taustalla olevalle palvelunhallintamodulille (esim. systemd tai sysvinit). Näin sama playbook toimii eri ympäristöissä ilman init‑järjestelmäkohtaisia muutoksia. Käytettävä palvelunhallinta valitaan automaattisesti tai tarvittaessa use‑valitsimella.

Dokumentaatio kattaa vain ne argumentit, jotka ovat yhteisiä kaikille tuetuille järjestelmille, joten init‑järjestelmäkohtaisiin erikoisominaisuuksiin tarvitaan erilliset moduulit.
Windows‑ympäristöissä tätä moduulia ei käytetä, vaan siihen on oma win_service‑moduuli.

**enabled**: enabled-asetus hallitsee palvelun automaattista käynnistystä bootin yhteydessä. Se on valinnainen, mutta joko state tai enabled on aina määriteltävä.

**name**: servicen nimi, tietotyyppi string (str) eli merkkijono.

**state**: määrittää palvelun halutun tilan. Arvot **started** ja **stopped** ovat idempotentteja, eli ne aiheuttavat muutoksen vain, jos palvelu ei jo ole halutussa tilassa. Sen sijaan **restarted** (palvelun pysäytys ja uudelleen käynnistys) ja **reloaded** (konfiguraation uudelleen lataus ja palvelun käynnistys jos ei ole jo käynnissä) suoritetaan aina. Moduulin käyttö edellyttää, että **vähintään state tai enabled on määritelty**.

**Examples**: tässä osiossa on kuvattu Ansible‑service‑moduulin esimerkkikäyttöjä, joilla hallitaan palveluiden tilaa (käynnistys, pysäytys, uudelleenkäynnistys jne.) etäkoneilla.
Esimerkiksi ensimmäisessä kohdassa varmistetaan, että httpd‑palvelu on käynnissä: jos palvelu ei ole käynnissä, se käynnistetään, ja jos se on jo käynnissä, Ansible ei tee mitään.

🔔 **Omia mietteitä:** Kätevää, että linuxin sisällä ansiblesta löytyy näin paljon dokumentaatiota ja tätä kautta voi löytää ratkaisuja monenlaisiin tilanteisiin turvautumatta hakukoneisiin tai tekoälyyn.
    
### a) Apassi. Asenna Apache 2 käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.

Tätä tehtävää varten käytin apuna omaa dokumentaatiotani ([Toropainen, S. 2025](https://github.com/Taskuraketit/linux-course/blob/main/h3_Hello_Web_Server.md)) sekä Johanna Heinosen dokumentaatiota ([Heinonen, J. 2026](https://github.com/johannaheinonen/johanna-test-repo/blob/main/module_3.md)).

Pakettien päivitys ja asennus

```bash
sudo apt-get update
sudo apt-get install -y apache2
```
<img width="863" height="100" alt="image" src="https://github.com/user-attachments/assets/905e11ad-51ec-448d-bac1-8ac42ca335d4" />

<img width="659" height="120" alt="image" src="https://github.com/user-attachments/assets/06e8a548-11ab-4a5d-b616-e4b7fa575a5b" />

Seuraavaksi loin kotihakeistooni kansion **public_html** ja sinne webbisivutiedoston **index.html**.

```bash
cd ~
mkdir public_html
micro index.html
```
index.html:n sisältö:

<img width="367" height="242" alt="image" src="https://github.com/user-attachments/assets/1ff9d6b7-09cc-442f-a82b-09fed07a959f" />

Konfiguraatiotiedoston säätö

```cd /etc/apache2/sites-available/
micro 000-default.conf
```
Tiedoston tallennus ja sulku. Nyt sisältö näyttää tältä:

<img width="705" height="213" alt="image" src="https://github.com/user-attachments/assets/e5212793-2e47-4a75-b632-4d6c0b958c72" />

Syntaksin tarkistus

```bash
sudo apache2ctl configtest
```

-> tuloksena "Syntax OK"

<img width="1252" height="103" alt="image" src="https://github.com/user-attachments/assets/350c4a67-6112-49c2-a324-cca50f2e7368" />

Muutosten käyttöönotto

```bash
sudo systemctl reload apache2
```

localhost-sivun avaaminen selaimessa:

<img width="518" height="265" alt="image" src="https://github.com/user-attachments/assets/874c8679-1b17-4693-bf91-11feb337ddf8" />

(Note to self: sivulla näkyi aiemmin luennon aikana luomani nettisivu, mutta sivulle saa tehtyä *hard refreshin* painamalla **ctrl + shift + R**.)

Tarkistetaan vielä webbisivutiedoston oikeudet:

```bash
cd ~/public_html/
ls -l index.html
```

Tulos:

<img width="558" height="39" alt="image" src="https://github.com/user-attachments/assets/304f77aa-656f-47ab-b2db-cf4b72ec9750" />

Oikeusmääritys -rw-r--r-- 1 kertoo, että
- kyseessä tavallinen tiedosto (-)
- omistaja (samuli) saa muokata ja lukea tiedostoa (rw-)
- ryhmä (samuli) saa lukea tiedostoa (r--)
- muut saavat lukea tiedostoa (r--)


### b) Moottorix. Asenna Nginx käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia. (Muista sammuttaa Apache ensin.)

Ensin päivitin paketit ja sensin nginx:n.

```bash
sudo apt-get update
sudo apt-get -y install nginx
```

Tämän jälkeen sammutin apachen ja tarkistin sen statuksen. Apache-komennoille löytyy hyvä kertauspaketti [Linuxize-sivulta](https://linuxize.com/post/start-stop-restart-apache/).

```bash
sudo systemctl stop apache2
sudo systemctl status apache2
```

<img width="1265" height="470" alt="image" src="https://github.com/user-attachments/assets/03b8d84f-9b78-45de-97cc-8e8611cffd74" />

Myöskään localhostilla nettisivu ei enää näkynyt eli apache oli todistetusti sammutettu.

<img width="1068" height="636" alt="image" src="https://github.com/user-attachments/assets/ed6b4211-d526-4d74-80f6-e0c4d882942d" />

Palvelun tilan tarkistus

```bash
systemctl status nginx
```

Ruudulta nähdään, ettei palvelu ole käynnissä:

<img width="1188" height="221" alt="image" src="https://github.com/user-attachments/assets/7bbad54a-60f3-48ff-a7ba-d27691af62fa" />

Palvelun käynnistys:

```bash
sudo systemctl start nginx
```

Palvelun toimivuuden tarkistus: avasin selaimessa sivun http://localhost ja päivitin sivun. Näkymä muuttui seuraavanlaiseksi:

<img width="890" height="351" alt="image" src="https://github.com/user-attachments/assets/e6d539ef-e911-4a73-bc10-c597382af117" />

Statuksen tarkistus uudelleen. Näkymä on muuttunut aiemmasta.

```bash
systemctl status nginx
```

<img width="1173" height="371" alt="image" src="https://github.com/user-attachments/assets/196b53e6-a875-4239-b5ee-411d1d52d979" />

Microsoft Copilotin mukaan nginx:n oletusrootin sijainti on **/var/www/html/**. Mennään kurkkaamaan miltä siellä näyttää.

```bash
cd /etc/www/html
tree
cat index.nginx-debian.html
```

Halusin kopioida alkuperäiäsen index.nginx-debian.html-tiedoston backupiksi, mutta tätä ei pystynyt tekemään peruskäyttäjän oikeuksin joten käytin kopiointiin ja uudelleennimeämiseen sudoa, minkä jälkeen muokkasin alkuperäistä tiedostoa.

<img width="937" height="174" alt="image" src="https://github.com/user-attachments/assets/dbb08d0d-3d5e-4608-b8d0-0eb5d6772dbf" />

Tässä kohtaa huomasin, että olin kopioinut tiedoston väärän nimisenä, kenties ylikirjoittaen alkuperäisen index.html.backup-tiedoston, joka minulla **ehkä** oli Apache2:n asennuksen jäljiltä siellä. Backupin uudelleen nimeäminen informatiivisemmaksi:

```bash
sudo mv index.html.backup index.nginx-debian.html.backup
```

<img width="923" height="272" alt="image" src="https://github.com/user-attachments/assets/9703f778-330a-4e5b-bc21-a425f06493f8" />

Tiedoston muokkaus:

````bash
micro index.nginx-debian.html
````

Alkuperäinen sisältö:

<img width="792" height="468" alt="image" src="https://github.com/user-attachments/assets/a38b0b46-8a29-4d68-a8fb-c49f2d21e8ac" />

Muokkaamani sisältö:

<img width="509" height="235" alt="image" src="https://github.com/user-attachments/assets/aded16e3-ace8-4ee4-a6cd-5aad664ca238" />

Avasin selaimen ja päivitin sivun, jolloin sivu tuli näkyviin sellaisena kuin halusin.

<img width="532" height="268" alt="image" src="https://github.com/user-attachments/assets/41966bf3-8008-48be-acc4-7c37a92ccc18" />

Tässä kohtaa mieleeni juolahti, ettei tehtävänanto taida täysin toteutua: "*Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.*"

Tämä on todennettavissa esimerkiksi tarkistamalla em. tiedoston oikeudet:

````bash
ls -l -F
````

<img width="730" height="100" alt="image" src="https://github.com/user-attachments/assets/b0cd3127-7968-4721-90f8-9ab9fac1e4a0" />


Tuloksista nähdään, että **vain root-käyttäjä voi muokata tiedosto(j)a**. Muutetaanpa siis vähän suuntaa tämän homman kanssa, jotta tehtävänanto toteutuu. Tähän osioon kysyin apuja Microsoft Copilotilta, joka neuvoi luomaan sivustohakemiston kotihakemistoon, luomaan sivustokonfiguraation, ottamaan sivuston käyttöön ja tämän jälkeen testaamaan ja lataamaan nginx:n uudelleen, minkä jälkeen sivun pitäisi näkyä selaimella. 

**Hakemiston luonti**

````bash
cd ~
mkdir -p ~/nginx-site
micro ~/nginx-site/index.html
````

index.html-tiedoston sisältö:

<img width="687" height="264" alt="image" src="https://github.com/user-attachments/assets/3f4dbe24-901f-4e24-b94f-a869ad936052" />

**Konfiguraatiotiedoston luonti**

````bash
sudo micro /etc/nginx/sites-available/nginx-site
````

**Tiedoston sisältö (Copilotin ehdotus)**

<img width="430" height="278" alt="image" src="https://github.com/user-attachments/assets/836d5ae1-d610-4499-948f-f438390aabe5" />

**Sivuston käyttöönotto, symlinkin luonti**

````bash
sudo ln -s /etc/nginx/sites-available/nginx-site /etc/nginx/sites-enabled/
````

**Alkuperäisen konfiguraatiotiedoston uudelleen nimeäminen -> jätetään backupksi**

````bash
cd /etc/nginx/sites-enabled/
tree
ls -l
sudo mv default default.backup
tree
````

<img width="910" height="364" alt="image" src="https://github.com/user-attachments/assets/cfed7c28-bb09-4f16-9a10-4b427ca93db4" />

**Testaus**

````bash
sudo nginx -t
````

Tuli seuraava virheilmoitus:

<img width="1249" height="61" alt="image" src="https://github.com/user-attachments/assets/c2a361cf-7380-4d45-96d9-b1c90544c29b" />

Virheilmoitus kertoo, että konfiguraatiotiedostosta puuttuu puolipiste -> korjataan se.

````bash
cd /etc/nginx/sites-enabled/
tree
micro nginx-site
````
Lisäsin puolipisteen index.html:n perään, tallensin ja suljin tiedoston.

**Uusi testi**

````bash
sudo nginx -t
````
<img width="688" height="64" alt="image" src="https://github.com/user-attachments/assets/72d1722e-4f83-4503-be8b-cf4aa7d972a8" />

Nyt konfiguraatio toimii.

**nginx:n uudelleen lataus**

````bash
sudo systemctl reload nginx
````

Seuraavaksi avasin selaimen ja päivitin sivun. Kaikki näytti toimivan toivotulla tavalla - toistaiseksi :)

<img width="553" height="249" alt="image" src="https://github.com/user-attachments/assets/084f23c7-8996-4acb-ab7c-7bdccc20504d" />

Lopuksi todennetaan, että sivua voi muuttaa peruskäyttäjän oikeuksin.

````bash
micro ~/nginx-site/index.html
````

Tehdään sivuun pieni muokkaus:

<img width="724" height="249" alt="image" src="https://github.com/user-attachments/assets/62acf19d-7928-4768-a3a2-82abf823942d" />

Tallensin ja suljin tiedoston (ilman sudoa!)

Avasin selaimen ja päivitin sivun, jolloin tekemäni muutos näkyi heti.

<img width="612" height="278" alt="image" src="https://github.com/user-attachments/assets/0f3ef3d5-8960-44d3-9c51-1e2ce67319f5" />



### c) Automoottorix. Automatisoi Nginx asennus Ansiblella. Ylläpitäjän osuus Ansiblella riittää, itse HTML-weppisivut voi tehdä käsin.



### d) Vapaaehtoinen bonus: Osiris-T. Osiris-T asentaa Wazuhin ja tarvittavat virtuaalikoneet. Voit lähettää vihamielistä verkkoliikennettä (tcpreplay), siepata sen (suricata) ja saat tulokset suoraan dashboardille (wazuh). Enemmän alpha kuin se kreikkalainen kirjain. Mutta Oskari, Nico ja Arttu ilahtuvat, jos kokeilet. Häkämies, Saario, Mukkula 2026: Osiris-T. Häkämies etal 2026: How to Install.



### Lähteet

**Ansible-doc Service**. Luettu: 11.4.2026

**Heinonen, J.** 2026. Apache2. Luettavissa: https://github.com/johannaheinonen/johanna-test-repo/blob/main/module_3.md. Luettu: 11.4.2026.

**Karvinen, T.** 2026. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 11.4.2026.

**Panovski, D.** 3.3.2026. How to Start, Stop, and Restart Apache on Linux. Luettavissa: https://linuxize.com/post/start-stop-restart-apache/. Luettu: 11.4.2026.

**Toropainen, S. 2025.** Hello Web Server. Luettavissa: https://github.com/Taskuraketit/linux-course/blob/main/h3_Hello_Web_Server.md. Luettu: 11.4.2026.

