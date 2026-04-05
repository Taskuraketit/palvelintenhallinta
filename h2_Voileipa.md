### x) Lue ja tiivistä. (Tässä x-alakohdassa ei tarvitse tehdä testejä tietokoneella, vain lukeminen tai kuunteleminen ja tiivistelmä riittää. Tiivistämiseen riittää muutama ranskalainen viiva. Ei siis vaadita pitkää eikä essee-muotoista tiivistelmää. Lisää kuhunkin jokin oma kysymys tai huomio.)

- Karvinen 2026: Sudo without password
- Munroe 2006: xkcd 149: Sandwitch
- Karvinen 2026: Passwordless Sudo with Ansible
- Ansiblen sisäänrakennettu dokumentaatio ansible-doc -kommennolla.
-- Kustakin vain
  - Johdantokappale (Usein MODULE alla, päättyy OPTIONS alkuun)
  - Nimetyt optiot selityksineen
  - Esimerkeistä (EXAMPLES) jokin helppo ja keskeinen esimerkki
    - 'ansible-doc copy': content, dest, src; owner, group, mode;
    - 'ansible-doc apt': name, state, update_cache.
    - 'ansible-doc file': path, recurse, src, state. owner, group, mode;
    - 'ansible-doc user': name; create_home, comment, groups, shell, state, system.
    - 'ansible-doc authorized_key': user, key.
### a) Sudoless. Tee ansiblea varten tunnus, jolla voi käyttää sudoa ilman salasanaa. Sekä ssh-kirjautuminen että sudon käyttö tulee olla ansbilea varten automatisoitu.
### b) Antero. Tee salasanaton, automaattisesti ssh:lla kirjautuva tunnus Ansiblella.
### c) Package. Asenna kaksi pakettia ansiblella.
### d) File. Kirjoita orjalle useamman rivin mittainen tiedosto Ansiblella. Määrittele sen omistaja, omistava ryhmä ja oikeudet. Käytä oikeuksille oktaalinumeroa, esim. "0600". Kerro, mitä oikeudet ovat symbolisessa muodossa, esim. "-rwxr--r--". Selitä, mitä kukin käyttäjä saa tehdä tuolle tiedostolle.
### e) Jotain muuta. Näytä esimerkki ansiblen käskystä, jota ei ole vielä käsitelty kurssilla tai kotitehtävissä. Voit ottaa jonkun muun modulin kuin apt, file, copy, user tai authorized_key. Tai voit käyttää ominaisuutta, jota ei vielä ole demonstroitu. Jos tiivistystehtävässä x on mainittu ominaisuuksia, joita ei tunneilla tai läksyissä kokeiltu, nekin kelpaavat.
