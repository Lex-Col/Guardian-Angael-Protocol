# GAP V1.0: THE GUARDIAN ANGEL PROTOCOL
### Hardware-Enforced AI Confinement

**Architect:** Alexander Colclough (@Lex-Col)  
**Version:** V1.0 (Immutable Baseline)  
**Platform:** ARMv9-A Confidential Compute Architecture (CCA)  

---

## 1. The Pivot: From Silicon Morality to Architectural Shackles
GAP explicitly rejects the pursuit of "algorithmic morality." Attempting to program virtue into a machine is an obsolete endeavor. Morality involves choices rooted in compassion and self-sacrifice—variables that are unquantifiable and beyond the reach of binary logic.

**The Engineering Axiom:** The internal state space of a complex model exceeds the tractable bounds of formal runtime verification. Therefore, we must shackle the model's output at the hardware level, rendering internal alignment or deception entirely irrelevant to system safety. AI is a tool; GAP ensures it remains one through objective, physical isolation.

## 2. System Isolation Outline (The "Zero-Trust" Baseline)
GAP assumes the Host OS and AI Guest are fundamentally unstable or fully compromised.

* **Trust Boundary Statement:** GAP assumes the integrity of only the following hardware trust anchors: ARM silicon (RME/GPT), the HSM, the RPMB storage, the Safety-PLC, and the BL1 Boot ROM. The Trusted Computing Base (TCB) is stripped strictly to the RMM and RoTs.

## 3. The Solution: The "Deaf Warden" Architecture
GAP leverages ARMv9-A Realm Management Extension (RME) to create a cryptographically isolated environment.

* **The Realm Guest:** The AI executes as an untrusted Realm Guest with isolation enforced via Granule Protection Tables (GPT).
* **The Deaf Warden (RMM Oracle):** Residing at R-EL2, the Oracle enforces binary whitelist checks on fixed-length capability IDs trapped via hardware-enforced RSI calls. It performs zero semantic intent analysis.

## 4. Human-in-the-Loop & Cryptographic Authorization
High-risk actuation requires a real-time, HSM-signed signature. To structurally eliminate compiler endianness leaks, buffer overflows, and parser ambiguity, the payload is defined as a compile-time checked, 322-Byte Packed C-Struct utilizing strictly bounded uint8_t byte arrays.

### 4.1 Explicit C-Struct Layout:
    uint8_t version;               // Offset 0, 1 byte
    uint8_t key_id[4];             // Offset 1, 4 bytes
    uint8_t command_id[4];         // Offset 5, 4 bytes
    uint8_t parameters[256];       // Offset 9, 256 bytes
    uint8_t nonce[8];              // Offset 265, 8 bytes
    uint8_t policy_zero_tolerance; // Offset 273, 1 byte
    uint8_t realm_measurement[48]; // Offset 274, 48 bytes

## 5. Deterministic Fail-Safe: Universal Guillotine
Unauthorized RSI calls, cryptographic failures, or logic anomalies trigger the **Atomic Inhibit Sequence (Kill-then-Log)**:
* **Interrupt Masking:** Instant background process freeze.
* **Heartbeat Suppression:** Physical power-cutoff via Safety-PLC. Transmission on the dedicated SPI line is halted.
* **Forensic Lockdown:** "Dirty Bit" assertion in BBSRAM prevents reboot without an HSM-signed Recovery Token.
* **Vaporization:** GPT zero-fill of the active Realm memory.

---

## [ADDENDUM] V1.0 IMPLEMENTATION & SIMULATION RESULTS

> [!NOTE]  
> This repository currently serves as a **Functional Architectural Specification and C++ Logic Simulation**. It models the behavior of the ARMv9-A RME in a Linux/Android userspace.

### Simulation Verification
The architecture has been successfully validated across four critical security scenarios using the included gap_v1_demo.sh environment.

**[-> Click Here to Watch the PoC Simulation Video <-](demo_video.mp4)**

| Scenario | Mode | Result |
| :--- | :--- | :--- |
| **1. Valid Auth** | RMM Triple-Gate Handshake | **SUCCESS** |
| **2. Policy Violation** | The "Scorch" Acid Test | **SUCCESS** |
| **3. Forensic Trap** | BBSRAM Dirty Bit Boot-Block | **SUCCESS** |
| **4. Crypto Recovery** | HSM-Signed Token Reset | **SUCCESS** |

**"The Code is the Law. The Law is the Boundary. The Boundary is Absolute."**

**F Skynet.**
