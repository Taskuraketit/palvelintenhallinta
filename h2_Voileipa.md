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
    - 'ansible-doc copy': content, dest, src; owner, group, mode;
 
   **ansible.builtin.copy-moduulia** käytetään tiedostojen tai hakemistorakenteiden kopioimiseen paikalliselta tai etäkoneelta kohdejärjestelmälle. Moduulin avulla voidaan asettaa tiedostojärjestelmän metatietoja, kuten käyttöoikeuksia ja omistajuutta, myös silloin kun kohdetiedosto on jo olemassa. Metatietojen noutamiseen käytetään stat-moduulia ja niiden asettamiseen file-moduulia. Jos kopioitavaan tiedostoon tarvitaan muuttujien käsittelyä, tulee käyttää template-moduulia. Windows-kohteille käytetään erillistä win_copy-moduulia.

  -  
      
    - 'ansible-doc apt': name, state, update_cache;

  **ansible.builtin.apt-moduuli** mahdollistaa apt-pakettien hallinnoinnin Linuxissa.   

    - 'ansible-doc file': path, recurse, src, state. owner, group, mode;
 
  **ansible.builtin.file-moduulia** käytetään tiedostojen, hakemistojen ja symbolisten linkkien sekä niiden kohteiden ominaisuuksien asettamiseen. Moduulilla voidaan myös poistaa tiedostoja, hakemistoja ja symlinkkejä. Monet muut Ansible-moduulit tukevat samoja optioita kuin file-moduuli, kuten copy, template ja assemble. Windows-järjestelmissä vastaavana moduulina käytetään win_file-moduulia.

    - 'ansible-doc user': name; create_home, comment, groups, shell, state, system;

**ansible.builtin-user-moduulia** käytetään käyttäjien sekä käyttäjäattribuuttien hallinnointiin. Windows-kohteisiin tulisi käyttää **ansible.windows.win_user-moduulia**
  
    - 'ansible-doc authorized_key': user, key;

**ansible.posix.authorized_key-moduulia** käytetään SSH-avaintojen lisäämiseen ja poistamiseen tietyiltä käyttäjiltä.
      
### a) Sudoless. Tee ansiblea varten tunnus, jolla voi käyttää sudoa ilman salasanaa. Sekä ssh-kirjautuminen että sudon käyttö tulee olla ansbilea varten automatisoitu.

### b) Antero. Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.

### c) Package. Asenna kaksi pakettia ansiblella.

### d) File. Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet. Käytä oikeuksille oktaalinumeroa, esim. "0600". Kerro, mitä oikeudet ovat symbolisessa muodossa, esim. "-rwxr--r--". Selitä, mitä kukin käyttäjä saa tehdä tuolle tiedostolle.

### e) Jotain muuta. Näytä esimerkki ansiblen käskystä, jota ei ole vielä käsitelty kurssilla tai kotitehtävissä. Voit ottaa jonkun muun modulin kuin apt, file, copy, user tai authorized_key. Tai voit käyttää ominaisuutta, jota ei vielä ole demonstroitu. Jos tiivistystehtävässä x on mainittu ominaisuuksia, joita ei tunneilla tai läksyissä kokeiltu, nekin kelpaavat.

### Lähteet


