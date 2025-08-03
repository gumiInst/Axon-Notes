## Technical Report: Boot Loop Issue Caused by Damaged Serial Input Line (2025/08/03)

### 1. Overview
This report details the investigation and resolution of a persistent boot loop problem observed on a board where UART0 is shared between a GNSS module and the AXON board’s serial terminal. The root cause was ultimately traced to a damaged serial input line.

### 2. Problem Description

The board exhibited frequent instability during startup, repeatedly failing to complete its boot sequence and entering continuous boot loops.

### 3. System Configuration

The system’s UART0 port is shared by the GNSS module and the AXON serial terminal through two hardware switches—one controlling the UART0 input source and the other controlling its output destination.

### 4. Root Cause Hypothesis

It was hypothesized that, during boot, UART0 was receiving unwanted signals (such as noise, interference, or corrupted data) from the serial terminal line. These signals were suspected to disrupt the normal boot process, causing the device to restart repeatedly.

### 5. Experimental Verification

**Test Procedures and Results:**

- **Test 1:**  
    - UART0 input switched to the GNSS module line (with the GNSS module powered off, making the line idle).
    - UART0 output switched to the serial terminal line.
    - **Result:** Device booted successfully.

- **Test 2:**  
    - UART0 input switched to the serial terminal line; output unchanged.
    - **Result:** Device remained stuck in the boot loop.

These tests confirmed that the boot loop issue only occurred when the UART0 input was connected to the serial terminal line.

### 6. Further Investigation

Physical inspection of the serial input line from the Type-C port to the switch revealed:
- A segment of the line was tightly wedged between the PCB and a screw.
- The insulation at this spot appeared damaged, exposing the conductor.

This damage likely allowed electrical noise or interference to couple onto the line, degrading signal integrity and causing the observed boot loop behavior.

### 7. Corrective Action

- The damaged segment of the serial input line was carefully removed from the wedged position.
- Upon doing so, the board booted normally, and the boot loop problem was resolved.

### 8. Conclusion

The boot loop was caused by interference or corruption of UART0 input signals due to physical damage (broken insulation) on the serial input line. This made the line sensitive to external noise and disruption, which in turn caused the system’s instability during boot. Once the cable was freed from the stuck position, operation normalized.

### 9. Recommendation

For long-term reliability:
- **Replace** the damaged serial input wire with a new, properly insulated cable.
- **Ensure** the new wire is routed away from potential pinch points, sharp edges, and screw holes.
- **Secure** all signal cables during assembly to prevent similar physical damage.
- **Inspect** related wiring in production units to proactively address possible latent faults.

By implementing these recommendations, the risk of recurrence or related signal integrity issues will be significantly reduced.
