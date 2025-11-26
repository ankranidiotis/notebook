# SSH Tunnel

```
ssh -L local_ip:local_port:remote_ip:remote_port user@remote_ssh_host -N
```

και αντιστοιχεί την `local_ip:local_port` στην `remote_ip:remote_port`. Η σημαία `-N` κρατάει ανοικτό το κανάλι επικοινωνίας ανάμεσα στις δύο μηχανές και δεν εκτελεί άλλες εντολές.

### Παράδειγμα
```
ssh -L 192.168.1.225:14330:localhost:1433 nsoutas@soutasoffice.ddns.net -N
```

Με την παραπάνω εντολή η διεύθυνση 192.168.1.225:14330 στον τοπικό υπολογιστή «ακούει» την απομακρυσμένη διεύθυνση localhost:1433 (που σε αυτή την περίπτωση είναι ένας MS SQL Server).
