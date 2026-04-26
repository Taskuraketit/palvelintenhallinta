### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Kannattaa lisätä mukaan myös jokin oma havainto, idea tai kysymys.)
#### 💻 Chacon and Straub 2014: Pro Git, 2ed: 1.3 Getting Started - What is Git?

- Git on hajautettu versionhallintajärjestelmä, joka on suunniteltu nopeaan ja luotettavaan lähdekoodin hallintaan. Toisin kuin monet vanhemmat järjestelmät, Git ei tallenna muutoksia tiedostoihin yksittäisinä eroina, vaan kokonaisina tilannekuvina (snapshots) projektista jokaisen tallennuksen yhteydessä.
- Gitin keskeisiä ominaisuuksia ovat paikallinen toiminta (suurin osa toiminnoista ei vaadi verkkoyhteyttä), korkea suorituskyky sekä tietojen eheys, joka perustuu tarkistussummiin (SHA-1-hajautusarvot). Lisäksi Git lisää pääasiassa vain uutta dataa, mikä tekee virheiden peruuttamisesta ja kokeilemisesta turvallista.
- Gitissä tiedostot voivat olla kolmessa eri tilassa: muokattu (modified), välivaiheessa (staged) tai tallennettu (committed). Näitä vastaavat projektin kolme pääaluetta: työskentelyhakemisto, staging area ja Git-hakemisto. Tämä rakenne antaa käyttäjälle tarkkaa hallintaa siitä, mitä muutoksia seuraavaan tallennukseen sisällytetään.

💡 **Omia mietteitä:** Git tuntuu olevan yksinkertaisen nerokas järjestelmä ja siitä löytyy paljon sovelluksia. Yksi näistä on GitHub ja ihmettelen miksi sitä ei käytetä Haaga-Heliassa enemmän eri kursseilla tehdyn työn dokumentointiin.
  
#### 💻 Gitin käyttö on lähinnä 'git add --all && git commit; git pull && git push'. Selitä tuon komennon jokainen osa. Käytä apuna itse valitsemiasi lähteitä ja viittaa niihin.

  - **git add --all:** git add -A on “tee kaikki oikein” ‑komento, joka huolehtii siitä, että staging area vastaa täysin työskentelyhakemiston tämänhetkistä tilaa. Se lisää uudet tiedostot staging-alueelle, päivittää muokatut tiedostot staging-alueella ja merkitsee poistetut tiedostot poistettaviksi staging-alueella. [(Git add documentation)](https://git-scm.com/docs/git-add)
  - **git commit:** git commit luo uuden version projektista tallentamalla staging area ‑alueelle valitut muutokset Git-historiaan. Commit on uusi versio projektista, on nykyisen haaran (branch) uusi kärki ja sisältää tilannekuvan staging area ‑alueesta, ei suoraan työskentelyhakemistosta. [(Git commit documentation)](https://git-scm.com/docs/git-commit)
  - **git pull:** git pull hakee muutokset etärepositoriosta ja yhdistää ne nykyiseen haaraan joko fast-forwardilla, mergellä tai rebasella. [(Git pull documentation)](https://git-scm.com/docs/git-pull)
  - **git push:** git push vie paikalliset commitit etärepositorioon ja päivittää siellä olevat haarat vastaamaan paikallista historiaa. [(Git push documentation)](https://git-scm.com/docs/git-pull)

💡 **Omia mietteitä:** Git-työnkulku auttaa hallitsemaan muutoksia vaiheittain: ensin valitaan, sitten tallennetaan, sen jälkeen synkronoidaan ja lopuksi julkaistaan. Tosielämän analogia voisi olla vaikka seuraavanlainen: ensin valitaan raportista kappaleet, jotka ovat valmiit palautettaviksi (git add). Seuraavaksi tallennetaan valitut kappaleet omaksi luonnosversioksi (git commit). Tämän jälkeen tarkistetaan muiden ryhmäläisten viimeisimmät muutokset ja yhdistetään ne omaan versioon (git pull). Lopuksi lähetetään päivitetty versio yhteiseen pilvipalveluun kaikkien käyttöön (git push).

### a) Online. Tee uusi varasto GitHubiin (tai Gitlabiin tai mihin vain vastaavaan palveluun). Varaston nimessä ja lyhyessä kuvauksessa tulee olla sana "sunshine". Aiemmin tehty varasto ei kelpaa. (Muista tehdä varastoon tiedostoja luomisvaiheessa, esim README.md ja GNU General Public License 3) Edistyneemmille voi olla hauskaa etsiä ja kokeilla jokin muu palvelu kuin Github.

Ensin kirjauduin GutHubiin omilla tunnuksillani ja valitsin oikean yläkulman valikosta "New repository". 

<img width="245" height="267" alt="image" src="https://github.com/user-attachments/assets/16503e1f-2afd-42b9-b256-cabc1fbbcbd3" /><br>
Seuraavaksi loin repositorion "Sunshine of your love" alla olevilla spekseillä: 

<img width="730" height="692" alt="image" src="https://github.com/user-attachments/assets/aa9dc820-cd0e-4d84-81b3-c2ee60d8fbaa" /><br>
Tämän jälkeen tarkistin vielä lopputuloksen:

<img width="1121" height="526" alt="image" src="https://github.com/user-attachments/assets/743defd7-001c-4a39-8a92-6a2bae6b1518" />

### b) Dolly. Kloonaa edellisessä kohdassa tehty uusi varasto itsellesi, tee muutoksia omalla koneella, puske ne palvelimelle, ja näytä, että ne ilmestyvät weppiliittymään.

**Varaston kloonaus omalle koneelle ja tarkistus, että kansio tuli luoduksi**

````bash
git clone https://github.com/Taskuraketit/sunshine-of-your-love
ls -l
````

<img width="905" height="461" alt="image" src="https://github.com/user-attachments/assets/48c44eb3-a152-43e4-918c-266101ac4bc0" /><br>
Siirryin kansioon ja tein README.md-tiedostoon muutoksia paikallisesti.

<img width="654" height="129" alt="image" src="https://github.com/user-attachments/assets/c3b48528-b89b-4795-8b5a-1108cf4a0e6b" /><br>
<img width="1058" height="127" alt="image" src="https://github.com/user-attachments/assets/5a85d9bd-85c8-4f1c-8388-3e61deec7fb1" />

**Muutosten tarkistus**

````bash
git status
````

<img width="720" height="213" alt="image" src="https://github.com/user-attachments/assets/a3a22b95-23b2-476a-9dd0-e385b2dd5ee2" /><br>
Lisätään tehdyt muutokset versionhallintaan

````bash
git add .
````

**Tehdään commit**

<img width="1067" height="98" alt="image" src="https://github.com/user-attachments/assets/5cc38c70-13c1-49c0-b98d-f4badce515ff" /><br>
**Pusketaan muutokset palvelimelle**

````bash
git pull   # ei välttämätön tässä tapauksessa
git push
````

Sain seuraavanlaisen virheilmoituksen:

<img width="956" height="152" alt="image" src="https://github.com/user-attachments/assets/e21d7560-7eee-4f6e-862b-80eb089206af" /><br>
Microsoft Copilot antoi virheilmoitukselle seuraavanlaisen analyysin:

> Virhe johtuu siitä, että GitHub ei enää hyväksy käyttäjätunnus–salasana‑tunnistautumista Git‑komennoissa (kuten git push).
> Vaikka salasana olisi ollut oikein, GitHub hylkää sen automaattisesti turvallisuussyistä.
> Oikea tapa tunnistautua on käyttää salasanan sijasta GitHubin Personal Access Tokenia tai SSH‑avaimia.

**Tokenin luonti Microsoft Copilotin ohjeiden mukaan**

Valikon kautta asetuksiin (Settings) 

<img width="251" height="586" alt="image" src="https://github.com/user-attachments/assets/7473cd63-b3bb-4093-a10e-ac305fd501ac" /><br>
Developer Settings -> Personal access tokens -> Tokens (classic) -> Generate new token -> Generate new token (classic)

<img width="1353" height="605" alt="image" src="https://github.com/user-attachments/assets/cf87b771-d6d0-4c0b-be20-09e01514e37d" /><br>
Tokenin asetukset:

<img width="1003" height="503" alt="image" src="https://github.com/user-attachments/assets/84aebd1d-bdb8-4719-b0d0-00c289fdef74" />

Sain ilmoituksen tokenin luonnin onnistumisesta ja tallensin sen salasanapankkiini.

Seuraavaksi tarkistin vielä gitin statuksen

````bash
git status
````

<img width="558" height="130" alt="image" src="https://github.com/user-attachments/assets/ffedc9ae-e4ff-4c7c-9950-f8a3e11130d8" /><br>
Kuvassa näkyy, ettei uutta commitoitavaa ole ja että yksi commit odottaa siirtoa.

Muutosten pusku palvelimelle:

````bash
git push
````

Tämän jälkeen syötin käyttäjätunnukseni sekä äsken luomanin tokenin. Tämän jälkeen siirto onnistui.

<img width="688" height="249" alt="image" src="https://github.com/user-attachments/assets/9ff9c1d1-91ba-47dc-bef5-85c414edb987" /><br>
Muutosten tarkistus webbiliittymästä:

<img width="1140" height="432" alt="image" src="https://github.com/user-attachments/assets/979895f6-e29a-4e55-86bd-4e18ecbf49a0" />

### c) Doh! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia.

Tässä kohtaa README.md-tiedoston poistaminen olisi typerää, joten mennään sillä.

````bash
ls -l
rm README.md
git status
git reset --hard
git status
````

<img width="720" height="473" alt="image" src="https://github.com/user-attachments/assets/f1f02c17-340d-4b7a-a0da-d50a49f245d5" />

Kuvasta nähdään, että resetointi onnistui. Todennetaan vielä webbiliittymässä, ettei README.md ole deletoitu.

<img width="1186" height="653" alt="image" src="https://github.com/user-attachments/assets/703e150b-a7e3-45b1-a4f6-53ef8a00b18f" />

**git reset --hard palauttaa myös poistetun README-tiedoston, jos poistoa ei ole vielä committoitu. Komento palauttaa työpuun viimeisimmän commitin tilaan ja poistaa kaikki committoimattomat muutokset pysyvästi. Committoituja poistoja resetointi ei enää peru.**

### d) Tukki. Tarkastele ja selitä varastosi lokia. Näytä myös, mitä muutoksia tiedostoihin on tehty. Tarkista, että nimesi ja sähköpostiosoitteesi näkyy haluamallasi tavalla ja korjaa tarvittaessa.

Tässä kohtaa hyödynnetty speksejä Gitin nettisivuilta [(Git-log documentation)](https://git-scm.com/docs/git-log).

Lokien tarkistus

````bash
git log
````

<img width="883" height="255" alt="image" src="https://github.com/user-attachments/assets/941298eb-7e6b-46da-a198-c1fe1d870c51" /><br>

````bash
git show
````

<img width="1041" height="324" alt="image" src="https://github.com/user-attachments/assets/bb8045cc-eb41-4f03-aab8-9490541b0c8b" /><br>
Yllä olevasta kuvasta näkyy, että README.md-tiedostoon on lisätty tekstirivi ("Tätä tiedostoa on...")

Tiiviimpi yhteenveto:

````bash
git show --stat
````

<img width="879" height="191" alt="image" src="https://github.com/user-attachments/assets/dc3ef464-9cb9-4f09-9641-26b36a0908a5" /><br>
Aiemmasta jo näkyi, että nimeni ja sähköpostiosoitteeni olivat oikein. Tehdään vielä oppimismielessä tarkistus ja sähköpostiosoitteen muutos henkilökohtaisesta sähköpostiosoitteesta Haaga-Helian sähköpostiosoitteeksi.

````bash
git config --global user.name
git config --global user.email
git config --global user.email   # tämän perään koulun sähköposiosoite tuplahipsujen sisällä
````

<img width="1149" height="157" alt="image" src="https://github.com/user-attachments/assets/55876e07-2d98-4ed2-bf6d-998f5d38d788" />


### e) Gitanbile. Laita Ansible-kansio versionhallintaan. Tee jokin muutos, aja ansiblella, tallenna versio (commit).

Luodaan projektikansioon hakemisto ansiblea varten, tarkistetaan status ja commitoidaan tehdyt muutokset.

````bash
mkdir gitanible
ls -l
git status
git add gitanible/
git commit -m "Lisätty gitanible-kansio versionhallintaan"
````

<img width="1022" height="351" alt="image" src="https://github.com/user-attachments/assets/58e5d9ae-dfb9-4af9-abcb-3c78c1905525" /><br>

Näyttää siltä, ettei olisi mitään muutoksia. Microsoft Copilot tarjosi tähän seuraavanlaisen selityksen:

> **Git ei versionhallinnoi tyhjiä hakemistoja.**
> - gitanible/ on tyhjä
> - Git ei näe siinä mitään seurattavaa
> - Siksi git status pysyy “clean”‑tilassa
> - Ja git commit ei tee mitään
>
> Tämä on odotettu ja normaali Gitin toiminta.

Luodaan projektikansioon tarvittavat hilavitkuttimet ansiblen käyttöä varten.

````
cd ~
cd sunshine-of-your-love
tree
cd gitanible
mkdir roles
micro site.yml
micro hosts.ini
micro ansible.cfg
<roles->> KESKEN
````

site.yml-tiedoston sisältö

<img width="234" height="111" alt="image" src="https://github.com/user-attachments/assets/d51faffe-86f5-414a-be18-c8d7d4caa9fb" /><br>
hosts.ini-tiedoston sisältö

<img width="505" height="114" alt="image" src="https://github.com/user-attachments/assets/1bb6a388-c30a-446e-a199-a1639319d870" /><br>

ansible.cfg-tiedoston sisältö

<img width="287" height="50" alt="image" src="https://github.com/user-attachments/assets/3a8a7353-a5b3-44bd-b797-88e35422a14d" />

Loin tarpeellisen kansiorakenteen ja tiedoston menemällä roles/-kansioon ja ajamalla komennon

````bash
ansible-galaxy init gitanible
````

<img width="906" height="461" alt="image" src="https://github.com/user-attachments/assets/1d2c583e-3d2d-4380-9c2b-a8bf2c8f7d77" /><br>

main.yml-tiedoston sisältö

<img width="406" height="71" alt="image" src="https://github.com/user-attachments/assets/8961b661-efc7-46aa-bf1f-e2ea92d056d9" />

Ajetaan site.yml gitanible-kansiossa

````bash
ansible-playbook site.yml
````

<img width="1149" height="306" alt="image" src="https://github.com/user-attachments/assets/a4d7fd28-49a4-430f-8110-b7cca7a45606" />

Kuvasta nähdään, että ansible toimii. Todennetaan, että ansible-kansio ei vielä ole versionhallinnassa. Tehdään tarvittavat toimenpiteet.

````bash
git add gitanible/
git status
git commit -m "Lisätty ansible-kansio versionhallintaan"
````

<img width="1018" height="652" alt="image" src="https://github.com/user-attachments/assets/8a167062-b2f4-41d5-bbc2-9dd1f07a9cd5" />

Kuvasta nähdään, että nyt ansible-kansio on versionhallinnassa.

Tehdään muutos main.yml-tiedostoon.

````bash
micro gitanible/roles/gitanible/tasks/main.yml
````

<img width="876" height="80" alt="image" src="https://github.com/user-attachments/assets/e2cae281-283e-40ad-ab1a-ac2ae156df91" /><br>

Tarkistetaan muutokset

````bash
git status
````

<img width="716" height="228" alt="image" src="https://github.com/user-attachments/assets/ffd5603c-f2d2-4de0-93d0-43cfb03728d4" />

Kuvasta nähdään, että main.yml-tiedostoon on tehty muutos, jota ei ole commitoitu. Ajetaan seuraavaksi playbook ennen commitia:

````bash
cd gitanible
ansible-playbook site.yml
cd ..
````

<img width="1144" height="343" alt="image" src="https://github.com/user-attachments/assets/40819f09-ef9e-4932-9263-38922a6eb12b" />

Tallennetaan muutos versionhallintaan.

````bash
git add gitanible/
git commit -m "Päivitetty Ansible-roolin task ja ajettu playbook"
git push
````

Tällä kertaa git push ei toiminutkaan odotetusti. 

<img width="1111" height="219" alt="image" src="https://github.com/user-attachments/assets/afd63b95-c15c-46d7-8f05-fd36bfea3cb0" />

Microsoft Copilotin mukaan ongelma johtuu todennäköisesti siitä, että kirjotin tokenin väärin. Kokeillaan siis uudelleen ja tällä kertaa erityisellä tarkkuudella.

Edelleen tuli sama error: <img width="968" height="215" alt="image" src="https://github.com/user-attachments/assets/20bb0328-7380-4702-be04-e69624a913cb" />


### f) Hae pari projektiin Moodlen keskustelusta. (Tästä alakohdasta f ei tarvitse tehdä vaiheittaista teknistä raporttia, riittää kun toteat, että pari on hankittu.)

### g) Vapaaehtoinen: Se toinen järjestelmä: kokeile Gittiä eri käyttöjärjestelmällä kuin sillä, millä teit muut harjoitukset. Selitä niin, että kyseistä järjestelmää osaamatonkin onnistuu. Mahdollisuuksia on runsaasti: Debian, Fedora, Windows, OSX...

### h) Vapaaehtoinen: yhteistyötä: anna kaverillesi (tai alter egollesi) oikeus kirjoittaa varastoosi (commit access). Tehkää molemmat muutoksia varastoon gitillä.

### Lähteet:

**git. s.a.** Git-add documentation. Luettavissa: https://git-scm.com/docs/git-add. Luettu: 26.4.2026. 



**Karvinen, T. 2026.** Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 26.4.2026.


