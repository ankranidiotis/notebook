#Αυτοματισμός Ύπνου & Αφύπνισης (Linux Mint)

Αυτός ο οδηγός περιγράφει τη διαδικασία δημιουργίας μιας υπηρεσίας systemd που θέτει τον υπολογιστή σε αναστολή (suspend) καθημερινά στις 23:00 και τον ξυπνάει αυτόματα στις 07:00 το επόμενο πρωί, χρησιμοποιώντας το εργαλείο rtcwake.

1. Το Bash Script
Διαδρομή: ~/bin/night_sleep.sh

Αυτό το script υπολογίζει την επόμενη 07:00 π.μ. και εκτελεί την εντολή αναστολής.

- Δημιουργήστε τον φάκελο αν δεν υπάρχει: mkdir -p ~/bin
- Δημιουργήστε το αρχείο: nano ~/bin/night_sleep.sh
- Επικολλήστε τον κώδικα:

```bash
#!/bin/bash

# Υπολογισμός ώρας αφύπνισης (7:00 π.μ. της επόμενης μέρας)
# Το 'tomorrow 07:00' διασφαλίζει σωστή λειτουργία ακόμα και αν τρέξει πριν ή μετά τα μεσάνυχτα.
WAKE_TIME=$(date +%s -d "tomorrow 07:00")

# Καταγραφή στο system log (για debugging μέσω journalctl)
echo "System suspending via autosleep. Wake up scheduled for $(date -d @$WAKE_TIME)" | logger -t autosleep

# Εκτέλεση rtcwake
# -m mem : Suspend to RAM (γρήγορη επαναφορά)
# -t     : Χρόνος αφύπνισης (epoch seconds)
/usr/sbin/rtcwake -m mem -t $WAKE_TIME
```

- Κάντε το αρχείο εκτελέσιμο:`chmod +x ~/bin/night_sleep.sh`
 
 
2. Το Systemd Service

Διαδρομή: `/etc/systemd/system/autosleep.service`

Αυτή η μονάδα ορίζει τι θα εκτελεστεί.

- Δημιουργία αρχείου: `sudo nano /etc/systemd/system/autosleep.service`
- Επικόλληση κώδικα (ΠΡΟΣΟΧΗ: Αντικαταστήστε το USERNAME με το όνομα χρήστη σας):

```ini
[Unit]
Description=Automated Sleep and Wake Service (11PM to 7AM)
After=network.target

[Service]
Type=oneshot
# Το rtcwake χρειάζεται root δικαιώματα, τα οποία έχει από default το systemd service
ExecStart=/home/USERNAME/bin/night_sleep.sh

[Install]
WantedBy=multi-user.target
```

3. Το Systemd Timer

Διαδρομή: `/etc/systemd/system/autosleep.timer`

Αυτή η μονάδα ορίζει πότε θα εκτελεστεί η υπηρεσία.

- Δημιουργία αρχείου: `sudo nano /etc/systemd/system/autosleep.timer`
- Επικόλληση κώδικα:

```ini
[Unit]
Description=Trigger sleep at 11 PM daily

[Timer]
# Μορφή: DayOfWeek Year-Month-Day Hour:Minute:Second
# *-*-* σημαίνει κάθε μέρα, κάθε μήνα, κάθε έτος.
OnCalendar=*-*-* 23:00:00
Persistent=true
Unit=autosleep.service

[Install]
WantedBy=timers.target
```

4. Εγκατάσταση & Ενεργοποίηση

Εκτελέστε τις παρακάτω εντολές στο τερματικό για να ενημερώσετε το σύστημα για τις νέες μονάδες και να ξεκινήσετε τον χρονοδιακόπτη:

```bash
# Ενημέρωση του systemd για τα νέα αρχεία
sudo systemctl daemon-reload

# Ενεργοποίηση του timer ώστε να ξεκινά με το boot και εκκίνηση τώρα
sudo systemctl enable --now autosleep.timer
```

5. Έλεγχος & Αντιμετώπιση Προβλημάτων

Επιβεβαίωση Χρονοδιαγράμματος

Δείτε πότε είναι προγραμματισμένη η επόμενη εκτέλεση:

```bash
systemctl list-timers --all | grep autosleep
```

Έλεγχος Ιστορικού (Logs)

Αν ο υπολογιστής δεν κοιμηθεί ή δεν ξυπνήσει σωστά, ελέγξτε τα logs:

```bash
journalctl -u autosleep.service
```

Χειροκίνητη Δοκιμή (Άμεση Εκτέλεση)

Για να δοκιμάσετε αν η εντολή ύπνου λειτουργεί τώρα (χωρίς να περιμένετε τις 23:00):

```bash
sudo systemctl start autosleep.service
```
