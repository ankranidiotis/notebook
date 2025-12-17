# SSH

## Εγκατάσταση

**Δημιουργία κλειδιών**

```bash
sssh-keygen -t rsa -b 4096 -C "your email"
```

**SSH Server**

Στην απομακρυσμένη μηχανή (server) εγκαταστάσουμε τον SSH server με τις παρακάτω εντολές:

```bash
sudo apt update
sudo apt install openssh-server
```
Εκκίνηση υπηρεσίας: 

```bash
sudo systemctl start ssh
```

Ενεργοποίηση στην εκκίνηση: 

```bash
sudo systemctl enable ssh
```

Επαλήθευση:

```bash
sudo systemctl status ssh
```


**SSH Client**

Στην τοπική μηχανή (client) εγκαταστάσουμε τον SSH client με τις παρακάτω εντολές:

```bash
sudo apt update
sudo apt install openssh-client
```

**SSH Connection**

Στην τοπική μηχανή (client) εκτελούμε την εντολή:

```bash
ssh <username>@<ip>
```

όπου

- `<username>` είναι το όνομα του χρήστη στην απομακρυσμένη μηχανή (server)
- `<ip>` είναι η IP της απομακρυσμένης μηχανής (server)

## Σύνδεση με κλειδί

Αρχικώς, πρέπει να αντιγράψουμε το κλειδί της μηχανής Α στην Β:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub remoteuser@remoteIP
```

## Έλεγχος των αποτυπωμάτων των κλειδιών

```bash
ssh-keygen -lf ~/.ssh/id_rsa
```

## Μεταφορά αρχείων μέσω SSH

```bash
scp <local_file> <username>@<ip>:<remote_path>
```

όπου

- `<local_file>` είναι το όνομα του αρχείου που θέλεις να μεταφέρεις
- `<username>` είναι το όνομα του χρήστη στην απομακρυσμένη μηχανή (server)
- `<ip>` είναι η IP της απομακρυσμένης μηχανής (server)
- `<remote_path>` είναι η θέση αντιγραφής στην απομακρυσμένη μηχανή (server)

## Ρυθμίσεις του SSH

Ανοίγουμε το αρχείο ρυθμίσεων του SSH με την εντολή:

```bash
sudo nano /etc/ssh/sshd_config
```
και αλλάζουμε τα εξής:

- PasswordAuthentication no
- Pub key authentication yes
- AuthorizedKeysFile .ssh/authorized _keys

Με αυτό τον τρόπο η σύνδεση γίνεται μόνον με το κλειδί. Στην συνέχεια, επανεκκινούμε την υπηρεσία SSH με την εντολή:

```bash
sudo systemctl restart ssh
```