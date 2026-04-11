### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.)
- Karvinen 2026: Apache installed with Ansible - quick notes

Ansiblen avulla voidaan asentaa Apache 2 tai muu webbipalvelin automaattisesti. Webbisivun saa näkymään localhostilla ja sitä voi muokata normaalikäyttäjän oikeuksin. 

Vaiheet lyhyesti ovat
1) **packagen asennus** (sudo apt-get install apache2)
2) **tarvittavien tiedostojen muokkaus** (sudoedit /etc/apache2/...)
3) **servicen käynnistys eli kansankielellä demonin potkaisu** (sudo systemctl restart apache2)

Artikkelissa löytyy myös esimerkkisisällöt hakemistorakenteeseen roles/apache2/ sekä tiedostoihin tasks/main.yml, handlers/main.yml sekä files/example.com.conf.

- Ansible Community Documentation: Handlers: running operations on change
  - Handlers: running operations on change (johdantokappale pääotsikon alta)
  - Notifying handlers
- 'ansible-doc service':
  - johdantokappale (MODULE alta)
  - enabled
  - name
  - state
  - EXAMPLES
    
### a) Apassi. Asenna Apache 2 käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia.



### b) Moottorix. Asenna Nginx käsin. Weppisivun tulee näkyä palvelimen etusivulla. Sivun tulee olla tavallisen käyttäjän muokattavissa, ilman root- tai sudo-oikeuksia. (Muista sammuttaa Apache ensin.)



### c) Automoottorix. Automatisoi Nginx asennus Ansiblella. Ylläpitäjän osuus Ansiblella riittää, itse HTML-weppisivut voi tehdä käsin.



### d) Vapaaehtoinen bonus: Osiris-T. Osiris-T asentaa Wazuhin ja tarvittavat virtuaalikoneet. Voit lähettää vihamielistä verkkoliikennettä (tcpreplay), siepata sen (suricata) ja saat tulokset suoraan dashboardille (wazuh). Enemmän alpha kuin se kreikkalainen kirjain. Mutta Oskari, Nico ja Arttu ilahtuvat, jos kokeilet. Häkämies, Saario, Mukkula 2026: Osiris-T. Häkämies etal 2026: How to Install.



### Lähteet
