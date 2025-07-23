# Phase 1: Setting Up Axon and LTE Router

Follow the steps below to properly configure the hardware connections and establish SSH access to the Axon device.

## ✅ Step-by-Step Guide

### 1. Power Up the LTE Router
- Plug in and turn on your LTE router.

### 2. Connect Ethernet Cables
- Connect the **Axon's ETH0 port** to the LTE router using an Ethernet cable.
- Connect your **laptop's Ethernet port** to the LTE router.

### 3. Connect Serial Port
- Use a serial cable to connect the **Axon serial port** to your **Windows laptop**.

### 4. Open Serial Terminal
- Launch **MobaXterm** on your laptop.
- Start a **Serial session** using the **COM port** corresponding to the Axon UART connection.

### 5. Adjust the Red Switch on Axon
- **Slide the red switch on the Axon toward the UART port.**
- This allows serial communication between the Axon and your laptop.
- After adjusting the switch, **power on the Axon**.

### 6. Monitor the Boot Process
- The Axon’s **boot log** should appear in the serial terminal window.

### 7. Log In to Axon
- Wait for the login prompt to appear.
- Enter the following credentials:

Username: `root`  
Password: `root`

### 8. Enable Promiscuous Mode on ETH0
- After successful login, run the following command in the serial terminal:
```
sudo ip link set dev eth0 promisc on
```
- This sets the `eth0` interface to **promiscuous mode**.

### 9. Access Axon via SSH
- Open a **new SSH session** in MobaXterm.
- Connect to the Axon using:

IP address: `192.168.10.10`  
Username: `root`  
Password: `root`

> ✅ At this point, SSH access to the Axon should be established without any issues.

### 10. Switch Interface for GNSS Module
- After successfully connecting via SSH:
- **Release the serial terminal.**
- **Slide the red switch** on the Axon to the **opposite position** to use the **GNSS module interface**.

## 📝 Notes

- Ensure all cables and connections are securely fastened.
- Follow the correct switch positions to enable proper communication with either the UART or GNSS interfaces.
- If SSH access fails, recheck the network connections and repeat the login process.
