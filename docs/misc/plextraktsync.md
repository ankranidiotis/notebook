# Οδηγός Εγκατάστασης PlexTraktSync (μέσω pipx)

Το **PlexTraktSync** είναι ένα εργαλείο που συγχρονίζει την κατάσταση παρακολούθησης (watched status), τις βαθμολογίες και τη συλλογή μεταξύ Plex και Trakt. Αυτός ο οδηγός χρησιμοποιεί το `pipx` για ασφαλή εγκατάσταση σε απομονωμένο περιβάλλον.

---

## 1. Προαπαιτούμενα
* **Python 3.9+**
* Λογαριασμοί σε **Plex** και **Trakt**.
* Εγκατεστημένο το **pipx**.

### Εγκατάσταση pipx (αν δεν το έχετε)
* **Ubuntu/Debian:** `sudo apt install pipx`
* **Fedora:** `sudo dnf install pipx`
* **macOS:** `brew install pipx`
* **Windows:** `pip install --user pipx`
* *Μετά την εγκατάσταση, τρέξτε:* `pipx ensurepath` (και ίσως χρειαστεί restart το τερματικό).

---

## 2. Εγκατάσταση PlexTraktSync

Εκτελέστε την παρακάτω εντολή για να εγκαταστήσετε την εφαρμογή σε δικό της εικονικό περιβάλλον:

```bash
pipx install PlexTraktSync
```

---

## 3. Δημιουργία Trakt App (API Keys)

Απαραίτητο βήμα για τη σύνδεση με το API του Trakt:

1.  Συνδεθείτε στο [Trakt.tv](https://trakt.tv).
2.  Πηγαίνετε στο [Create Application](https://trakt.tv/oauth/applications/new).
3.  Συμπληρώστε:
    * **Name:** `PlexTraktSync`
    * **Description:** `Sync script`
    * **Redirect URI:** `urn:ietf:wg:oauth:2.0:oob` (**Προσοχή:** Ακριβώς αυτό).
4.  Πατήστε **Save App**.
5.  Κρατήστε το **Client ID** και το **Client Secret**.

---

## 4. Αρχική Ρύθμιση (Login)

Τρέξτε την εντολή ρύθμισης:

```bash
plextraktsync login
```

Ακολουθήστε τα βήματα στην οθόνη:
1.  Επικολλήστε το **Trakt Client ID** και **Client Secret** (από το βήμα 3).
2.  Ανοίξτε το link που θα εμφανιστεί, κάντε **Approve** και αντιγράψτε το PIN πίσω στο τερματικό.
3.  Το script θα συνδεθεί αυτόματα με τον Plex Server σας (ή θα ζητήσει login).

---

## 5. Συγχρονισμός

Εκτελέστε τον πρώτο συγχρονισμό (ενδέχεται να καθυστερήσει ανάλογα με το μέγεθος της βιβλιοθήκης):

```bash
plextraktsync sync
```

---

## 6. Αυτοματοποίηση (Cron - Linux/macOS)

Επειδή το `pipx` εγκαθιστά τα binaries συνήθως στο `~/.local/bin`, πρέπει να δηλώσουμε σωστά τη διαδρομή στο cron.

1.  Βρείτε την ακριβή διαδρομή: `which plextraktsync`
2.  Ανοίξτε το crontab: `crontab -e`
3.  Προσθέστε τη γραμμή (π.χ. για κάθε 6 ώρες):

```cron
0 */6 * * * /home/ΤΟ_ΟΝΟΜΑ_ΧΡΗΣΤΗ_ΣΑΣ/.local/bin/plextraktsync sync
```

*Σημείωση: Αντικαταστήστε τη διαδρομή με αυτή που έβγαλε η εντολή `which`.*

---

## 7. Διαχείριση Ρυθμίσεων

Τα αρχεία ρυθμίσεων αποθηκεύονται συνήθως στο `~/.config/PlexTraktSync/` (Linux/Mac) ή `%AppData%` (Windows).

* Για να επεξεργαστείτε τις ρυθμίσεις (π.χ. ποιες βιβλιοθήκες να εξαιρούνται):
  ```bash
  plextraktsync edit
  ```

* Για αναβάθμιση της εφαρμογής στο μέλλον:
  ```bash
  pipx upgrade PlexTraktSync
  ```