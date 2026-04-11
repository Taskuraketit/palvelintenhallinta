### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.)
#### **Karvinen 2026: Apache installed with Ansible - quick notes**

Ansiblen avulla voidaan asentaa Apache 2 tai muu webbipalvelin automaattisesti. Webbisivun saa näkymään localhostilla ja sitä voi muokata normaalikäyttäjän oikeuksin. 

Vaiheet lyhyesti ovat
1) **packagen asennus** (sudo apt-get install apache2)
2) **tarvittavien tiedostojen muokkaus** (sudoedit /etc/apache2/...)
3) **servicen käynnistys eli kansankielellä demonin potkaisu** (sudo systemctl restart apache2)

Artikkelissa löytyy myös esimerkkisisällöt hakemistorakenteeseen **roles/apache2/** sekä tiedostoihin **tasks/main.yml**, **handlers/main.yml* sekä **files/example.com.conf**.

**Omia mietteitä:**
- webbipalvelimen ja webbisivun pystytys ovat keskeisiä taitoja linux-ympäristön ylläpidossa, koska kyseessä on yksi linuxin perustoiminnoista
- jos aikoo operoida enemmänkin linuxin kanssa, on tämä taito hyvä osata suoraan selkärangasta -> harjoitus tekee mestarin tässäkin!

#### **Ansible Community Documentation: Handlers: running operations on change**
  - Handlers: running operations on change (johdantokappale pääotsikon alta)
  - Notifying handlers

Handlerit An­siblessa ovat tehtäviä, jotka suoritetaan vain silloin, kun jokin toinen tehtävä aiheuttaa muutoksen kohdejärjestelmässä (notify). Ne ratkaisevat tilanteen, jossa tietty toimenpide (esim. palvelun uudelleenkäynnistys) on tarpeen vain muutoksen jälkeen, ei jokaisella ajokerralla.

**Omia mietteitä:**
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

    
#### **'ansible-doc service':**
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

    
### a) Apassi. Asenna Apache 2 käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.

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



### c) Automoottorix. Automatisoi Nginx asennus Ansiblella. Ylläpitäjän osuus Ansiblella riittää, itse HTML-weppisivut voi tehdä käsin.



### d) Vapaaehtoinen bonus: Osiris-T. Osiris-T asentaa Wazuhin ja tarvittavat virtuaalikoneet. Voit lähettää vihamielistä verkkoliikennettä (tcpreplay), siepata sen (suricata) ja saat tulokset suoraan dashboardille (wazuh). Enemmän alpha kuin se kreikkalainen kirjain. Mutta Oskari, Nico ja Arttu ilahtuvat, jos kokeilet. Häkämies, Saario, Mukkula 2026: Osiris-T. Häkämies etal 2026: How to Install.



### Lähteet

Ansible-doc Service. Luettu: 11.4.2026

Karvinen, T. 2026. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 11.4.2026.

