# Enroll MOK on Linux machine

Assuming that Secure Boot is already enabled on UEFI and a MOK key is not enrolled, let’s run the following commands to create a new MOK certificate and enroll it.

First, ensure that the necessary tools are installed on your Ubuntu system. Use the following command to install them:

```
sudo apt-get update
sudo apt-get install mokutil
```

If you haven’t already generated a MOK key pair, you need to do so. Use the openssl command to generate a key and certificate:

```
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=Your Name/"
```

This command generates a key (MOK.key) and a certificate (MOK.crt). The /CN=Your Name/ part specifies the Common Name in the certificate. Then, enroll the new MOK certificate:

```
sudo mokutil --import MOK.der
```

Reboot your system to complete the MOK enrollment. During the boot process, you should see a “MOK Manager” screen that asks if you want to enroll a key.

- Select “Enroll MOK.”
- Choose “Continue.”
- Enter the password you set when importing the MOK.
- Confirm the enrollment.

After confirming, your system will reboot automatically.
