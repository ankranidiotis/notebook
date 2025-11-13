# 🔐 OpenDNS Setup Guide (for Network-Wide Filtering)

---

## **A. Initial Setup (Dashboard)**

1. **Sign Up:** Create a free **OpenDNS Home** account.
    
2. **Add Network:** Log into the dashboard and go to the **Settings** tab.
    
3. Click **"Add a network."** OpenDNS will detect your current public IP address (e.g., `94.71.227.253`).
    
4. **Name It:** Give your network a label (e.g., `Office`).
    
5. **Dynamic IP Check:** **Crucially,** check the box that says **"Yes, it is dynamic."** (This is essential as most home IPs change).
    
6. **Set Filtering:** Go to the **Web Content Filtering** section and choose your desired level (e.g., High, Moderate, or Custom).
    

---

## **B. Router Configuration**

This step forces all devices on your Wi-Fi to use the OpenDNS filters.

1. **Access Router:** Log into your router's administration page (usually by typing `` `192.168.1.1` `` or `` `192.168.0.1` `` in a browser).
    
2. **Find DNS Settings:** Look under **WAN, Internet, Setup,** or **DHCP Settings**.
    
3. **Enter OpenDNS Servers:** Change the DNS settings from "Automatic" to manual and enter these addresses:
    
    - **Primary DNS:** `` `208.67.222.222` ``
        
    - **Secondary DNS:** `` `208.67.220.220` ``
        
4. **Save & Reboot:** **Save** your settings and then **reboot** the router.
    

---

## **C. Permanent IP Tracking (Dynamic IP Fix)**

If you have a dynamic IP (which you likely do), you need a program to keep your dashboard updated, or your filters will fail when your IP changes.

1. **Download Updater:** Download and install the **OpenDNS Dynamic IP Updater client** (available for Windows and Mac from the Cisco support site).
    
2. **Linux (`ddclient`):** On Linux, install and configure the built-in `` `ddclient` `` package instead.
    
    - **Install:** `` `sudo apt install ddclient` ``
    - **Protocol:** Use `` `dyndns2` ``
    - **Server:** Use `` `updates.opendns.com` ``
    - **IP Discovery Method** `Web based IP Discovery service`
    - **Login:** Your OpenDNS email address
    - **Hosts to update**  `Office`
        
3. **Run Service:** Ensure the Updater program/service is running in the background:

```
sudo systemctl start ddclient.service
sudo systemctl enable ddclient.service
sudo systemctl status ddclient.service
```
    

---

## **D. Final Verification**

1. **Reboot**
    
2. **Test:** Open a browser and visit **`http://welcome.opendns.com/`**.
    
3. **Success:** You should see a green checkmark confirming that your network is now using OpenDNS. The filters are active!