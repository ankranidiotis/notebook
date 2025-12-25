# Οδηγός Εγκατάστασης Jellyfin & Tailscale σε Contabo VPS (Secure Setup)

Αυτός ο οδηγός περιγράφει την εγκατάσταση ενός απόλυτα ασφαλούς Media Server. Ο Jellyfin είναι προσβάσιμος μόνο μέσω του ιδιωτικού δικτύου Tailscale (VPN), καθιστώντας τον αόρατο σε scanners και hackers στο δημόσιο ίντερνετ.

## 1. Ασφάλιση του VPS (SSH)
Πριν ξεκινήσετε με το Docker, θωρακίστε την πρόσβαση στον ίδιο τον server.

* **Χρήση SSH Keys:** Βεβαιωθείτε ότι συνδέεστε με κλειδιά.
* **Απενεργοποίηση Password Login:**
    * Εκτελέστε: `sudo nano /etc/ssh/sshd_config`
    * Αλλάξτε τη γραμμή `PasswordAuthentication` σε `no`.
* **Επανεκκίνηση SSH:**
    * Εκτελέστε: `sudo systemctl restart ssh`

---

## 2. Εγκατάσταση Docker & Compose
Εκτελέστε τις παρακάτω εντολές:

```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```
> **Σημείωση:** Κάντε logout και login για να ενεργοποιηθεί η συμμετοχή σας στο group docker.

```bash
sudo apt install docker-compose-plugin
```

---

## 3. Προετοιμασία Φακέλων
Εκτελέστε:
```bash
mkdir -p ~/docker/jellyfin/config ~/docker/jellyfin/cache ~/docker/jellyfin/tailscale_state
sudo chown -R $USER:$USER ~/docker/jellyfin
```

---

## 4. Ρύθμιση Docker Compose (Secure Sidecar Pattern)
Δημιουργήστε το αρχείο `~/docker/jellyfin/docker-compose.yml` με το εξής περιεχόμενο. Η συγκεκριμένη διάταξη αναγκάζει το Jellyfin να χρησιμοποιεί αποκλειστικά το δίκτυο του Tailscale container (Sidecar pattern), κλείνοντας την "τρύπα" της δημόσιας IP.

```yaml
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: contabo-jellyfin
    network_mode: bridge
    privileged: true
    environment:
      - TS_AUTHKEY=tskey-auth-xxxx # Αντικαταστήστε με το δικό σας Auth Key από το Tailscale Admin Console
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
    # Σημαντικό: Το Jellyfin χρησιμοποιεί το δίκτυο του Tailscale
    network_mode: "service:tailscale"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - /home/media/movies:/data/movies:rw
      - /home/media/tvshows:/data/tvshows:rw
    restart: unless-stopped
```

---

## 5. Ρύθμιση Firewall (UFW)
Επιτρέπουμε μόνο το SSH και την απαραίτητη κίνηση για το Tailscale. Η θύρα 8096 δεν εκτίθεται στο δημόσιο ίντερνετ.

**Βήμα 1: Έλεγχος Κατάστασης**
Πριν εφαρμόσετε κανόνες, ελέγξτε αν το firewall είναι ήδη ενεργό για να αποφύγετε το κλείδωμα:
```bash
sudo ufw status
```
*Αν είναι `inactive`, προχωρήστε άφοβα. Αν είναι `active`, βεβαιωθείτε ότι έχετε ήδη ανοιχτή την πόρτα SSH.*

**Βήμα 2: Εφαρμογή Κανόνων**
Εκτελέστε τις εντολές με την εξής σειρά (η εντολή `limit ssh` εξασφαλίζει ότι δεν θα κλειδωθείτε έξω):

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw limit ssh
sudo ufw allow 41641/udp
sudo ufw enable
```

---

## 6. Εκκίνηση και Αρχική Ρύθμιση
* **Εκκίνηση:**
    ```bash
    cd ~/docker/jellyfin
    docker compose up -d
    ```
* **Σύνδεση για πρώτη φορά:**
    Επειδή ο server δεν είναι ακόμα προσβάσιμος εξ αποστάσεως, χρησιμοποιήστε SSH Tunnel από το PC σας:
    `ssh -L 8096:127.0.0.1:8096 root@IP-of-Contabo`
    Ανοίξτε το `http://127.0.0.1:8096` στον browser σας.
* Στον Wizard επιλέξτε **"Allow remote connections"**.

---

## 7. Σύνδεση Συσκευών (Mint / Chromecast / Mobile)
* **Tailscale IP:** Βρείτε την IP της μορφής `100.x.y.z` από το Tailscale Admin Panel.
* **Σύνδεση:** Χρησιμοποιήστε τη διεύθυνση `http://100.x.y.z:8096` στις εφαρμογές Jellyfin.
* **Επαλήθευση Ασφαλείας:** Με το Tailscale κλειστό στη συσκευή σας, η διεύθυνση `http://IP-of-Contabo:8096` δεν πρέπει να φορτώνει.

---

## 8. Πρόσθετες Ρυθμίσεις
* **Transcoding:** Dashboard > Users > Media Playback > Απενεργοποίηση (για μείωση φόρτου CPU στο VPS).
* **Plugins:** Dashboard > Plugins > OpenSubtitles.
* **Library:** Dashboard > Libraries > Manage Library > Subtitles > Download languages: Greek.

**Σημείωση:** Με αυτό το setup, η κίνηση των δεδομένων σας είναι πλήρως κρυπτογραφημένη και ο server παραμένει αόρατος σε οποιονδήποτε τρίτο στο διαδίκτυο.


## 8. Λίστα Ελέγχου Μετά την Εγκατάσταση (Post-Installation Checklist)

Αφού επιβεβαιώσατε ότι η θύρα 8096 είναι κλειστή, ακολουθήστε τα παρακάτω βήματα για τη μέγιστη θωράκιση του VPS σας.

### 1. Διαχείριση Κλειδιών Tailscale (Key Expiry)

Από προεπιλογή, το Tailscale λήγει τα κλειδιά των συσκευών κάθε 6 μήνες. Αν λήξει το κλειδί του VPS, ο server θα αποσυνδεθεί από το VPN και το Jellyfin θα σταματήσει να παίζει στις συσκευές σας μέχρι να ξανακάνετε login χειροκίνητα.

Βήματα για απενεργοποίηση:

- Συνδεθείτε στο Tailscale Admin Console.
- Μεταβείτε στην καρτέλα Machines.
- Βρείτε στη λίστα το μηχάνημα με το όνομα contabo-jellyfin.
- Στη δεξιά πλευρά της γραμμής, κάντε κλικ στις τρεις τελείες (...).
- Επιλέξτε το Disable key expiry.
- Θα εμφανιστεί μια μικρή ένδειξη "Expiry disabled" δίπλα ή κάτω από το όνομα του server, επιβεβαιώνοντας ότι η σύνδεση είναι πλέον μόνιμη.