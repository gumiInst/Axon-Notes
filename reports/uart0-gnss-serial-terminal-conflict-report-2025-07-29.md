# Technical Report: UART0 Serial Port Conflict between GNSS Module and Serial Terminal (2025/07/29)

## Issue Description

- The GNSS receiver on `/dev/ttyAMA0` reports repeated “invalid checksum” errors, indicating garbled or incomplete data.
- Error messages such as “device reports readiness to read but returned no data” are observed, signifying unreliable data access through the serial port.
- These issues persist even when a mechanical switch physically connects the UART exclusively to the GNSS module and disconnects the Linux serial terminal.

## Root Cause

- Both the GNSS software and the serial terminal (managed by the `serial-getty` service) are configured for access to UART0 (`/dev/ttyAMA0`).
- When a mechanical switch is used:
    - **Software State Not Synchronized:** The Linux operating system and `serial-getty` service are unaware of the hardware switch. If `serial-getty` is still running, it maintains control of `/dev/ttyAMA0` at the OS level, even if its physical connection is interrupted.
    - **Resource Conflicts:** Simultaneous or lingering software access from both the GNSS application and serial terminal service leads to port conflicts, device contention, and data corruption.

## Limitations of Mechanical Switching

- **Signal Glitches and Data Corruption:** Mechanical switching while the system is powered introduces transients, bounce, or disconnects, corrupting transmitted UART data.
- **Software/Hardware State Mismatch:** The operating system continues to believe the serial terminal is connected unless the service is explicitly stopped. Switching only the wiring does not free the port in software.
- **Concurrent Device File Locks:** If the serial terminal service has not relinquished the port at the software level, both applications may attempt to access the UART, causing unpredictable behavior and persistent errors.
- **Driver/Buffering Issues:** UART drivers may buffer data or expect stable connections. Rapid or unsynchronized switching leaves stale buffers and indeterminate signal state, leading to persistent communication errors.

## Solution

- For reliable and exclusive GNSS module access to UART0, stop the serial terminal (`serial-getty`) at the software level before running GNSS applications:
```
sudo systemctl stop serial-getty@ttyAMA0.service
```
- Once GNSS operations are finished, restore the serial terminal service:
```
sudo systemctl start serial-getty@ttyAMA0.service
```
- This process ensures exclusive access at both the hardware and software level, eliminating contention, data corruption, and access errors. The configuration is temporary—upon system reboot, the serial terminal service restarts automatically.



