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

1. Η πρόσβαση στον Plex Server γίνεται μέσω του `http://localhost:32400/web`
2. Είναι απαραίτητο να ανοίξουμε την θύρα `32400` στο τείχος προστασίας του λειτουργικού συστήματος.
3. Οι φάκελοι βιβλιοθήκης πρέπει να έχουν τα κατάλληλα δικαιώματα πρόσβασης από τον Plex Server. Για αυτό τον λόγο τρέχουμε την εντολή `chmod +x /home/ΤΟ_ΟΝΟΜΑ_ΧΡΗΣΤΗ_ΣΟΥ` και για κάθε φάκελο την εντολή `sudo chmod -R 755 /διαδρομή/προς/τα/αρχεία/σου`. Επίσης βάζουμε τον χρήστη plex στην ομάδα του χρήστη μας ως εξής: `sudo gpasswd -a plex ΤΟ_ΟΝΟΜΑ_ΧΡΗΣΤΗ_ΣΟΥ` και κάνουμε επανεκκίνηση του plex server `sudo systemctl restart plexmediaserver`.
4. Σύνδεση με προσωπικό λογαριασμό Plex.

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
- Αν ο server υπάρχει ήδη, τότε πρέπει να τον κάνουμε claim (Settings > Remote Access > Claim Server)

6. Ρύθμιση Firewall (Αν χρησιμοποιείτε UFW)
Επιτρέψτε την επικοινωνία στην πόρτα του Plex:

```Bash
sudo ufw allow 32400/tcp
```

7. Ο Plex client σε υπολογιστή τρέχει μέσω web browser στην διεύθυνση http://contabo_vps_ip:32400/web


# Plex Server και Tailscale

Αν ο VPS και οι συσκευές που έχουν πρόσβαση σε αυτόν ανήκουν σε ένα tailscale network, τότε ο Plex Server δεν χρειάζεται να εκτίθεται δημόσια, αλλά θα είναι κρυμμένος μέσα στο δίκτυο του tailscale. Για τον σκοπό αυτόν κάνουμε τα εξής:

1. Συνδεόμαστε στον Plex Server μέσω του web browser στην διεύθυνση http://tailscale_vps_ip:32400/web
2. Settings > Network > Custom server access URLs και προσθέτουμε την διεύθυνση http://tailscale_vps_ip:32400. 
3. Settings > Remote Access > Disable remote access.

Παράλληλα, επιτρέπουμε την πρόσβαση στον Plex Server (θύρα 32400) μόνον μέσω του tailscale:

```Bash
sudo ufw allow in on tailscale0 to any port 32400
```

και αποκλείουμε την πρόσβαση από το δημόσιο δίκτυο:

```Bash
sudo ufw status numbered
```
που δίνει για παράδειγμα

```
 sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 41641/udp                  ALLOW IN    Anywhere                  
[ 2] 32400/tcp                  ALLOW IN    Anywhere                  
[ 3] 22 on tailscale0           ALLOW IN    Anywhere                  
[ 4] 32400 on tailscale0        ALLOW IN    Anywhere                  
[ 5] 32400/tcp (v6)             ALLOW IN    Anywhere (v6)             
[ 6] 22 (v6) on tailscale0      ALLOW IN    Anywhere (v6)             
[ 7] 41641/udp (v6)             ALLOW IN    Anywhere (v6)             
[ 8] 32400 (v6) on tailscale0   ALLOW IN    Anywhere (v6)   
```

και διαγράφουμε την πρόσβαση 32400/tcp για IPv4 και για IPv6 από το δημόσιο δίκτυο:

```Bash
sudo ufw delete 2
```
που δίνει 

```
sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 41641/udp                  ALLOW IN    Anywhere                  
[ 2] 22 on tailscale0           ALLOW IN    Anywhere                  
[ 3] 32400 on tailscale0        ALLOW IN    Anywhere                  
[ 4] 32400/tcp (v6)             ALLOW IN    Anywhere (v6)             
[ 5] 22 (v6) on tailscale0      ALLOW IN    Anywhere (v6)             
[ 6] 41641/udp (v6)             ALLOW IN    Anywhere (v6)             
[ 7] 32400 (v6) on tailscale0   ALLOW IN    Anywhere (v6)   
```

και μετά διαγράφουμε και το 4.

```
sudo ufw status numbered
Status: active

     To                         Action      From
     --                         ------      ----
[ 1] 41641/udp                  ALLOW IN    Anywhere                  
[ 2] 22 on tailscale0           ALLOW IN    Anywhere                  
[ 3] 32400 on tailscale0        ALLOW IN    Anywhere                  
[ 4] 22 (v6) on tailscale0      ALLOW IN    Anywhere (v6)             
[ 5] 41641/udp (v6)             ALLOW IN    Anywhere (v6)             
[ 6] 32400 (v6) on tailscale0   ALLOW IN    Anywhere (v6)  
```
Έτσι η πρόσβαση στον Plex Server (θύρα 32400) γίνεται **μόνον** μέσω του tailscale.
