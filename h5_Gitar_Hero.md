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

### c) Doh! Tee tyhmä muutos gittiin, älä tee commit:tia. Tuhoa huonot muutokset ‘git reset --hard’. Huomaa, että tässä toiminnossa ei ole peruutusnappia.

### d) Tukki. Tarkastele ja selitä varastosi lokia. Näytä myös, mitä muutoksia tiedostoihin on tehty. Tarkista, että nimesi ja sähköpostiosoitteesi näkyy haluamallasi tavalla ja korjaa tarvittaessa.

### e) Gitanbile. Laita Ansible-kansio versionhallintaan. Tee jokin muutos, aja ansiblella, tallenna versio (commit).

### f) Hae pari projektiin Moodlen keskustelusta. (Tästä alakohdasta f ei tarvitse tehdä vaiheittaista teknistä raporttia, riittää kun toteat, että pari on hankittu.)

### g) Vapaaehtoinen: Se toinen järjestelmä: kokeile Gittiä eri käyttöjärjestelmällä kuin sillä, millä teit muut harjoitukset. Selitä niin, että kyseistä järjestelmää osaamatonkin onnistuu. Mahdollisuuksia on runsaasti: Debian, Fedora, Windows, OSX...

### h) Vapaaehtoinen: yhteistyötä: anna kaverillesi (tai alter egollesi) oikeus kirjoittaa varastoosi (commit access). Tehkää molemmat muutoksia varastoon gitillä.

### Lähteet:

**git. s.a.** Git-add documentation. Luettavissa: https://git-scm.com/docs/git-add. Luettu: 26.4.2026. 



**Karvinen, T. 2026.** Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 26.4.2026.


