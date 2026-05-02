# Tässä on kuvattu h6 miniprojektin testausta

Lähtökohtana, että virtuaalikoneelle oli asennettu 
- tarvittavat ohjelmat (ja vähän ekstraa)
- tabulaattoritäydennys
- leikepöytäkopiointi toimimaan isäntä- ja virtuaalikoneen välillä

````bash
sudo apt-get update && sudo apt-get install -y ansible git tree micro timeshift curl glances htop bash-completion unattended-upgrades
````

Kopioin repon virtuaalikoneelle

<img width="789" height="156" alt="image" src="https://github.com/user-attachments/assets/56d9077b-a263-4f05-8c2c-6035102991a0" /><br>
Tarkistin repon sisällön

<img width="753" height="380" alt="image" src="https://github.com/user-attachments/assets/38bfda37-da60-452d-b409-3691440ce0ad" /><br>
Tarkistin playbook.ymlin sisällön

<img width="542" height="385" alt="image" src="https://github.com/user-attachments/assets/8fb2524b-b61e-4ec3-aa46-49e48292321d" /><br>
Tarkistin users.ymlin sisällön

<img width="532" height="195" alt="image" src="https://github.com/user-attachments/assets/06889e94-3886-43d2-a180-43b5ecd07eba" /><br>
Ajoin playbook.ymlin, **tuli virheilmoitus**

<img width="1046" height="382" alt="image" src="https://github.com/user-attachments/assets/4ce03cfc-de57-4433-a0d8-a4e321d2c48f" /><br>
Virheilmoitus johtuu Microsoft Copilotin mukaan siitä, että 
- Playbook ajetaan localhostia vasten
- Playbook käyttää become: true (eli sudoa)
- Ansible yrittää suorittaa komentoja root‑oikeuksilla
- sudo vaatii salasanan, mutta Ansiblelle ei ole annettu sitä
- Tämän seurauksena Ansible jää odottamaan ja kaatuu

Testataan seuraavaksi niin, että ajetaan komento sudona eli **ansible-playbook playbook.yml -K**

<img width="1047" height="424" alt="image" src="https://github.com/user-attachments/assets/32972bb5-06d8-4013-a50d-9c361f78b7b9" /><br>
Kuvasta nähdään, että nyt **ajo onnistui** (ok=2, changed=1).

Todennetaan vielä **idempotenssi**
<img width="1242" height="470" alt="image" src="https://github.com/user-attachments/assets/dd1461fa-4d10-481a-8c6c-3023a496fc29" /><br>
Jälkimmäisellä ajokerralla yhteenvedossa näkyy **ok=2** ja **changes=0** eli molemmat tehtävät (käyttäjien luonti sekä SSH-avainten luonti) saatiin suoritettua, mutta **mitään ei muuttunut**.

**Muita tarpeellisia testejä**

1) Käyttäjien olemassaolon todentaminen

````bash
getent passwd matti liisa maija eero
````

<img width="738" height="96" alt="image" src="https://github.com/user-attachments/assets/5b16601d-4246-4134-b0d7-4b3d641ca5cd" /><br>
⚠️ **Tämä kannattaa tehdä repon kopioimisen jälkeen _ennen playbookin ajoa_, <br>jotta todennetaan etteivät käyttäjät ole olemassa jo valmiiksi!** ⚠️

Ajon jälkeen ja idempotenssin toteamisen jälkeen

2) Kotihakemistojen olemassaolon todentaminen

````bash
ls -ld /home/matti /home/liisa /home/maija /home/eero
````

<img width="905" height="100" alt="image" src="https://github.com/user-attachments/assets/0c94dcd2-9ca4-4b1a-ac63-2b1f72bcdc3b" /><br>

3) SSH-avaimen olemassaolon todentaminen (yhdellä uudella käyttäjällä)

````bash
sudo ls -l /home/matti/.ssh
````

<img width="642" height="98" alt="image" src="https://github.com/user-attachments/assets/d2abeab1-7761-4d80-8e5f-940516d67b69" />
