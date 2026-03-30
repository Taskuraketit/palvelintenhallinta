# x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.) 
## Karvinen 2026: SSH public key - Login without password
- SSH (*Secure Shell*) on etäkäyttöohjelmisto, jolla voidaan ottaa salattuja yhteyksiä järjestelmästä toiseen. Tänä päivänä se on johtava ratkaisu turvalliseen kirjautumiseen palvelimille.
- Nykyään SSH:ta käytetään useissa palveluissa kuten git, rsync ja ansible.
- Public key eli julkinen avain helpottaa kirjautumista kun ei tarvitse kirjoittaa salasanaa jokaista toimintoa tai siirtymistä varten. Oikein käytettynä se myös auttaa parantamaan palvelimen tietoturvaa.

**SSH:n käyttö lyhyesti**

> $ sudo apt-get update   # asennuspakettien päivitys
> 
> $ sudo apt-get -y install ssh   ## ssh:n asentaminen
> 
> $ sudo systemctl enable --now ssh   # SSH-demonin startti
>
> 
> $ ssh localhost   # SSH-yhteyden muodostaminen omaan koneeseen
> 
> $ w   # komennolla näkee omat kirjautumiset
> 
> $ remote$ exit   # komento sulkee SSH-istunnon
> 
>
> $ ssh-keygen   # 3 x enter -> hyväksytään oletusasetukset
> 
> - luo julkisen avaimen → ~/.ssh/id_rsa.pub (tai id_ed25519.pub)
> - luo yksityisen avaimen → ~/.ssh/id_rsa (tai id_ed25519) => **TÄTÄ EI SAA JAKAA!**
> - tallentaa ne käyttäjän kotihakemiston .ssh‑hakemistoon
>
> $ ssh-copy-id localhost   # julkinen avain kopioituu omalle koneelle
> 
> $ ssh localhost   # jos kirjautuminen onnistui ilman salasanaa, kaikki ok!

**Ongelmanratkaisu**
> **Asiakaskone**
> 
> $ ssh -v localhost   # komento yrittää muodostaa SSH‑yhteyden localhostiin ja näyttää yksityiskohtaisesti kaikki vaiheet
>
> **Serveri**
> 
> $ sudo journalctl --follow   # komento näyttää reaaliaikaisesti järjestelmän lokiviestejä
> 
> $ sudo systemctl status ssh   # komento näyttää SSH‑palvelimen tilan

## Karvinen 2026: Hello Ansible
- Ansible on konfiguraationhallintatyökalu, jonka avulla käyttäjä voi
  - kirjoittaa infraa koodina (IaC, *infrastructure as a code*)
  - hallita useita työasemia yhden keskuskoneen kautta
- Ansbile toimii SSH-yhteyden kautta, joten orjakoneella pitää olla SSH-demoni ja Python asennettuna
- Monivaiheinen asennus- ja testauspatteristo on kuvattu Tero Karvisen artikkelissa "Hello Ansible" (ks. lähdeluettelo)

# Linuxin asennus virtuaalikoneelle

Kurssin ensimmäisen luennon aikana huomasin, etten muistanut viime syksyllä Linux-palvelimet-kurssilla luomani virtuaalisen Debian-koneen kirjautumissalasanaa (enkä arvatenkaan ollut tallentanut sitä salasanapankkiini), joten päädyin asentamaan uuden virtuaalisen Debian-koneen. Apuja tähän sain sekä omasta dokumentaatiostani (Toropainen, S. 2025) sekä Johanna Heinosen ohjeesta (Heinonen, J. s.a.). Muilta osin asennustoimenpiteet menivät pitkälti samalla tavalla kuin aiemmalla kurssilla, mutta Debianin iso-versio oli tällä välin vaihtunut 13.0.0 -> 13.4.0. 

Asennuksia varten suoritin ensin komennot
> $ sudo apt update && sudo apt full-upgrade -y
>
...sekä Copilotin suosituksesta myös
> $ sudo apt install build-essential dkms linux-headers-$(uname -r)
>
...mutta jälkikäteen en ole varma tarvitsenko todella näitä työkaluja. Päätin kuitenkin antaa niiden olla.

Seuraavaksi asensin muutaman hyödyllisen ohjelman
> $ sudo apt-get install -y micro glances htop fzf
>
...ja perustin GitHubiin uuden repositorion *palvelintenhalinta*.

# a) Sshecrets. Asenna SSH-demoni ja testaa se kirjautumalla SSH:lla.
Aloitin syöttämällä seuraavat komennot
> $ sudo apt-hget update   # asennuspakettien päivitys
> 
> $ sudo apt-get -y install ssh   # ssh:n asennus -> asentui normaalisti kysymättä mitään
> 
> $ sudo systemctl enable --now ssh   # ssh:n käyttöönotto
>
Seuraavaksi syötin komennon:
> $ ssh localhost
jolloin komentoriville ilmestyi teksti:
> 
> The authenticity of host 'localhost (::1)' can't be established.

Minun piti vahvistaa yhteyden muostaminen (*Are you sure you want to continue connecting (yes/no/[fingerprint])?*)
> 
-> valitsin "yes"

Tämän jälkeen syötin komennon
> $ ssh localhost
> 
jolloin SSH-yhteys muodostui.

Kirjauduin ulos SSH-yhteydestä komennolla
> $ exit

<img width="1013" height="307" alt="image" src="https://github.com/user-attachments/assets/d334b557-6579-48c5-bc50-030a3d4d8bb6" />


# b) Pubkey. Automatisoi ssh-kirjautuminen julkisella avaimella.

Aloitin muodostamalla SSH-yhteyden komennolla
> $ ssh localhost
>
SSH-yhteys muodostui.

Loin SSH-avaimet komennolla
> $ ssh-keygen
>
Kopioin avaimet komennolla
> $ ssh-copy-id localhost
>
Testasin SSH-kirjautumista komennolla
> $ ssh localhost
>
jolloin yhteys muodostui salasanaa kysymättä.

Poistuin SSH-yhteydestä komennolla
> $ exit
>
<img width="1265" height="435" alt="image" src="https://github.com/user-attachments/assets/e220e55d-2974-407d-afd9-f18b96e64b71" />


# c) Hei Ansible. Tee hei maailma ansiblella ja kokeile sitä SSH:n yli.

Salasanattoman kirjautumisen testi

> $ ssh localhost
>

<img width="804" height="295" alt="image" src="https://github.com/user-attachments/assets/78027077-98d2-42b6-8b5c-f8a29c958e5c" />

-> toimii

Asennuspakettien päivitys

> $ sudo apt-get update
>

<img width="808" height="456" alt="image" src="https://github.com/user-attachments/assets/d82500d8-416e-463a-aed5-b6141d35a18e" />

Tarvittavien ohjelmien asennus

> $ sudo apt-get install ansible micro bash-completion tree
>

<img width="806" height="455" alt="image" src="https://github.com/user-attachments/assets/0ef6c75f-dd8f-41dd-abf7-f3d0cf8f8723" />

Ansible-kansion luonti

> $ cd
>
> $ mkdir ansible/
>
> $ cd ansible
>

<img width="402" height="104" alt="image" src="https://github.com/user-attachments/assets/eab5d3bc-161f-4d75-a8f7-2bb114da3adf" />

Hosts.ini-tiedoston luonti

> $ micro hosts.ini
>
> (tiedoston muokkaus)
>
> $ cat hosts.ini
>

<img width="473" height="93" alt="image" src="https://github.com/user-attachments/assets/a0e838be-615d-474e-977d-58414c7b4b3f" />

Ansible-testi

> $ ansible all -a 'uptime' -i hosts.ini
>

<img width="1265" height="142" alt="image" src="https://github.com/user-attachments/assets/8c5abc22-4e52-40e7-8c16-bfd7916d79d5" />

Hosts.ini-tiedoston muokkaus ja uusi testi

> $ micro hosts.ini
>
> (tiedoston muokkaus)
>
> $ cat hosts.ini
>
> $ ansible all -a 'uptime' hosts.ini
>

<img width="692" height="198" alt="image" src="https://github.com/user-attachments/assets/21d9b056-86fa-472e-a994-f13a7d4a2ee3" />

ansible.cfg-tiedoston luonti ja uusi testi

> $ micro ansible.cfg
>
> (tiedoston muokkaus)
>
> $ cat ansible.cfg
>
> $ ansible all -a "uptime"
>

<img width="649" height="170" alt="image" src="https://github.com/user-attachments/assets/4f930250-c0ba-4c3a-85a7-9f4699e20481" />

site.yml-tiedoston luonti ja testaus

> $ micro site.yml
>
> (tiedoston muokkaus)
> 
> $ cat site.yml
>
> $ ansible-playbook site.yml

<img width="1265" height="306" alt="image" src="https://github.com/user-attachments/assets/5b98842c-bc95-4c58-b38d-7c2cec9eac59" />

Roles/hello/task-kansion ja main.yml-tiedoston luonti

> $ mkdir -p roles/hello/tasks/
>
> $ micro roles/hello/tasks/main.yml
>
> $ cat roles/hello/tasks/main.yml
>

Tuli virheilmoitus, koska dest ja content ovat väärässä kohdassa

> $ ansible-playbook site.yml
>

<img width="616" height="150" alt="image" src="https://github.com/user-attachments/assets/b008568a-8c4d-4775-8252-5abc6ba35c49" />

Tarvittavat muokkaukset ja testi

> $ micro roles/hello/tasks/main.yml
> 
> (tiedoston muokkaus)
> 
> $ cat roles/hello/tasks/main.yml
>

<img width="1269" height="367" alt="image" src="https://github.com/user-attachments/assets/96aa46e7-c157-4e29-a1af-d1d2103cb741" />

Todennus, että ansible toimii

> $ ssh localhost 'cat /tmp/hello-ansible'
>

<img width="717" height="61" alt="image" src="https://github.com/user-attachments/assets/cf76c679-ad9c-416a-adaf-bd467ce19be4" />

Vielä tarvittavat muokkaukset, jotta *ansible-playbook*-komennosta tulee informatiivisempi

> $ micro ansible.cfg
>
> (tiedoston muokkaus)
> 
> $ cat ansible-playbook site.yml
>

<img width="1265" height="395" alt="image" src="https://github.com/user-attachments/assets/80e7c29f-77a7-4f2c-8c0e-623c6dd516ed" />

Lopputulema

> $ tree -F
>
> $ head -1000 ansible.cfg hosts.ini site.yml roles/hello/tasks/main.yml
>

<img width="1011" height="639" alt="image" src="https://github.com/user-attachments/assets/38a7de33-1387-474a-a999-3820d2361543" />

**Jes, kaikki toimii kuten pitääkin :) Sanoisin että tämä homma meni jopa hieman yllättävän sujuvasti. Ongelmanratkaisukin kävi näppärästi omatoimisesti.**

# d) Vapaaehtoinen bonus, vaikea: kokeile Ansiblella jokin näista asetuksista: paketin asennus, asetustiedosto /etc/ alle, käynnistä jokin demoni, tee uusi käyttäjä. Tarvitset todennäköisesti sudoa, become: true.

# Lähteet
**Heinonen, J.** s.a. How to Install Linux to Virtualbox? Luettavissa: https://github.com/johannaheinonen/johanna-test-repo/blob/main/module_1.md. Luettu: 29.3.2026.

**Karvinen, T.** 2026. Hello Ansible. Luettavissa: https://terokarvinen.com/hello-ansible/. Luettu: 29.3.2026

**Karvinen, T.** s.a. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 29.3.2026.

**Karvinen, T.** 2026. SSH public key - Login without password. Luettavissa: https://terokarvinen.com/ssh-public-key-login-without-password/. Luettu: 29.3.2026.

**Linux.fi**. s.a. SSH. Luettavissa: https://www.linux.fi/wiki/SSH. Luettu: 29.3.2026.

**Toropainen, S.** 2025. h1 Oma Linux. Luettavissa: https://github.com/Taskuraketit/linux-course/blob/main/h1_Oma_Linux.md. Luettu: 29.3.2026
