# VNC

    ## Εγκατάσταση

Για δύο Linux μηχανές Α (client), και Β (server), προκειμένου να εγκαταστήσουμε την υπηρεσία VNC στην μηχανή Β, έτσι ώστε η μηχανή Α να μπορεί να συνδεθεί σε αυτήν, κάνουμε τα εξής:

1. Στην μηχανή Β εγκαταστάσουμε τον VNC server `x11vnc` τρέχοντας τις παρακάτω εντολές:

```bash
sudo apt update
sudo apt install x11vnc
x11vnc -storepasswd
x11vnc -usepw -forever -display :0
```

Κατά την εγκατάσταση, θα μας ζητηθεί να ορίσουμε έναν κωδικό πρόσβασης, ο οποίος θα αποθηκευτεί στον φάκελο `~/.vnc/`. Η τελευταία εντολή θα ξεκινήσει τον VNC Server. Το παράθυρο πρέπει να μείνει άνοιχτο όσο η μηχανή Β είναι σε λειτουργία.

2. Έπειτα η μηχανή Α μπορεί να συνδεθεί στην Β μέσω της διεύθυνσης `<IP>:5900`, όπου `<IP>` είναι η IP της μηχανής Β.

## Δημιουργία υπηρεσίας VNC

Για να δημιουργήσουμε μια υπηρεσία VNC που θα ξεκινά με την εκκίνηση της μηχανής Β, κάνουμε τα εξής:

```bash
sudo nano /etc/systemd/system/x11vnc.service
```

Στο αρχείο αυτό εισάγουμε τα εξής:

```ini
[Unit]
Description=Start x11vnc Server at Boot
After=display-manager.service
 
[Service]
ExecStart=/usr/bin/x11vnc -display :0 -auth guess -forever -loop -noxdamage -repeat -rfbauth /home/USERNAME/.vnc/passwd -rfbport 5900 -shared
 
Restart=on-failure
RestartSec=2
 
[Install]
WantedBy=multi-user.target
```

όπου θα πρέπει να αλλάξεις τον `USERNAME` με το όνομα του χρήστη στην μηχανή Β.

Έπειτα ενεργοποιούμε την υπηρεσία με τις παρακάτω εντολές:

```bash
sudo systemctl daemon-reload
sudo systemctl enable x11vnc.service
sudo systemctl start x11vnc.service
```

Μπορούμε να ελέγξουμε την εκκίνηση της υπηρεσίας με την εντολή `sudo systemctl status x11vnc.service`.