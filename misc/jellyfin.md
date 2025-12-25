# Jellyfin

## Εγκατάσταση Docker

```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```
Σημείωση: Θα χρειαστεί να αποσυνδεθείς (logout) και να ξαναμπείς (login) στο SSH για να ενεργοποιηθεί αυτή η αλλαγή.

## Εγκατάσταση Docker Compose

```bash
sudo apt install docker-compose-plugin
```

## Εγκατάσταση Jellyfin σε Docker

**Δημιουργία φακέλων:**

```bash
mkdir -p ~/docker/jellyfin/config ~/docker/jellyfin/cache
sudo chown -R $USER:$USER ~/docker/jellyfin
```

**Δημιουργία docker-compose.yml:**

```bash
cd ~/docker/jellyfin
nano docker-compose.yml
```
με περιεχόμενο:

```yaml
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    network_mode: bridge
    ports:
      - 127.0.0.1:8096:8096
    volumes:
      - /root/docker/jellyfin/config:/config
      - /root/docker/jellyfin/cache:/cache
      - /home/media/movies:/data/movies:rw
      - /home/media/tvshows:/data/tvshows:rw
    restart: unless-stopped
```

**Γιατί έβαλα 127.0.0.1:8096:8096**; Με αυτόν τον τρόπο, το Jellyfin δεν είναι προσβάσιμο από το internet ακόμα. Είναι προσβάσιμο μόνο "μέσα" από τον server. Αυτό το κάνουμε για να αναγκάσουμε την κίνηση να περάσει αργότερα από τον Reverse Proxy (SSL) ή το VPN που θα στήσουμε.

**Εκκίνηση του container**

```bash
docker compose up -d
```

**Έλεγχος ασφαλείας**

Το `http://IP-of-Contabo:8096` δεν πρέπει να τρέχει, γιατί έχουμε ορίσει 127.0.0.1:8096. Με SSH Tunnel θα μπορούμε να το προσβάλουμε από τον υπολογιστή μας.

## Ρύθμιση του Jellyfin

Δημιουργούμε τον SSH Tunnel με τον υπολογιστή μας.

```bash
ssh -L 8096:127.0.0.1:8096 root@IP-of-Contabo
```

Ανοίγουμε τον browser και πατάμε `http://127.0.0.1:8096` και ορίζουμε ένα username και password για τον Jellyfin. 

> Set up remote access: YES

**Απενεργοποίηση του transcoding**

Πήγαινε στο Dashboard > Users > Media Playback και απενεργοποίησε το transcoding. Πάτα Save. 

## Εγκατάσταση του Jellyfin Media Player

Εγκαθιστούμε την Flatpak έκδοση από τον Package Manager του Linux.

Για ελληνικούς υποτίτλους:

1. Settings > Subtitles > Ελληνικά.
2. Dashboard > Plugins > Subtitles > OpenSubtitles.
3. Πάτα Install.

Κάνουμε επανεκκίνηση του container:

```bash
cd ~/docker/jellyfin
docker compose restart
```
και μετά πηγαίνουμε πάλι στα Plugins > Subtitles και ορίζουμε τον username και password του OpenSubtitles.

*Ρύθμιση της Βιβλιοθήκης για Ελληνικά*
Πρέπει να πεις στο Jellyfin να ψάχνει για ελληνικούς υπότιτλους κατά το σκανάρισμα:

- Dashboard -> Libraries.
- Πάτα τις 3 τελείες πάνω στη βιβλιοθήκη "Movies" -> Manage Library.
- Σκρόλαρε κάτω στο Subtitles.
- Επίλεξε το Greek στο "Download languages".
- Τσέκαρε το κουτάκι OpenSubtitles (κάτω από το Subtitle downloaders).
- Πάτα Save.

## Εγκατάσταση και ρύθμιση VPN με Tailscale

Στο αρχείο `docker-compose.yml` πρέπει να προσθέσουμε τον VPN:

```yaml
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: contabo-jellyfin
    network_mode: host # Σημαντικό: Το Tailscale "πατάει" πάνω στο OS
    privileged: true # <-- ΑΠΑΡΑΙΤΗΤΟ για να πειράξει το δίκτυο του host
    environment:
      - TS_AUTHKEY=xxx
      - TS_STATE_DIR=/var/lib/tailscale
    volumes:
      - ./tailscale_state:/var/lib/tailscale
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - NET_ADMIN
      - NET_RAW
    restart: unless-stopped

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    network_mode: host # Σημαντικό: Το Jellyfin "βλέπει" το tailscale0 interface
    volumes:
      - /root/docker/jellyfin/config:/config
      - /root/docker/jellyfin/cache:/cache
      - /home/media/movies:/data/movies:rw
      - /home/media/tvshows:/data/tvshows:rw
    restart: unless-stopped
```

**Δημιουργία Auth Key**

Για να συνδεθεί αυτόματα ο Contabo χωρίς να χρειαστεί να πατήσεις link:

1. Μπες στο Tailscale Admin Console.
2. Settings > Keys > Generate Auth Key.
3. Reusable: YES
4. Generate Key.
5. Αντίγραψε το κλειδί (ξεκινάει από tskey-auth...) και βάλτο στο TS_AUTHKEY στο YAML σου.
6. Ενημέρωσε το `docker-compose.yml` και ξανακινητοποιήσε το container.
7. Στο Contabo, τρέξε

```bash
sudo ufw allow 8096/tcp
sudo chmod 666 /dev/net/tun
```
8. Ενημέρωσε το `docker-compose.yml` και ξανακινητοποιήσε το container.

```bash
cd ~/docker/jellyfin
docker compose down
docker compose up -d
```

**Επιβεβαίωση Σύνδεσης**
Μετά από λίγα δευτερόλεπτα, ο Contabo θα πρέπει να εμφανιστεί στο Tailscale δίκτυό σου. Μπορείς να το ελέγξεις με δύο τρόπους:

- Στο Web Panel: Μπες στο Tailscale Machines και δες αν υπάρχει το "contabo-jellyfin".
- Στο Mint (Τερματικό): Τρέξε tailscale status. Θα πρέπει να δεις τον server στη λίστα με την IP του (π.χ. 100.64.0.5).


**Σύνδεση στον Jellyfin Media Player**
Τώρα που ο server είναι "δίπλα" στον υπολογιστή σου μέσω του VPN:

-Άνοιξε τον Jellyfin Media Player στο Linux Mint.
- Αν σε βγάλει στην οθόνη επιλογής server, πάτα Add Server.
- Στο Server Address, βάλε την Tailscale IP του Contabo: http://100.x.y.z:8096 (αντικατάστησε το 100.x.y.z με την πραγματική IP που βλέπεις στο Tailscale).
- Κάνε login με το username και το password που δημιούργησες στον Wizard.

## Εγκατάσταση του Tailscale στο Google Chromecast with Google TV

1. Εγκατάσταση του Tailscale στο Chromecast
Για να μπορέσει το Chromecast να "δει" τον Contabo, πρέπει να μπει και αυτό στο ίδιο VPN δίκτυο.

Άνοιξε το Play Store στην Google TV σου.

Αναζήτησε και εγκατάστησε την εφαρμογή Tailscale.

Άνοιξε το Tailscale και κάνε Log in (θα σου βγάλει ένα QR code ή έναν κωδικό για να τον βάλεις στο κινητό/PC σου).

Μόλις συνδεθείς, πάτα το κουμπί για να γίνει Active. Θα δεις στη λίστα συσκευών μέσα στο Tailscale τον server σου (akran-media-server).

2. Εγκατάσταση του Jellyfin App
Πήγαινε πάλι στο Play Store της Google TV.

Αναζήτησε και εγκατάστησε την επίσημη εφαρμογή Jellyfin.

Άνοιξε το Jellyfin.

3. Σύνδεση στον Server
Όταν το Jellyfin σου ζητήσει τη διεύθυνση του server:

Πληκτρολόγησε την Tailscale IP του Contabo: http://100.125.30.84:8096

Βάλε το username και το password που δημιούργησες.
