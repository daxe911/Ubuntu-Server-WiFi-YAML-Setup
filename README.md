**Ubuntu-Server-WiFi-YAML-Setup**

This guide provides step-by-step instructions on how to configure a static or dynamic IP for an Ubuntu Server using a YAML file to connect to a router via Wi-Fi.
Requirements

- Ubuntu Server installed on the device.
- Access to the terminal (SSH or directly).
- Wi-Fi network SSID (name) and password.

**Step 1: Accessing the Netplan Configuration File**

Netplan is the default network management tool for Ubuntu 18.04 and later. To configure Wi-Fi, we need to edit the YAML configuration file.

Open the terminal and run the following command to edit the Netplan file (if the file doesn’t exist, it will be created automatically):

    sudo nano /etc/netplan/01-netcfg.yaml

**Step 2: Setting up a Dynamic IP (DHCP) Configuration**

If you want to assign a dynamic IP (DHCP) to your device, add the following content to the YAML file:

network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: true
      dhcp6: false
      access-points:
        "<SSID>":
          password: "<Your-WiFi-Password>"

Replace <SSID> with your Wi-Fi network name and <Your-WiFi-Password> with your password.

**Step 3: Setting up a Static IP Configuration**

If you prefer to use a static IP, modify the YAML file as follows:

network:
  version: 2
  renderer: networkd
  wifis:
    wlan0:
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.x.xx/24
      gateway4: 192.168.x.x
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
      access-points:
        "<SSID>":
          password: "<Your-WiFi-Password>"

Replace the values:
- 192.168.x.xx/24 with the static IP you want to assign.
- 192.168.x.x with the IP address of your router.
- <SSID> with your Wi-Fi network name.
- <Your-WiFi-Password> with your password.

**Step 4: Apply the Changes**

After editing the YAML file, save it by pressing Ctrl + O, then exit the editor with Ctrl + X.

Now apply the configuration by running:

sudo netplan apply

**Step 5: Verify the Connection**

To verify if your device is connected to Wi-Fi and using the correct IP address, use the following command:

ip addr show wlan0

You should see the IP address assigned to your wlan0 interface.

**Troubleshooting: Starting systemd-networkd Service**

If your network is not working as expected after configuring your YAML file with Netplan, it may be due to the systemd-networkd service not running.
This service is responsible for managing network settings when using Netplan.

**Step 1: Check if systemd-networkd is running**

You can check the status of the systemd-networkd service by running the following command:

sudo systemctl status systemd-networkd

- If the service is active (running), then systemd-networkd is working as expected, and the issue may lie elsewhere.
- If the service is inactive or failed, you will need to start it manually.

**Step 2: Start the systemd-networkd service**

If you found that the service is not running, you can start it with the following command:

sudo systemctl start systemd-networkd

This will start the service and should immediately apply your network settings from Netplan.

**Step 3: Enable systemd-networkd to start on boot**

To ensure that the systemd-networkd service starts automatically whenever your system boots, run the following command:

sudo systemctl enable systemd-networkd

This will enable the service to start automatically every time the system is restarted.

**Final Check**

After starting the service and enabling it, you can reapply your Netplan configuration with:

sudo netplan apply

Then, check your network status with:

ip addr show wlan0

**Conclusion**

If your device wasn't connecting to the network after configuring Netplan, ensuring that systemd-networkd is running might solve the problem.
Starting and enabling the service ensures that the network configuration is correctly applied.

