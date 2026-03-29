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

# a) Sshecrets. Asenna SSH-demoni ja testaa se kirjautumalla SSH:lla.

# b) Pubkey. Automatisoi ssh-kirjautuminen julkisella avaimella.

# c) Hei Ansible. Tee hei maailma ansiblella ja kokeile sitä SSH:n yli.

# d) Vapaaehtoinen bonus, vaikea: kokeile Ansiblella jokin näista asetuksista: paketin asennus, asetustiedosto /etc/ alle, käynnistä jokin demoni, tee uusi käyttäjä. Tarvitset todennäköisesti sudoa, become: true.

# Lähteet
**Karvinen, T.** s.a. Hello Ansible. Luettavissa: https://terokarvinen.com/hello-ansible/. Luettu: 29.3.2026

**Karvinen, T.** s.a. Palvelinten hallinta. Luettavissa: https://terokarvinen.com/palvelinten-hallinta/. Luettu: 29.3.2026.

**Karvinen, T.** s.a. SSH public key - Login without password. Luettavissa: https://terokarvinen.com/ssh-public-key-login-without-password/. Luettu: 29.3.2026.

**Linux.fi**. s.a. SSH. Luettavissa: https://www.linux.fi/wiki/SSH. Luettu: 29.3.2026.

**Toropainen, S.** 2025. h1 Oma Linux. Luettavissa: https://github.com/Taskuraketit/linux-course/blob/main/h1_Oma_Linux.md. Luettu: 29.3.2026
