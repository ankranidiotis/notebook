# Εγκατάσταση γραφικού περιβάλλοντος

```
# 1. Εγκατάσταση βασικών πακέτων
sudo apt update
sudo apt install ubuntu-mate-core
sudo apt install lightdm
sudo systemctl set-default graphical.target
sudo service lightdm start

# 2. Δημιουργία χρήστη 'username'
sudo useradd -m -s /bin/bash username

# 3. Ορισμός του κωδικού
echo "username:yourpassword" | sudo chpasswd

# 4. Του δίνουμε δικαιώματα διαχειριστή (sudo)
sudo usermod -aG sudo username

# 5. Ρυθμίζουμε το LightDM να επιτρέπει την πληκτρολόγηση
sudo bash -c 'cat > /etc/lightdm/lightdm.conf <<EOF
[Seat:*]
greeter-show-manual-login=true
allow-guest=false
EOF'

# 6. Επανεκκίνηση του LightDM για να ενημερωθεί η λίστα χρηστών sudo service 
lightdm restart

sudo reboot
```
