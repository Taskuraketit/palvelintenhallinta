# Tässä on kuvattu h6 miniprojektin testausta

Lähtökohtana, että virtuaalikoneelle oli asennettu 
- tarvittavat ohjelmat (ja vähän ekstraa)
- tabulaattoritäydennys
- leikepöytäkopiointi toimimaan isäntä- ja virtuaalikoneen välillä

## Ensimmäinen vaihe: tekeekö ohjelma sen mitä pitää eli luo käyttäjät sekä generoi näille ssh-avaimet?

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

## Toinen vaihe: muita tarpeellisia testejä

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

## Kolmas vaihe: aiemmin tehtyjen asioiden peruminen (ei sinänsä välttämätöntä, mutta tulee testattua tämäkin)

Käyttäjien poistaminen käsin

````bash
sudo userdel -r matti
sudo userdel -r liisa
sudo userdel -r maija
sudo userdel -r eero
````

<img width="601" height="173" alt="image" src="https://github.com/user-attachments/assets/f7329c5c-b272-443a-bf22-0f2286be2971" /><br>
Kotihakemistoon siityminen ja projektikansion poisto

````bash
cd ~
rm -rf h6-Miniprojekti/
````

Todennetaan, ettei käyttäjiä sekä heidän aiemmin luotuja kotihakemistoja löydy

````bash
getent passwd matti liisa maija eero
ls /home
````

<img width="572" height="114" alt="image" src="https://github.com/user-attachments/assets/70269f2e-86a0-40c9-ad68-eb2c1bd7b436" /><br>
Tyhjää täynnä :)

## Neljäs vaihe: paranneltu testaus

Tehdään nyt uusi testi, mutta hieman aiempaa loogisemmin sillä ajatuksella, että järjestys olisi sopiva myös tulevassa demonstraatiossa.

1. Repon kopiointi

<img width="793" height="156" alt="image" src="https://github.com/user-attachments/assets/bc64f64a-2868-49a9-be20-398261cf4fb0" /><br>
2. Repon sisällön tarkastelu

<img width="745" height="379" alt="image" src="https://github.com/user-attachments/assets/75a4f69c-88d5-419f-813a-e869375174ea" /><br>
<img width="537" height="588" alt="image" src="https://github.com/user-attachments/assets/d2698e2d-52d1-4a0e-a584-f4303886b1b0" /><br>
3. Todennetaan, ettei käyttäjiä ole luotu valmiiksi

<img width="749" height="37" alt="image" src="https://github.com/user-attachments/assets/f417dbf6-a648-4421-a237-d4c2be1ecddc" /><br>
4. Ajetaan playbook

<img width="1233" height="379" alt="image" src="https://github.com/user-attachments/assets/87cd3525-107c-4c8c-bb06-c8adced64c02" /><br>
5. Todennetaan idempotenssi ajamalla playbook.yml uudelleen

<img width="1251" height="461" alt="image" src="https://github.com/user-attachments/assets/58ff50b0-033e-40e2-98fa-a4f5544ac5d2" /><br>
➡️ Tällä kertaa **mikään ei muuttunut**

6. Todennetaan vielä käyttäjien sekä heidän SSH-avaimiensa olemassaolo

<img width="735" height="210" alt="image" src="https://github.com/user-attachments/assets/bfefbdc9-4cc4-46c9-9788-4275e45e5412" /><br>
...ja näin olemme todentaneet, että **Userforge TSN tekee sen mitä pitääkin: luo käyttäjät ja generoi näille SSH-avaimet** :) 🥳
