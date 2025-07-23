# 📝 Network Debug Report – Linux Custom Device (2025/07/23)

## 📌 1. Issue Description

The device runs a custom Linux OS with a static IP address `192.168.10.10`. The issue is described as follows:

- The device **does not respond to ping from other devices** on the same network.
- **SSH is intermittently available** after multiple reboots; sometimes it works, sometimes it doesn’t.
- `arping` from another device returns the correct MAC address of `192.168.10.10`, confirming the device exists on the LAN.
- Using `nc` (netcat) confirms that port 22 is open and the device responds with the SSH banner (`SSH-2.0-OpenSSH_8.9`).
- The unusual behavior observed: **as soon as `tcpdump -i eth0 icmp` is run on the device, pings from outside start working.**
- Likewise, when enabling promiscuous mode with `ip link set eth0 promisc on`, ping **begins to work normally without tcpdump**.

---

## 🔍 2. Suspects Examined and Eliminated

All the following factors were considered, tested, and ruled out as root causes during the investigation:

| Component Checked               | Result / Status                                     | Ruled Out |
|--------------------------------|-----------------------------------------------------|-----------|
| **Static IP Configuration**    | Correct: `192.168.10.10/24` assigned ✅              | ✅        |
| **eth0 interface**             | Interface up, cable connected, `LOWER_UP` ✅        | ✅        |
| **Route / Gateway**            | Corrected to have only 1 default via `eth0`         | ✅        |
| **`sshd` service**             | Running and responding on port 22 ✅                | ✅        |
| **iptables firewall**          | All policies `ACCEPT`, no blocking rules            | ✅        |
| **`icmp_echo_ignore_all`**     | Value = `0` (ICMP allowed via sysctl)               | ✅        |
| **Reverse Path Filter (`rp_filter`)** | Set to `0`, still no change                     | ✅        |
| **ebtables filtering**         | Not installed or supported on system               | ✅        |
| **eBPF/XDP filtering**         | Not directly observed, but considered likely cause | ⚠️ Suspected |
| **Client-side ping config**    | Confirmed in same subnet; no ICMP blocked locally   | ✅        |
| **Switch/router blocking**     | No signs of ICMP filtering at layer 2/3             | ✅        |

---

## ✅ 3. Final Root Cause & Explanation

### 🔹 **Root Cause:**
**The network interface driver (or network stack) does not properly process incoming ICMP packets when the `eth0` interface is in normal (non-promiscuous) mode.**

### 🔹 **Explanation:**

- Enabling promiscuous mode causes the network card to accept all traffic, not just those addressed to its MAC.
- This indicates the NIC driver or kernel-level network stack **fails to hand certain packets (like ICMP) upstream**, unless a raw socket (tcpdump) or promiscuous mode is enabled.
- The fact that SSH (`TCP port 22`) works means **it's not a full networking break**, but specific to how the system filters or ignores some packet types.

---

## 💡 4. Recommended Solutions

### ✅ Temporary Workarounds:

| Solution                          | Instructions / Description                                      |
|----------------------------------|-----------------------------------------------------------------|
| 🛠 **Enable promiscuous mode manually** | `sudo ip link set dev eth0 promisc on`                       |
| 🛠 **Enable at boot**             | Add the above line to `/etc/rc.local` or create a systemd service |

<details>
<summary>Sample /etc/rc.local</summary>

#!/bin/sh -e
ip link set dev eth0 promisc on
exit 0


</details>

---

### 📈 Long-Term Solutions:

| Recommended Fixes                            | Notes                                                        |
|----------------------------------------------|--------------------------------------------------------------|
| 🧰 **Update or replace NIC driver**           | Switch to a verified kernel module or upgrade kernel         |
| 🔧 **Inspect and remove eBPF/XDP filters**    | Run `ip link show eth0` to check; use `ip link set dev eth0 xdp off` |
| 🧠 **Report bug to vendor or maintainer**     | Especially if using a board with custom SoC Linux builds     |

---

## ✅ 5. Final Conclusion

This custom Linux device **only responds to ping when `promiscuous mode` is enabled**, suggesting a deficiency or bug in the network driver or kernel stack that prevents ICMP packets from being processed normally under standard configurations.

- **This is a known driver-level or hardware-layer issue**, especially on some embedded platforms or custom builds.
- **SSH continued to function** throughout, confirming only some protocols were affected.
- **Temporary solution** such as enabling promiscuous mode works and may be sufficient for non-critical environments.
- For production stability, however, **updating drivers or switching to stable hardware/kernel is strongly advised.**

---

If you'd like assistance writing a systemd service to enable promiscuous mode and start `sshd` at boot, feel free to ask. We can provide a ready-to-go `.service` file.
