# Εγκατάσταση Plex Server σε Linux machine με Docker

## Προαπαιτούμενα

1. Docker
2. Docker Compose

## Εγκατάσταση

1. Δημιουργούμε τον φάκελο `plex-server` στον φάκελο `home` του χρήστη. 
2. Μέσα στον φάκελο δημιουργούμε το αρχείο `docker-compose.yml` με τον παρακάτω περιεχόμενο:

```yaml
services:
  plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: host
    environment:
      - PUID=1000
      - PGID=1000
      - VERSION=docker
    volumes:
      - ./config:/config
      - /media/nassoskranidiotis/Backup/Movies:/movies  # Θέση στον σκληρό δίσκο όπου αποθηκεύονται οι ταινίες
      - /media/nassoskranidiotis/Backup/Series:/tv    # Θέση στον σκληρό δίσκο όπου αποθηκεύονται οι σειρές
    devices:
      - /dev/dri:/dev/dri
    restart: unless-stopped
```

3. Εκτελούμε τον `docker-compose up -d` για να εκτελέσουμε τον Plex Server.
4. Δημιουργούμε τους φακέλους `/movies` και `/tv` στον σκληρό δίσκο και τοποθετούμε τους ταινίες και τις σειρές. Οι υπότιτλοι να έχουν το ίδιο όνομα με αυτό της ταινίας και κατάληξη `.el.srt`.

## Πρόσβαση στον Plex Server

1. Η πρόσβαση στον Plex Server γίνεται μέσω του `http://localhost:32400`
2. Είναι απαραίτητο να ανοίξουμε την θύρα `32400` στο τείχος προστασίας του λειτουργικού συστήματος.
3. Σύνδεση με προσωπικό λογαριασμό Plex.

## Ρυθμίσεις Plex

1. Settings -> Transcoder -> Show Advanced -> Disable video stream transcoding -> Save
2. Manage -> Libraries -> Add Library και προσθέτουμε το είδος της βιβλιοθήκης (π.χ. Movies) και στην συνέχεια επιλέγουμε τον φάκελο `/movies` (που έχουμε ορίσει μέσα στο Docker container).
3. Στις 3 τελείες δίπλα στο Movies επιλέγουμε Scan Library Files. 

## Επίλυση προβλημάτων

1. Αν το HRD10 ή Dolby Vision δεν παίζει καλά στην τηλεόραση, τότε πρέπει να ενεργοποιηθεί το HDMI Deep Color (LG) σε αυτήν. 


# Εγκατάσταση Plex Server σε Contabo VPS

1. Ενημέρωση Συστήματος & Εγκατάσταση Εξαρτήσεων
Συνδεθείτε μέσω SSH στον VPS και εκτελέστε:

```Bash
sudo apt update && sudo apt upgrade -y
sudo apt install curl gpg -y
```

2. Προσθήκη του Plex Repository
Για να λαμβάνετε αυτόματα ενημερώσεις, προσθέστε το επίσημο αποθετήριο:

```Bash
# Προσθήκη κλειδιού GPG
curl https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex-archive-keyring.gpg > /dev/null

# Προσθήκη του Repository
echo "deb [signed-by=/usr/share/keyrings/plex-archive-keyring.gpg] https://downloads.plex.tv/repo/deb public main" | sudo tee /etc/apt/sources.list.d/plexmediaserver.list

# Εγκατάσταση του Plex
sudo apt update
sudo apt install plexmediaserver -y
```

3. Δημιουργία Φακέλων & Δικαιώματα
Δημιουργήστε τους φακέλους για τις ταινίες και δώστε δικαιώματα στον χρήστη plex:

```Bash
sudo mkdir -p /home/media/movies
sudo mkdir -p /home/media/tvshows
sudo chown -R plex:plex /home/media
sudo chmod -R 755 /home/media
```

4. Πρώτη Σύνδεση (SSH Tunnel)
Επειδή ο server είναι απομακρυσμένος, η πρώτη ρύθμιση πρέπει να γίνει μέσω "γέφυρας". Από το τερματικό του υπολογιστή σας (Linux Mint):

```Bash
ssh -L 8888:127.0.0.1:32400 root@IP_TOY_CONTABO -N
```

Αφήστε το τερματικό ανοιχτό και ανοίξτε στον browser τη διεύθυνση: http://127.0.0.1:8888/web

5. Ρύθμιση Plex & Remote Access
- Συνδεθείτε (Sign In) με τον λογαριασμό σας.
- Ονομάστε τον server σας.
- Προσοχή: Τσεκάρετε το κουτάκι "Allow me to access my media outside my home".
- Προσθέστε τις βιβλιοθήκες επιλέγοντας τις διαδρομές /home/media/movies κλπ.
- Μόλις ολοκληρώσετε, πηγαίνετε Settings > Remote Access και βεβαιωθείτε ότι το εικονίδιο είναι Πράσινο.

6. Ρύθμιση Firewall (Αν χρησιμοποιείτε UFW)
Επιτρέψτε την επικοινωνία στην πόρτα του Plex:

```Bash
sudo ufw allow 32400/tcp
```