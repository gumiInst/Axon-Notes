# Phase 1: Initial Setup

Follow the instructions below to configure the Axon, LTE Router, and establish SSH and serial access.

### ✅ Step 1: Power and Connection Setup

- Power up the **LTE router**.
- Connect **Axon's ETH0 port** and your **laptop's Ethernet port** to the LTE router using Ethernet cables.
- Connect the **Axon serial port** to your **Windows laptop** using a serial cable.

### ✅ Step 2: Serial Terminal Access

- Launch **MobaXterm** on your laptop.
- Open a **Serial session**, using the COM port corresponding to the Axon's UART connection.
- Ensure the **red switch on the Axon is slid toward the UART port**.  
  This enables serial communication between the Axon and the laptop.
- Now, **power up the Axon**.

> 🖥️ The boot process log should now appear on the MobaXterm serial terminal.

- Wait for the login prompt to appear.
- Log in with the following credentials:  
  `Username: root`  
  `Password: root`

### ✅ Step 3: Enable Promiscuous Mode

- After logging in successfully, run the following command to set the Ethernet interface to promiscuous mode:
```
sudo ip link set dev eth0 promisc on
```

> ✅ At this point, SSH access to the Axon should be available without any issues.

### ✅ Step 4: SSH Access via MobaXterm

- On your laptop, open a new **SSH session in MobaXterm**.
- Connect to the Axon using the following information:  
  `IP address: 192.168.10.10`  
  `Username: root`  
  `Password: root`

- After accessing Axon via SSH:
  - You may now close the serial terminal.
  - Slide the **red switch to the side opposite the UART port** to enable the **GNSS module** interface.

### ✅ Step 5: Emwave Device Setup

- Power up the **Emwave device** and connect it to the laptop via the appropriate interface.

---

# Phase 2: GNSS Streaming and Logging

In this phase, you will run GNSS-related applications on the Axon via SSH, and use your laptop to log data from the Emwave device.

### ✅ Step 1: Configure UART for GNSS

- Confirm that the **red switch on Axon is slid away from the UART port**.  
  This ensures the **UART port is available for the GNSS module**.

### ✅ Step 2: Prepare SSH Sessions

You will need **two MobaXterm SSH sessions** open:

1. One for running `ublox_gnss_streamer`  
2. One for running `gnss_eval_tcp_client`

---

### ✅ Step 3: Run `ublox_gnss_streamer`

On the first SSH session, execute:
```
./ublox_gnss_streamer_aarch64 \
--serial-port /dev/ttyAMA0 \
--serial-baudrate 921600 \
--serial-timeout 1.0 \
--ntrip-host ntrip.hi-rtk.io \
--ntrip-port 2101 \
--ntrip-mountpoint SNS_AUTO \
--ntrip-username sns \
--ntrip-password 1234 \
--tcp-host 0.0.0.0 \
--tcp-port 5000 \
--logger-level info
```

> 📡 This command streams GNSS data from `/dev/ttyAMA0` and publishes it on a TCP server at port `5000`.

**Note:**  
- You should wait until the **RTK status is achieved** before continuing.
- You can check RTK lock via the RTK indicator light (e.g. for F9K modules, RTK light off means fix).

---

### ✅ Step 4: Run `gnss_eval_tcp_client`

In the second SSH session, run the following:

```
./gnss_eval_tcp_client_aarch64 \
--tcp-host 127.0.0.1 \
--tcp-port 5000 \
--eval-hz 120 \
--log-enable
```

> 🗂️ This client receives GNSS data from the TCP server and logs it to the `.gnss` folder. You can verify the logs from MobaXterm.

---

### ✅ Step 5: Run Logger for Emwave Device on Laptop

On your **laptop**, open the **VS Code workspace** that contains the `emtl30kr_gnss_logger`.

Then run this Python module to start logging:

```
python3 -m emtl30klr_gnss_logger.main -l info --log-to-file auto -p COM11
```

> 🧪 This starts GNSS logging for the Emwave device using the specified workspace setup.

---

### ✅ Step 6: Compare Logged Data

- After logging is complete:
  - Locate the `.gnss` log files saved by `gnss_eval_tcp_client` (check in MobaXterm).
  - **Download these logs to your laptop**.
  - Place them inside the VS Code workspace used by `emtl30kr_gnss_logger` for comparison and validation.

---

## ✅ Summary

- Make sure the switch positions are correct for either **UART communication** or **GNSS streaming**.
- Use **two SSH sessions** for parallel GNSS data streaming and evaluation.
- Always verify that RTK fix is achieved before evaluating or comparing GNSS data.

If you face any connectivity, logging, or access issues, recheck cable connections, switch positions, and IP configurations.
