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


### c) Package. Asenna kaksi pakettia ansiblella.

### d) File. Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet. Käytä oikeuksille oktaalinumeroa, esim. "0600". Kerro, mitä oikeudet ovat symbolisessa muodossa, esim. "-rwxr--r--". Selitä, mitä kukin käyttäjä saa tehdä tuolle tiedostolle.

### e) Jotain muuta. Näytä esimerkki ansiblen käskystä, jota ei ole vielä käsitelty kurssilla tai kotitehtävissä. Voit ottaa jonkun muun modulin kuin apt, file, copy, user tai authorized_key. Tai voit käyttää ominaisuutta, jota ei vielä ole demonstroitu. Jos tiivistystehtävässä x on mainittu ominaisuuksia, joita ei tunneilla tai läksyissä kokeiltu, nekin kelpaavat.

### Lähteet


