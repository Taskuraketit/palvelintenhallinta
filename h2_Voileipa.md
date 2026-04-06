### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.)

- **Karvinen 2026: Sudo without password**

Ansible tarvitsee root-oikeudet orjakoneeseen, mikä on toteutettavissa käyttämällä käyttäjätunnusta, joka ei tarvitse sudo-salasanaa komentojen suorittamiseen.

Tiivistetysti työvaiheet ovat:

**1. Uuden käyttäjän luonti**

```bash
sudo adduser antero   # valitse haluamasi käyttäjätunnus
sudo groupadd sudoless   # sudoless-ryhmän luonti
sudo adduser antero sudoless   # käyttäjän lisäys sudoless-ryhmään
```

**2. Takaportti siltä varalta, jos sudo menee rikki**

Avataan uusi ikkuna ja muodostetaan ssh-yhteys.

```bash
sudo -i
```

**3. sudoless-ryhmän sääntöjen luonti**

```bash
sudo visudo /etc/sudoers.d/sudoless
```

Lisätään rivi:

```bash
%sudoless ALL = (ALL) NOPASSWD: ALL
```

**4. Testaus. Lähtökohtaisesti mitä ei ole testattu, ei voida todeta olevan tehty.**

Kirjaudutaan luodulla käyttäjätunnuksella ulos ja takaisin sisään. Suoritetaan komennot (esimerkki):

```bash
ssh antero@localhost
sudo -k
sudo echo "See you at TeroKarvinen.com"
See you at TeroKarvinen.com
```

Mikäli käyttäjän salasanaa **ei kysytty**, on säännön luonti onnistunut.


- **Munroe 2006: xkcd 149: Sandwitch**

xkcd.com-sivuston sarjakuva nro 149 kuvastaa hyvin sudon syvintä olemusta: henkilö pyytää toista tekemään tälle voileivän, mihin tämä reagoi kehottamalla tekemään tätä itse voileipänsä. Sen jälkeen pyytäjä esittää saman pyynnön lisäämällä sanan "sudo" pyynnön (komennon) eteen, minkä jälkeen toinen henkilö suostuu pyyntöön kyseenalaistamatta sitä. Sudon käyttäminen (salasanalla tai ilman) antaa linux-ympäristössä laajat oikeudet toteuttaa erilaisia komentoja, mikä toisaalta mahdollistaa monipuolisen ylläpidon ja helpottaa sitä, mutta luo myös erilaisia riskejä tahattomille vahingoille.

  
- **Karvinen 2026: Passwordless Sudo with Ansible**

Artikkelissa esitellään käyttäjän luonti/lisäys sudoless-ryhmään ja luodaan ryhmälle sääntö, jotta salasanaa ei enää kysytä. Lähtötilanteessa Ansible tulisi olla toiminnassa, minkä lisäksi on suositeltavaa osata tehdä sama asia ensin **manuaalisesti** ennen automatisointia.

Lähtötilanteen tulisi näyttää tältä (esimerkki):

```bash
$ tree -F
./
├── ansible.cfg
├── hosts.ini
├── roles/
│   ├── antero/
│   │   └── tasks/
│   │       └── main.yml
│   └── world/
│       └── tasks/
│           └── main.yml
└── site.yml
```

**main.yml-tiedoston sisältö (esimerkki)**

```bash
cat roles/antero/tasks/main.yml
```
```bash
- group:
    name: "sudoless"
    state: present
- user:
    name: "antero"
    state: present
    groups: ["sudoless", "sudo", "adm"]
- authorized_key:
    user: "antero"
    key: "ssh-ed25519 U2VlIHlvdSBhdCBUZXJvS2FydmluZW4uY29tIQ== tero@example.com"
- copy:
    dest: "/etc/sudoers.d/sudoless"
    content: "%sudoless ALL = (ALL) NOPASSWD: ALL\n"
    owner: "root"
    group: "root"
    mode: "0644"
```

Ensimmäisellä ajokerralla käyttäjällä ei vielä ole sudo-oikeuksia ilman salasanaa, mutta Ansible tarvitsee sudo-oikeudet.
Tämä on ratkaistavissa lisäämällä **site.yml**-tiedoston loppuun blokki:

```bash
- hosts: all
  become: true
  roles:
    - world
    - antero
```

Komennon ajaminen

```bash
ansible-playbook site.yml
```

Lopuksi ajetaan komento

```bash
ansible-playbook site.yml --ask-become-password
```
 
- **Ansiblen sisäänrakennettu dokumentaatio ansible-doc -kommennolla.**
-- Kustakin vain
  - Johdantokappale (Usein MODULE alla, päättyy OPTIONS alkuun)
  - Nimetyt optiot selityksineen
  - Esimerkeistä (EXAMPLES) jokin helppo ja keskeinen esimerkki
    
  **'ansible-doc copy': content, dest, src; owner, group, mode;**
 
   **ansible.builtin.copy-moduulia** käytetään tiedostojen tai hakemistorakenteiden kopioimiseen paikalliselta tai etäkoneelta kohdejärjestelmälle. Moduulin avulla voidaan asettaa tiedostojärjestelmän metatietoja, kuten käyttöoikeuksia ja omistajuutta, myös silloin kun kohdetiedosto on jo olemassa. Metatietojen noutamiseen käytetään stat-moduulia ja niiden asettamiseen file-moduulia. Jos kopioitavaan tiedostoon tarvitaan muuttujien käsittelyä, tulee käyttää template-moduulia. Windows-kohteille käytetään erillistä win_copy-moduulia.

- **content** määrittää tiedoston sisällön suoraan playbookissa ilman erillistä lähdetiedostoa.
- **dest** on kohdetiedoston tai ‑hakemiston polku etäkoneella, johon tiedosto kopioidaan.
- **src** on lähdetiedoston tai ‑hakemiston polku Ansible-ohjauskoneella tai roolissa.
- **owner** määrittää tiedoston omistajakäyttäjän kohdejärjestelmässä.
- **group** määrittää tiedoston omistajaryhmän kohdejärjestelmässä.
- **mode** asettaa tiedoston käyttöoikeudet (esim. 0644).

Seuraava esimerkki kopioi paikallisen asetustiedoston kohdejärjestelmälle ja asettaa sille oikeudet ja omistajan:

```bash
- name: Kopioi konfiguraatiotiedosto palvelimelle
  copy:
    src: files/app.conf
    dest: /etc/app/app.conf
    owner: root
    group: root
    mode: "0644"
```

  **'ansible-doc apt': name, state, update_cache;**

**ansible.builtin.apt-moduuli** mahdollistaa apt-pakettien hallinnoinnin (asennus, poisto, päivitys) Linuxissa.   

- **name** on paketin nimi tai nimilista, jota halutaan hallita.
- **state** määrittää paketin tilan, kuten asennettu (present) tai poistettu (absent).
- **update_cache** päivittää APT‑pakettivaraston tiedot ennen muihin toimenpiteisiin ryhtymistä.

    Esimerkki

```bash
  - name: Asenna nginx ja päivitä pakettivarasto
  apt:
    name: nginx
    state: present
    update_cache: true
```

  **'ansible-doc file': path, recurse, src, state. owner, group, mode;**
 
**ansible.builtin.file-moduulia** käytetään tiedostojen, hakemistojen ja symbolisten linkkien sekä niiden kohteiden ominaisuuksien asettamiseen. Moduulilla voidaan myös poistaa tiedostoja, hakemistoja ja symlinkkejä. Monet muut Ansible-moduulit tukevat samoja optioita kuin file-moduuli, kuten copy, template ja assemble. Windows-järjestelmissä vastaavana moduulina käytetään win_file-moduulia.

- **path** on hallittavan tiedoston tai hakemiston polku.
- **recurse** asettaa oikeudet ja omistajuuden rekursiivisesti hakemiston sisällölle.
- **src** on lähdekohde symbolisille linkeille.
- **state** määrittää kohteen tilan, kuten file, directory, link tai absent.
- **owner** asettaa kohteen omistajakäyttäjän.
- **group** asettaa kohteen omistajaryhmän.
- **mode** asettaa käyttöoikeudet.

Esimerkki

```bash
- name: Luo hakemisto sovellukselle
  file:
    path: /opt/app
    state: directory
    owner: appuser
    group: appuser
    mode: "0755"
```
  

  **'ansible-doc user': name; create_home, comment, groups, shell, state, system;**

**ansible.builtin-user-moduulia** käytetään käyttäjien sekä käyttäjäattribuuttien hallinnointiin (käyttäjien luonti, muokkaus ja poisto). Windows-kohteisiin tulisi käyttää **ansible.windows.win_user-moduulia**

- **name** on käyttäjän nimi.
- **create_home** määrittää luodaanko käyttäjälle kotihakemisto.
- **comment** on käyttäjän kuvausteksti.
- **groups** on lista ryhmistä, joihin käyttäjä liitetään.
- **shell** on käyttäjän oletuskomentotulkki.
- **state** on käyttäjän tila, kuten *present* tai *absent*.
- **system** määrittää onko käyttäjä järjestelmäkäyttäjä.

Esimerkki

```bash
- name: Luo käyttäjä antero
  user:
    name: antero
    comment: "Sovelluskäyttäjä"
    groups: sudo
    shell: /bin/bash
    create_home: true
    state: present
```
  
  **'ansible-doc authorized_key': user, key;**

**ansible.posix.authorized_key-moduulia** käytetään julkisten SSH-avainten hallintaan käyttäjien authorized_keys‑tiedostossa. Moduuli mahdollistaa salasanattoman SSH‑kirjautumisen.

- **user** on käyttäjä, jonka authorized_keys‑tiedostoa muokataan.
- **key** on lisättävä SSH‑julkinen avain.

Esimerkki

```bash
- name: Lisää SSH-avain käyttäjälle antero
  authorized_key:
    user: antero
    key: "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... user@example.com"
```

### a) Sudoless. Tee ansiblea varten tunnus, jolla voi käyttää sudoa ilman salasanaa. Sekä ssh-kirjautuminen että sudon käyttö tulee olla ansbilea varten automatisoitu.

Uuden käyttäjän ja ryhmän luonti sekä käyttäjän lisäys kyseiseen ryhmään. Muutama hassu typo sattui, mutta oli hyvä nähdä millaisia virheilmoituksia tässä voi tulla :)

```bash
sudo adduser samans   # tämän jälkeen käyttäjän tietojen lisäys + vahvistetaan tiedot valitsemalla 'y'
sudo groupadd sudoless
sudo adduser samans sudoless
```
<img width="683" height="365" alt="image" src="https://github.com/user-attachments/assets/10dbecb2-40d3-49da-8835-72c40d7f2402" />

Seuraavaksi avasin toiseen ikkunaan uuden terminaalin ja avain root shellin sudo-oikeuksin.

```bash
ssh samuli@localhost   # kirjautuminen ilman salasanaa!
sudo -i
```
<img width="1013" height="257" alt="image" src="https://github.com/user-attachments/assets/378a7af2-c6d2-4e06-b1c2-637e5e4717b5" />

Tämän jälkeen palasin takaisin alkuperäiseen ikkunaan.

Suoritin komennon

```bash
sudo visudo /etc/sudoers.d/sudoless
```

ja lisäsin tiedostoon rivin

```bash
%sudoless ALL = (ALL) NOPASSWD: ALL
```

Tallensin ja suljin tiedoston.

Tämän jälkeen suoritin testin

```bash
ssh samans@localhost   # sudo-salasanaa kysyttiin
sudo -k   # sudo-kirjautumisen nollaus
sudo echo "Haloo haloo toimiiko tämä"
```

echo-komennolla tulostettu tuli näkyviin ilman sudo-salasanan kysymistä. Jee, toimii!


### b) Antero. Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.

Julkisen avaimen selvitys

```bash
cat ~/.ssh/id_ed25519.pub
```

Tämä antoi tuloksen:

<img width="1016" height="39" alt="image" src="https://github.com/user-attachments/assets/f7866e10-8edc-4e3f-9d04-6fb58ced5250" />

Aluksi muutin alkuperäisen main.yml-tiedoston backupiksi.

```bash
cd roles/hello/tasks/
mv main.yml main.yml.backup
micro main.yml
```

Lisäsin uuteen main.yml-tiedostoon seuraavat tiedot:

<img width="1151" height="323" alt="image" src="https://github.com/user-attachments/assets/08f46cfb-9544-47a0-992c-7d9df973ba09" />

Tallensin ja suljin tiedoston.

Tässä kohtaa tajusin, että samans-käyttäjä olisi pitänyt lisätä sudoless-ryhmän lisäksi ryhmiin adm ja sudo joten tein sen nyt.

```bash
sudo adduser samans sudo
sudo adduser samans adm
```

<img width="591" height="82" alt="image" src="https://github.com/user-attachments/assets/6d8bcf52-ce7f-4d40-9aea-f1e4bbf1f6f4" />

Tässä kohtaa hoksasin myös, että roles-kansion alla voi olla useita kansioita, jotka sisältävät tiedostoja, joiden avulla suoritetaan eri asioita. Siirsin aiemmin luomani uuden main.yml-tiedoston uuteen luomaani roles/adduser/tasks/-kansioon.

```bash
mkdir roles/adduser
cd roles/roles/adduser
mkdir tasks
cd ..
cd ..
mv roles/hello/tasks/main.yml roles/adduser/tasks
```

ja nimesin main.yml.backupin takaisin main.yml:ksi.

Seuraavaksi muokkasin main.yml-tiedostoa.

<img width="220" height="125" alt="image" src="https://github.com/user-attachments/assets/b65e8734-4882-4048-9298-6c9095dd8487" />

Tallensin ja suljin tiedoston.

Suoritin testiksi ajon

```bash
ansible-playbook site.yml
```
-> tuli virheilmoitus:

<img width="1272" height="237" alt="image" src="https://github.com/user-attachments/assets/5a8447d7-da02-4c13-958e-341e574223d3" />

Seuraavaksi suoritin ajon

```bash
ansible-playbook site.yml --ask-become-password
```

minkä jälkeen komentoriville tuli pitkä selostus sille mitä tapahtui.

<img width="1263" height="674" alt="image" src="https://github.com/user-attachments/assets/96786fc1-1dec-416c-89ae-e6a445c5daff" />

Uusi testi komennolla

```bash
ansible-playbook site.yml
```
sain virheilmoituksen:

<img width="1268" height="229" alt="image" src="https://github.com/user-attachments/assets/830f2421-c902-4434-8377-fd539a1b9845" />

Tarkistin että löytyy inventory (tässä tapauksessa hosts.ini) ja sen sisällön.

```bash
ls
micro hosts.ini
```

Kokeilin ajaa komennon pienellä muutoksella:

```bash
ansible-playbook site.yml -i hosts.ini --ask-become-password
```
...mutta sain edelleen saman virheilmoituksen.

Copilotin kehotuksesta muutin komennon muotoon

```bash
ansible-playbook site.yml -i hosts.ini -K
```
minkä jälkeen ajo onnistui.

<img width="1264" height="521" alt="image" src="https://github.com/user-attachments/assets/ba98248d-f781-44b6-ba80-a62ea202a483" />

Testiksi ajoin seuraavaksi komennon

```bash
ansible-playbook site.yml
```

ja sain virheilmoituksen:

<img width="1263" height="241" alt="image" src="https://github.com/user-attachments/assets/dcf32157-6c79-459c-a009-d46cfe603899" />

Copilotin ehdotuksesta muutin main.yml-tiedoston viimeisen rivin

mode: "0644"

muotoon

mode: "0440"

ja tallensin ja suljin tiedoston.

Tämän jälkeen ajoin kerran komennon

```bash
ansible-playbook site.yml -i hosts.ini -K
```

ja kaikki näytti tässä vaiheessa oikealta.

<img width="1263" height="500" alt="image" src="https://github.com/user-attachments/assets/2458bed6-35e6-4165-9d69-cbef70e14b0c" />

Sitten ajoin testiksi komennon

```bash
ansible-playbook site.yml
```
ja sain edelleen saman virheilmoituksen:

<img width="1267" height="201" alt="image" src="https://github.com/user-attachments/assets/b41a496b-db5a-4ba1-91bf-85a64add42d6" />

Copilotin analyysi tilanteesta:

***Miksi tämä tapahtuu?***
***Keskeinen fakta (tämä ratkaisee koko asian)***

***Salasanaton sudo koskee vain niitä käyttäjiä, jotka kuuluvat sudoless‑ryhmään.***

*Sinun tapauksessasi:*

- GNU/Linux‑istunnossa olet kirjautunut käyttäjänä samuli
- sudoless‑ryhmään kuuluu samans
- Ansible ajetaan edelleen käyttäjänä samuli
- samuli ei kuulu sudoless‑ryhmään

*➡️ sudo kysyy salasanaa → Ansible ei voi vastata → virhe.*

Tässä kohtaa pysähdyin tarkemmin miettimään tehtävänantoa ja keksin, että ilmeisesti tässä ei ole varsinainen virhe kyseessä vaan teen väärää asiaa - tehtävänä oli luoda *uusi käyttäjä* ansiblella. 

Koska *samans*-käyttäjä on jo olemassa, päätin vaihtaa /roles/adduser/tasks/main.yml-tiedostoon uuden käyttäjän **mansikka** ja muokata mode-kohtaan "0644" kuten Teron artikkelissa mainittu.

<img width="1151" height="328" alt="image" src="https://github.com/user-attachments/assets/52ac6fc8-6196-4e50-a044-2c2baab1424f" />

Tässä kohtaa päätin kokeilla Copilotin ehdotusta hosts.ini-tiedoston muokkauksesta. Nimesin alkuperäisen hosts.ini-tiedoston hosts.ini.backupiksi ja loin samaan hakemistoon uuden hosts.ini-tiedoston, jonka sisältö seuraava:

<img width="607" height="125" alt="image" src="https://github.com/user-attachments/assets/fc623699-a472-48db-9141-768bbac03ade" />

**Uusi yritys**

hosts.ini-tiedoston muokkaus

<img width="470" height="125" alt="image" src="https://github.com/user-attachments/assets/261c463a-e16c-4d48-98a4-a850dc01f0ce" />

testaus että ssh-yhteys onnistuu ilman salasanaa

```bash
ssh samans@localhost
```

<img width="1020" height="190" alt="image" src="https://github.com/user-attachments/assets/cde7d7cd-228d-4869-ae21-9578bab83ae8" />

Suoritin komennon

```bash
ansible all -i hosts.ini -m ping
```

ja sain virheilmoituksen

<img width="731" height="193" alt="image" src="https://github.com/user-attachments/assets/471a197b-f06a-4f75-8a7d-ecf243bc11c2" />

hosts.ini-tiedoston muokkaus

<img width="485" height="125" alt="image" src="https://github.com/user-attachments/assets/55893d7b-e140-4a75-b098-021f041402a1" />

Ajoin uudestaan komennon

```bash
ansible all -i hosts.ini -m ping
```

ja nyt sain erilaisen ilmoituksen kuin aiemmin

<img width="666" height="98" alt="image" src="https://github.com/user-attachments/assets/8d52ad42-c550-4270-9f80-753729c6323c" />

Ajoin komennon

```bash
ansible all -i hosts.ini -m command -a "whoami" --become
```

ja sain tulokseksi:

<img width="905" height="58" alt="image" src="https://github.com/user-attachments/assets/e7ac7880-6b77-4c8d-9ca7-edc9418b37a4" />

Ajoin komennon

```bash
ansible-playbook -i hosts.ini site.yml
```

ja sain seuraavat tulokset:

<img width="1268" height="523" alt="image" src="https://github.com/user-attachments/assets/92516f23-d9d2-46b2-87c1-19a3aebf1b1f" />

Virheilmoitus kertoo, että main.yml-tiedostossa on virhe. Korjataan se.

Lisäsin /-merkin bin-sanan eteen (roles/adduser/tasks/main.yml):

<img width="573" height="361" alt="image" src="https://github.com/user-attachments/assets/901d3d42-0d46-445f-926a-02317c4e9fea" />

Tallensin ja suljin tiedoston.

Ajoin playbookin uudelleen:

```bash
ansible-playbook -i hosts.ini site.yml
```
Tulos:

<img width="1267" height="485" alt="image" src="https://github.com/user-attachments/assets/799e9ced-3314-4b25-961a-c1412ea167a8" />

Komennolla 

```bash
ssh mansikka@localhost
```

näkyy, että käyttäjä luotiin oikein :) Jee!

<img width="1027" height="237" alt="image" src="https://github.com/user-attachments/assets/f628aaa9-891b-4e91-8206-46eb07a0019f" />

Tarkistin vielä, että mansikka-käyttäjällä on kotihakemisto

```bash
ls -ld /home/mansikka
```

<img width="643" height="56" alt="image" src="https://github.com/user-attachments/assets/02595e88-aeca-4cf6-a49f-af2ac7772434" />


### c) Package. Asenna kaksi pakettia ansiblella.

Aluksi selvitin miten asennus tapahtuu käyttäen apuna Copilotia sekä paria nettilähdettä ([Kastning, J. 16.8.2021](https://www.redhat.com/en/blog/software-packages-ansible); [Kenlon, S. 8.9.2020](https://opensource.com/article/20/9/install-packages-ansible)). Näiden avulla sain selvitettyä perusidean ja -rakenteen asennustoimenpiteille.

Copilotin ehdottamista paketeista minulla oli jo valmiiksi asennettuina ainakin htop, tree ja curl. Sen sijaan minulla ei ollut asennettuna gitiä ja net-toolsia, joten päätin valita ne.
Tämän voi testata helposti esimerkiksi tarkistamalla ohjelman versionumeron:

```bash
htop --version
tree --version
curl --version
git --version
net-tools --version
```
<img width="1261" height="327" alt="image" src="https://github.com/user-attachments/assets/95a56a16-472b-4026-a392-e153edfcf760" />

Aloitin luomalla kansion /roles/packages/tasks/ sekä sen sisälle uuden tiedoston main.yml, jonka sisällöksi kirjoitin näin:

<img width="674" height="150" alt="image" src="https://github.com/user-attachments/assets/b40c4b36-9c34-4688-be90-e71769a9583a" />

- Asennetaan paketit git ja net-tools
- käytetään apt-moduulia
- ei tee mitään jos paketit on jo asennettu

  Seuraavaksi lisäsin site.yml-tiedostoon roles-kohdan alle "packages" ja tallensin sekä suljin tiedoston.

  <img width="223" height="133" alt="image" src="https://github.com/user-attachments/assets/4af12abc-1107-46da-ac1a-0bd53f8d9c26" />

Tässä kohtaa tarkistin vielä tree-komennolla hakemistorakenteen:

<img width="374" height="340" alt="image" src="https://github.com/user-attachments/assets/21caa3ab-b4cd-416c-9353-8e88b51b0c62" />

Seuraavaksi ajoin ansible-playbookin komennolla

```bash
ansible-playbook site.yml
```

Sain vahvistuksen asennuksen läpimenosta:

<img width="1259" height="518" alt="image" src="https://github.com/user-attachments/assets/dc59b345-68bc-4ba5-b3b4-27af30d7d788" />

Tarkistin asennuksen läpimenon vielä komennoilla

```bash
git --version
sudo ifconfig   # vaatii sudon toimiakseen
```

Molemmat haut tuottivat tuloksia eli asennukset ovat menneet onnistuneesti läpi.


### d) File. Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet. Käytä oikeuksille oktaalinumeroa, esim. "0600". Kerro, mitä oikeudet ovat symbolisessa muodossa, esim. "-rwxr--r--". Selitä, mitä kukin käyttäjä saa tehdä tuolle tiedostolle.

Aloitin luomalla uuden roolin tehtävää varten.

```bash
mkdir -pt roles/filetask/tasks   # -p -> luodaan koko hakemistopolku kerralla
```

Seuraavaksi loin hakemistoon main.yml-tiedoston, jonka sisällöksi laitoin seuraavasti:

<img width="496" height="212" alt="image" src="https://github.com/user-attachments/assets/84dd18a3-34ed-4e6e-ba8d-e9bfdc79c53d" />

Seuraavaksi lisäsin site.yml-tiedostoon uuden roolin "filetask"

<img width="209" height="155" alt="image" src="https://github.com/user-attachments/assets/00ef5f47-b829-4856-909f-01d5f265a1cc" />

Tallensin ja suljin tiedoston.

Tämän jälkeen kokeilin ajaa komennon

```bash
ansible-playbook site.yml
```

...ja ilmoituksen perusteella jotain näytti tapahtuvan :D

<img width="1179" height="579" alt="image" src="https://github.com/user-attachments/assets/2e237261-b03e-445a-adb5-4be5dbff372d" />

Testiksi ajoin tämän jälkeen komennon

```bash
ls -l /tmp/ansible_test.txt
```

ja sain tulokseksi:

<img width="682" height="39" alt="image" src="https://github.com/user-attachments/assets/d2b8adfd-2b0c-42b3-b7ca-fcbfa7af5c11" />

Toimii :)


### e) Jotain muuta. Näytä esimerkki ansiblen käskystä, jota ei ole vielä käsitelty kurssilla tai kotitehtävissä. Voit ottaa jonkun muun modulin kuin apt, file, copy, user tai authorized_key. Tai voit käyttää ominaisuutta, jota ei vielä ole demonstroitu. Jos tiivistystehtävässä x on mainittu ominaisuuksia, joita ei tunneilla tai läksyissä kokeiltu, nekin kelpaavat.


Alointin luomalla uuden roolin

```bash
mkdir -p roles/lineinfile_test/tasks
```

Seuraavaksi loin hakemistoon main.yml-tiedoston, jonka sisällöksi laitoin seuraavaa:

<img width="567" height="105" alt="image" src="https://github.com/user-attachments/assets/a2824b8c-da1f-4c3a-9fcd-e88e9fed1a17" />

Tässä Ansible tarkistaa onko rivi jo olemassa ja 
- jos **riviä ei ole**, se lisätään
- jos **on**, tiedostoa ei muuteta
- jos **tiedostoa ei ole** se luodaan.

Tallensin ja suljin tiedoston.

Tämän jälkeen lisäsin site.yml-tiedostoon uuden roolin:

<img width="271" height="178" alt="image" src="https://github.com/user-attachments/assets/4e6035ce-e291-4f46-a9dc-e416e2a5d9b7" />

Tallensin ja suljin tiedoston.

Tämän jälkeen ajoin komennon

```bash
ansible-playbook site.yml
```

Sain virheilmoituksen, joka kertoo virheen olevan main.yml-tiedostossa.

<img width="1266" height="138" alt="image" src="https://github.com/user-attachments/assets/0b125bbd-469b-4a15-9d86-69de96cd4da9" />

Copilotin mukaan tämä johtuu siitä, että Ansible ei löydä lineinfile-moduulia nimellä lineinfile vaan on käytettävä nimen lisäksi collectionia: *ansible.builtin.lineinfile*

Muokkasin main.yml-tiedoston seuraavanlaiseksi:

<img width="583" height="117" alt="image" src="https://github.com/user-attachments/assets/42e80c2f-fb27-45f8-afca-b25fd4152e6a" />

Tallensin ja suljin tiedoston ja ajoin komennon

```bash
ansible-playbook site.yml
```

Sain virheilmoituksen

<img width="1259" height="130" alt="image" src="https://github.com/user-attachments/assets/872a4eb1-602c-4778-bdd0-0c81b61a8b41" />

Tässä kohtaa päätin vaihtaa toiseen moduuliin (debug).

Nimesin uudelleen lineinfile_test-kansion -> debug

```bash
mv lineinfile/ deubg/
```

ja muokkasin alkuperäisen main.yml-tiedoston sisällön seuraavanlaiseksi:

<img width="422" height="82" alt="image" src="https://github.com/user-attachments/assets/9b770df0-ba9a-46e1-a792-3086993417aa" />

Tämän jälkeen muokkasin site.yml-tiedoston sisällön seuraavanlaiseksi (lineinfile_test korvattu -> debug):

<img width="223" height="175" alt="image" src="https://github.com/user-attachments/assets/5ebc8fea-9221-446a-bd74-73200d3bcb5e" />

Tallensin ja suljin tiedoston.

Seuraavaksi ajon taas komennon

```bash
ansible-playbook site.yml
```

Sain ilmoituksen moduulin toimimisesta :)

<img width="1259" height="679" alt="image" src="https://github.com/user-attachments/assets/8aa19794-e93d-4870-9698-ed79a5095b81" />


### Lähteet

Karvinen, T. 2026. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 6.4.2026.

Karvinen, T. 2026. Passwordless Sudo with Ansible. Luettavissa: https://terokarvinen.com/passwordless-sudo-with-ansible/. Luettu: 6.4.2026.

Karvinen, T. 2026. Sudo without password. Luettavissa: https://terokarvinen.com/passwordless-sudo/. Luettu: 6.4.2026.

Kenlon, S. 8.9.2020. How to install software with Ansible. Luettavissa: https://opensource.com/article/20/9/install-packages-ansible. Luettu: 6.4.2026.

Kastning, J. 16.8.2021. How to install software packages with an Ansible playbook. Red Hat Blog. Luettavissa: https://www.redhat.com/en/blog/software-packages-ansible. Luettu: 6.4.2026.

Munroe, R. 2006. xkcd #149: Sandwich. Luettavissa: https://xkcd.com/149/. Luettu: 6.4.2026.
