# Instruction Sheet: Installing Epson L6160 on Modern Linux Mint/Ubuntu

This guide provides the definitive steps to manually install your Epson L6160 network printer when the standard automatic setup fails.

**Prerequisites:** You need your printer's IP address (e.g., `192.168.1.100`).

---

## Step 1: Prepare the Drivers 🔧

First, we will install the correct official driver and remove the conflicting system driver.

1.  **Download the Official Driver:**
    * Search online for "Epson L6160 driver linux".
    * From the official Epson support site, download the printer driver file that ends in `.deb`.

2.  **Install the Official Driver:**
    * Go to your `Downloads` folder.
    * Double-click the downloaded `.deb` file to install it with the GDebi Package Installer.

3.  **Remove the Conflicting Driver:**
    * Open a terminal and run the following command to remove the old, problematic system driver:
        ```bash
        sudo apt-get remove printer-driver-escpr
        ```

---

## Step 2: Prepare the System

Next, we will disable the faulty automatic discovery service that interferes with the setup.

4.  **Disable the `cups-browsed` Service:**
    * In the terminal, run these two commands to stop and permanently disable the service:
        ```bash
        sudo systemctl stop cups-browsed
        sudo systemctl disable cups-browsed
        ```

---

## Step 3: Manually Create the Printer 🖨️

Finally, we will use the command line to manually create the printer queue with the correct driver and settings.

5.  **Find the Driver File (PPD) Path:**
    * In the terminal, run this command to find the exact location of the driver file:
        ```bash
        find /opt/epson-inkjet-printer-escpr2 -name "*L6160*.ppd.gz"
        ```
    * **Copy the full path** that the command outputs.

6.  **Place the Driver File in the System Folder:**
    * Use the path you just copied in the following command. Replace `[PASTE THE FULL PATH HERE]` with the path you copied.
        ```bash
        sudo gunzip -c /opt/epson-inkjet-printer-escpr2/ppds/Epson/Epson-L6160_Series-epson-inkjet-printer-escpr2.ppd.gz | sudo tee /usr/share/cups/model/Epson-L6160.ppd > /dev/null
        ```

7.  **Create the Printer Queue:**
    * Run this final command in the terminal. Note the use of the `-P` flag.
        ```bash
        sudo lpadmin -p Epson_L6160 -E -v socket://192.168.1.100:9100 -P /usr/share/cups/model/Epson-L6160.ppd
        ```

---

## Step 4: Verification ✅

* Go to **System Settings > Printers**. You should see a single printer named **"Epson_L6160"**.
* Right-click on the printer and print a test page to confirm everything is working.
