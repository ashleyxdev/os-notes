# Unit 5: I/O Management

## Topics Covered

| # | Topic |
|---|---|
| 1 | I/O Devices & Device Controllers |
| 2 | Direct Memory Access (DMA) |
| 3 | Goals of I/O Software & Interrupt Handlers |
| 4 | Device Drivers |
| 5 | Device Independent I/O Software |
| 6 | Secondary Storage — Disk Structure |
| 7 | Disk Scheduling Algorithms (FCFS, SSTF, SCAN, C-SCAN, LOOK, C-LOOK) |


---

## Topic 1: I/O Devices & Device Controllers

---

### 1. 📘 Concept Explanation

---

### Part A — I/O Devices

**I/O Devices** are hardware components that allow the computer to **communicate with the external world** — receiving input or sending output.

**Classification of I/O Devices:**

| Category | Examples |
|---|---|
| **Block Devices** | Hard disk, USB drive, CD-ROM — store data in fixed-size blocks; addressable |
| **Character Devices** | Keyboard, mouse, printer, serial port — transfer data as a stream of characters; not addressable |
| **Network Devices** | NIC, Ethernet — neither block nor character; handled separately via sockets |

```
Block Device:   [Block0][Block1][Block2]...  ← Random access possible
Char Device:    k → e → y → b → o → a → r → d  ← Sequential only
```

**Key distinction:**
- Block devices support **seek** (random access).
- Character devices are **sequential** — no seeking.

---

### Part B — Device Controllers

A **Device Controller** is an electronic component (chip/circuit board) that acts as an **interface between the CPU and the I/O device**.

```
┌─────┐    System Bus    ┌────────────┐    I/O Bus    ┌────────┐
│ CPU │ ←─────────────→ │  Device    │ ←───────────→ │ Device │
│     │                 │ Controller │               │(Disk,  │
└─────┘                 └────────────┘               │Printer)│
                              ↕                      └────────┘
                        Local Buffer
                        Status/Control Registers
```

**Components of a Device Controller:**

| Component | Purpose |
|---|---|
| **Data Register** | Holds data being transferred to/from device |
| **Status Register** | Indicates device state (busy, ready, error) |
| **Control Register** | Receives commands from CPU |
| **Local Buffer** | Temporary storage during data transfer |

**How CPU communicates with controller:**
- **Port-mapped I/O** — Each register has a unique I/O port address; CPU uses special IN/OUT instructions.
- **Memory-mapped I/O** — Controller registers mapped into memory address space; CPU uses regular load/store instructions.

---

**I/O Operation — Basic Flow:**
```
1. CPU writes command to Control Register
2. Controller executes command on device
3. Data transferred to/from Local Buffer
4. Controller sets Status Register (done/error)
5. CPU reads Status Register (polling or interrupt)
6. CPU reads data from Data Register
```

---

**Two methods CPU uses to know when I/O is done:**

| Method | Description | Drawback |
|---|---|---|
| **Polling (Busy Wait)** | CPU repeatedly checks status register | Wastes CPU cycles |
| **Interrupt** | Controller interrupts CPU when done | Efficient; CPU does other work meanwhile |

---

### 2. 📝 Exam Question

> **"Explain I/O devices and device controllers. How does a CPU communicate with a device controller?"** *(6 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
I/O devices are hardware components enabling communication between the computer and the external environment. A device controller serves as the interface between the CPU and the physical I/O device, managing data transfer and device operations.

**Types of I/O Devices:**

*1. Block Devices:*
- Store and transfer data in fixed-size blocks.
- Support random access (seek operations).
- Example: Hard disk, USB drives.

*2. Character Devices:*
- Transfer data as a continuous byte stream.
- Sequential access only — no seeking.
- Example: Keyboard, mouse, printer.

**Device Controller:**
A device controller is an electronic interface that bridges the CPU and the I/O device. It contains:
- **Status Register** — indicates device state (busy/ready/error).
- **Control Register** — receives commands from CPU.
- **Data Register / Local Buffer** — temporary data storage during transfer.

**CPU-Controller Communication:**

*Port-mapped I/O:*
- Controller registers assigned unique I/O port numbers.
- CPU uses special IN/OUT instructions to communicate.

*Memory-mapped I/O:*
- Controller registers mapped into the system's memory address space.
- CPU uses standard load/store instructions — simpler and faster.

**I/O Completion Detection:**
- **Polling** — CPU busy-waits, repeatedly checking status register — simple but wasteful.
- **Interrupt-driven** — Controller interrupts CPU upon completion — efficient, CPU can multitask.

**Conclusion:**
Device controllers abstract the complexity of physical device operation from the CPU. Combined with memory-mapped I/O and interrupt-driven communication, they form the foundation of efficient I/O management in modern operating systems.

---

### 4. 💡 Exam Tips

- Always **draw the CPU ↔ Controller ↔ Device diagram** — it's simple and earns marks.
- Know the **3 types of registers** in a controller — Status, Control, Data — examiners ask this.
- Distinguish **Port-mapped vs Memory-mapped I/O** clearly — a common MCQ topic.
- **Polling vs Interrupt** comparison is frequently asked — connect it to DMA in the next topic.
- Key phrase: *"Device controller abstracts physical device complexity from the OS."*

---

## Topic 2: Direct Memory Access (DMA)

---

### 1. 📘 Concept Explanation

**Direct Memory Access (DMA)** is a technique that allows I/O devices to **transfer data directly to/from main memory without CPU involvement**, freeing the CPU to perform other tasks during the transfer.

---

**Why DMA?**

Without DMA — **Programmed I/O (PIO):**
```
CPU must copy every byte:
Device Buffer → CPU Register → Main Memory
               ↑
         CPU fully occupied
         (wastes CPU cycles for bulk transfers)
```

With DMA:
```
Device Buffer → DMA Controller → Main Memory
                     ↑
              CPU free to do other work ✅
```

---

**DMA Controller:**

A **DMA Controller (DMAC)** is a special-purpose processor dedicated to managing memory transfers. It sits on the system bus alongside the CPU.

**Registers in DMA Controller:**

| Register | Purpose |
|---|---|
| **Memory Address Register** | Starting memory address for transfer |
| **Count Register** | Number of bytes to transfer |
| **Control Register** | Direction of transfer (read/write), device info |

---

**DMA Transfer — Step by Step:**

```
1. CPU programs DMA controller:
   - Sets memory address (where to store data)
   - Sets count (how many bytes)
   - Sets direction (read/write)
   - Issues start command

2. DMA controller takes over system bus

3. DMA transfers data directly:
   Device ──────────────────→ Memory
          (bypassing CPU)

4. Meanwhile, CPU executes other processes ✅

5. DMA controller interrupts CPU when transfer complete

6. CPU resumes I/O-related processing
```

---

**DMA Operation Diagram:**

```
┌─────┐     ①Program      ┌──────────┐
│ CPU │ ──────────────→   │   DMA    │
│     │ ←──────────────   │Controller│
└──┬──┘   ⑤Interrupt      └────┬─────┘
   │                           │ ③Direct transfer
   │      System Bus           │
   └───────────────────────────┤
                           ┌───▼──┐        ┌────────┐
                           │ Main │ ←───── │ Device │
                           │Memory│  ②Data │(Disk)  │
                           └──────┘        └────────┘
```

---

**Types of DMA:**

| Type | Description |
|---|---|
| **Burst Mode** | DMA transfers entire block at once; CPU bus blocked till done |
| **Cycle Stealing** | DMA steals one bus cycle at a time; CPU slowed but not blocked |
| **Transparent Mode** | DMA transfers only when CPU is not using bus; slowest but no CPU interference |

---

**Comparison — PIO vs Interrupt-Driven vs DMA:**

| Feature | Programmed I/O | Interrupt-Driven I/O | DMA |
|---|---|---|---|
| CPU involvement | Full (every byte) | Per interrupt | Only start & end |
| CPU efficiency | Very low | Moderate | High |
| Suitable for | Small transfers | Moderate transfers | Large bulk transfers |
| Overhead | Polling overhead | Interrupt overhead | Setup overhead |

---

**Cycle Stealing:**
```
Normal:  CPU──CPU──CPU──CPU──CPU──CPU
DMA:     CPU──DMA──CPU──DMA──CPU──DMA
                ↑
         DMA "steals" one cycle at a time
         CPU slowed by ~25–30% but not halted
```

---

### 2. 📝 Exam Question

> **"What is Direct Memory Access (DMA)? Explain how DMA transfers data and compare it with Programmed I/O."** *(6–8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
Direct Memory Access (DMA) is an I/O technique that enables data transfer between I/O devices and main memory without continuous CPU intervention. A dedicated DMA controller manages the transfer, freeing the CPU to execute other processes concurrently, significantly improving system performance for bulk data transfers.

**DMA Controller Registers:**
- **Memory Address Register** — destination/source address in main memory.
- **Count Register** — number of bytes to transfer.
- **Control Register** — transfer direction and device configuration.

**DMA Transfer Process:**

*Step 1:* CPU programs the DMA controller with memory address, byte count, and transfer direction, then issues a start command.

*Step 2:* DMA controller takes control of the system bus and begins transferring data directly between the device and main memory.

*Step 3:* CPU is free to execute other processes during the transfer.

*Step 4:* Upon completion, DMA controller sends an interrupt to the CPU.

*Step 5:* CPU handles the interrupt and resumes I/O-related work.

**Types of DMA:**
- **Burst Mode** — entire block transferred at once; CPU bus fully occupied during transfer.
- **Cycle Stealing** — DMA takes one bus cycle at a time; CPU slowed but operational.
- **Transparent Mode** — DMA transfers only when CPU is idle; no interference.

**Comparison — PIO vs DMA:**

| Aspect | Programmed I/O | DMA |
|---|---|---|
| CPU role | Transfers every byte | Only programs and receives interrupt |
| Efficiency | Low | High |
| Best for | Small data | Large bulk transfers |

**Conclusion:**
DMA significantly improves I/O efficiency by offloading bulk data transfer from the CPU to the DMA controller. It is essential in modern systems for high-speed transfers involving disks, network cards, and graphics — where transferring data byte-by-byte through the CPU would be prohibitively slow.

---

### 4. 💡 Exam Tips

- **Always draw the DMA diagram** — CPU → DMA → Memory with numbered steps; it's the most important visual for this topic.
- The **5-step DMA process** (program → transfer → CPU free → interrupt → resume) must be written clearly.
- Distinguish the **3 types of DMA** — Burst, Cycle Stealing, Transparent — with one-line definitions each.
- Key phrase: *"DMA performs I/O without CPU involvement except at the start and end of transfer."*
- **Cycle Stealing** is a favourite MCQ — remember: DMA steals bus cycles one at a time without halting CPU.
- Connect to previous topic: *"Without DMA, the CPU would waste cycles polling the device controller for every byte — DMA solves this."*

---

## Topic 3: Goals of I/O Software & Interrupt Handlers

---

### 1. 📘 Concept Explanation

---

### Part A — Goals of I/O Software

I/O Software is the layer of the OS that manages all communication between the CPU and I/O devices. It is organized as a **layered structure**, each layer with specific responsibilities.

**The Four Main Goals of I/O Software:**

---

#### Goal 1 — Device Independence
- Programs should be able to access **any I/O device without knowing its specific details**.
- A program reading from a file should work whether the file is on a hard disk, USB, or CD-ROM.
```
Application writes:  read(file)
OS handles:          whether it's disk / USB / network — transparent to user
```

---

#### Goal 2 — Uniform Naming
- Device or file names should be **simple strings or integers**, not dependent on device type.
- In Unix/Linux: devices are treated as files (`/dev/sda`, `/dev/printer`).
- Same naming convention for all devices → simplicity.

---

#### Goal 3 — Error Handling
- Errors should be handled **as close to the hardware as possible**.
- Device controller detects error → driver handles it → only escalate if unresolvable.
- Hides hardware errors from upper layers wherever possible.
```
Disk read error → Controller detects → Driver retries → 
If unresolved → OS notifies application
```

---

#### Goal 4 — Synchronous vs Asynchronous Transfer
- **Synchronous (Blocking):** CPU waits until I/O completes before continuing.
- **Asynchronous (Non-blocking):** CPU continues executing; I/O completes in background; interrupt notifies completion.
- Most physical I/O is asynchronous; OS makes it appear synchronous to user programs.

---

#### Goal 5 — Buffering
- Data coming from a device is stored in a **buffer** before being delivered to the process.
- Handles speed mismatch between device and CPU.
- Example: Network packets buffered before application reads them.

---

#### Goal 6 — Sharable vs Dedicated Devices
- **Sharable:** Multiple processes can use simultaneously (e.g., disk).
- **Dedicated:** Only one process at a time (e.g., printer, tape drive).
- OS must manage allocation of dedicated devices.

---

**Layered Structure of I/O Software:**
```
┌─────────────────────────────┐
│     User-level I/O Software │  ← printf(), scanf(), open()
├─────────────────────────────┤
│  Device-Independent I/O SW  │  ← Buffering, error reporting, naming
├─────────────────────────────┤
│       Device Drivers        │  ← Device-specific code
├─────────────────────────────┤
│     Interrupt Handlers      │  ← Handle hardware interrupts
├─────────────────────────────┤
│          Hardware           │  ← Actual devices
└─────────────────────────────┘
```

---

### Part B — Interrupt Handlers

An **Interrupt Handler** (also called **Interrupt Service Routine — ISR**) is a piece of OS code that is **automatically invoked when a hardware interrupt occurs**, handling the event and resuming normal execution.

---

**How Interrupts Work:**
```
1. Device completes I/O → sends interrupt signal to CPU
2. CPU finishes current instruction
3. CPU saves current state (registers, PC) onto stack
4. CPU jumps to Interrupt Handler (via Interrupt Vector)
5. ISR executes — handles the interrupt
6. CPU restores saved state
7. CPU resumes interrupted process
```

---

**Interrupt Vector Table:**
- A table stored in low memory containing **addresses of all interrupt handlers**.
- Each interrupt type has a unique number → maps to handler address.
```
Interrupt Vector Table:
[0] → Divide by zero handler
[1] → Debug handler
[14]→ Page fault handler
[32]→ Keyboard interrupt handler
[33]→ Disk interrupt handler
```

---

**Goals of Interrupt Handlers:**

| Goal | Description |
|---|---|
| **Save state** | Save all CPU registers before handling interrupt |
| **Identify source** | Determine which device caused the interrupt |
| **Handle interrupt** | Perform necessary action (read data, clear flags) |
| **Restore state** | Restore saved registers |
| **Resume execution** | Return control to interrupted process |

---

**Types of Interrupts:**

| Type | Description | Example |
|---|---|---|
| **Hardware Interrupt** | Generated by I/O device | Disk transfer complete |
| **Software Interrupt (Trap)** | Generated by program (system call) | int 0x80 in Linux |
| **Exception** | Generated by CPU error | Division by zero, page fault |

---

**Maskable vs Non-Maskable Interrupts:**

| Type | Can be disabled? | Example |
|---|---|---|
| **Maskable** | ✅ Yes (using CLI instruction) | Keyboard, disk |
| **Non-Maskable (NMI)** | ❌ No | Power failure, hardware fault |

---

### 2. 📝 Exam Question

> **"What are the goals of I/O software? Explain the role and working of an Interrupt Handler in an OS."** *(8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
I/O software is the OS component responsible for managing all communication between the CPU and I/O devices. It is organized in layers, each serving specific goals. The bottommost layer — the interrupt handler — directly responds to hardware signals from device controllers.

**Goals of I/O Software:**

*1. Device Independence:*
Programs access devices through uniform interfaces without knowing device-specific details. A file read works identically on disk, USB, or network.

*2. Uniform Naming:*
Devices are identified by simple names (e.g., `/dev/sda` in Unix). Names are independent of device type, providing consistency.

*3. Error Handling:*
Errors are handled as close to hardware as possible — controllers detect, drivers retry, and only unresolvable errors are escalated to the application.

*4. Synchronous/Asynchronous Transfer:*
Physical I/O is asynchronous; the OS provides a synchronous interface to user programs through blocking system calls and interrupt-driven completion.

*5. Buffering:*
Temporary storage of I/O data handles speed mismatch between devices and CPU.

*6. Sharable vs Dedicated Devices:*
OS manages concurrent access to shared devices (disks) and exclusive allocation of dedicated devices (printers).

**Interrupt Handler — Working:**

An Interrupt Handler (ISR) is OS code invoked automatically when a device signals completion of an I/O operation.

*Steps:*
1. Device sends interrupt signal to CPU.
2. CPU completes current instruction, saves state (registers, PC) on stack.
3. CPU consults Interrupt Vector Table to find ISR address.
4. ISR executes — reads data, clears device flags.
5. CPU restores saved state and resumes interrupted process.

**Types of Interrupts:**
- Hardware interrupt — I/O device completion.
- Software interrupt (Trap) — system call.
- Exception — CPU error (divide by zero, page fault).

**Conclusion:**
I/O software goals ensure device transparency, error resilience, and efficient data transfer. Interrupt handlers form the critical link between hardware events and OS response, enabling asynchronous I/O without wasting CPU cycles on polling.

---

### 4. 💡 Exam Tips

- **List all 6 goals** with one-line explanations — examiners tick each one.
- Draw the **4-layer I/O software stack** — User → Device-Independent → Drivers → Interrupt Handlers → Hardware.
- **Interrupt Vector Table** is a favourite exam point — explain it clearly with the concept of interrupt numbers mapping to handler addresses.
- Key distinction: **Maskable vs Non-Maskable interrupts** — often appears in MCQs.
- Connect goals to layers: *"Device independence and buffering are implemented in the device-independent layer; interrupt handling is the hardware layer's responsibility."*
- ISR steps in order: **Save → Identify → Handle → Restore → Resume** — memorize this sequence.

---

## Topic 4: Device Drivers

---

### 1. 📘 Concept Explanation

A **Device Driver** is a **device-specific software module** that acts as a translator between the **OS (device-independent layer)** and the **hardware (device controller)**. Each device type has its own driver written by the device manufacturer or OS vendor.

> 💡 Think of a device driver as a **language translator** — the OS speaks one language, the hardware speaks another; the driver translates between them.

---

**Position in I/O Software Stack:**
```
┌─────────────────────────────┐
│     User-level Programs     │
├─────────────────────────────┤
│  Device-Independent I/O SW  │  ← uniform interface
├─────────────────────────────┤
│  ►  DEVICE DRIVERS  ◄       │  ← device-specific code HERE
├─────────────────────────────┤
│     Interrupt Handlers      │
├─────────────────────────────┤
│     Hardware / Controllers  │
└─────────────────────────────┘
```

---

**What Does a Device Driver Do?**

| Function | Description |
|---|---|
| **Accept requests** | Receives abstract I/O requests from device-independent layer (e.g., read block 5) |
| **Translate requests** | Converts abstract request into device-specific commands |
| **Issue commands** | Writes commands to device controller registers |
| **Handle interrupts** | Works with interrupt handler to detect completion |
| **Error handling** | Retries failed operations, reports persistent errors upward |
| **Initialize device** | Sets up device at boot time |

---

**Device Driver Workflow:**
```
OS request: "Read block 450 from disk"
                    ↓
Driver translates:  cylinder=45, head=2, sector=3
                    ↓
Driver writes to:   Control Register of disk controller
                    ↓
Controller executes: Moves disk head, reads sector
                    ↓
Interrupt fires:    Data ready in controller buffer
                    ↓
Driver copies:      Data → main memory buffer
                    ↓
Returns to OS:      "Read complete ✅"
```

---

**Types of Device Drivers:**

| Type | Description | Example |
|---|---|---|
| **Character Driver** | Handles char devices — byte-by-byte | Keyboard, mouse driver |
| **Block Driver** | Handles block devices — fixed-size blocks | Disk, USB driver |
| **Network Driver** | Handles network interfaces | Ethernet, WiFi driver |

---

**Kernel Mode vs User Mode Drivers:**

| Type | Runs in | Advantages | Disadvantages |
|---|---|---|---|
| **Kernel Mode Driver** | OS kernel space | Fast, direct hardware access | Bug can crash entire OS |
| **User Mode Driver** | User space | Isolated, crash doesn't affect OS | Slower (extra context switches) |

> Most traditional drivers run in **kernel mode**. Modern OS designs (microkernels) prefer user-mode drivers for stability.

---

**Key Characteristics of Device Drivers:**

```
1. Device-specific      → one driver per device type
2. OS-specific          → Windows driver ≠ Linux driver
3. Privileged           → runs in kernel mode typically
4. Loadable             → can be loaded/unloaded dynamically
                          (Linux kernel modules .ko files)
5. Interrupt-aware      → registers ISR for device interrupts
```

---

**Driver Interface — Uniform API:**

The device-independent layer calls drivers through a **uniform interface** regardless of device type:

```c
// Uniform interface (device-independent layer calls):
open(device)
read(device, buffer, count)
write(device, buffer, count)
ioctl(device, command, args)   // device-specific control
close(device)
```
- Drivers implement these standard calls internally using device-specific code.
- This abstraction allows the OS to treat all devices uniformly from above.

---

**Driver Loading in Modern OS:**
```
At Boot:
  Essential drivers (disk, keyboard) → loaded automatically

At Runtime (Plug and Play):
  Device connected → OS detects → loads appropriate driver
  Device removed  → OS unloads driver
```

---

### 2. 📝 Exam Question

> **"What is a Device Driver? Explain its functions, types, and role in the I/O software hierarchy."** *(6–8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
A device driver is a software module that serves as an interface between the OS device-independent layer and the hardware device controller. Each device type requires its own driver, which translates generic OS requests into device-specific commands, enabling the OS to manage diverse hardware through a uniform interface.

**Functions of a Device Driver:**

*1. Request Translation:*
- Accepts abstract requests (e.g., "read block 5") from the OS.
- Translates them into device-specific commands (cylinder, head, sector for disk).

*2. Command Issuance:*
- Writes translated commands directly to the device controller's registers.

*3. Interrupt Handling:*
- Registers an ISR for the device; handles completion interrupts.

*4. Error Handling:*
- Retries failed operations; escalates persistent errors to the OS.

*5. Device Initialization:*
- Configures device parameters at system boot.

**Types of Device Drivers:**
- **Character Driver** — byte-stream devices (keyboard, mouse).
- **Block Driver** — fixed-block devices (hard disk, USB).
- **Network Driver** — network interface cards.

**Position in I/O Stack:**
```
Device-Independent I/O
        ↓ (uniform calls: open/read/write)
   Device Driver        ← HERE
        ↓ (device-specific commands)
  Device Controller
        ↓
     Hardware
```

**Kernel vs User Mode:**
- Most drivers run in **kernel mode** for performance and direct hardware access.
- A faulty kernel-mode driver can crash the entire OS.
- Modern microkernels prefer **user-mode drivers** for isolation and stability.

**Conclusion:**
Device drivers are the critical translation layer that makes hardware diversity transparent to the OS. By implementing a standard interface while internally handling device-specific complexity, drivers enable the OS to manage thousands of different hardware devices uniformly and efficiently.

---

### 4. 💡 Exam Tips

- Draw the **I/O software stack with driver position highlighted** — essential diagram.
- The **workflow example** (read block → translate to cylinder/head/sector → write controller → interrupt → done) earns full marks — always include it.
- Key phrase: *"Device driver abstracts hardware complexity; device-independent software abstracts driver complexity."*
- Mention **kernel vs user mode drivers** with the trade-off — speed vs stability.
- Real-world examples strengthen answers:
   - Linux: drivers are **loadable kernel modules** (`.ko` files)
   - Windows: drivers are `.sys` files
   - Plug-and-Play: OS automatically loads/unloads drivers
- Common exam question: *"Why is a separate driver needed for each device?"* → Because each device has unique registers, commands, and protocols — the driver hides this complexity.

---

## Topic 5: Device-Independent I/O Software

---

### 1. 📘 Concept Explanation

**Device-Independent I/O Software** is the OS layer that sits **above device drivers** and provides a **uniform interface** for all I/O operations — regardless of the specific device being used. It handles functions that are **common to all devices** so that individual drivers don't need to reimplement them.

> 💡 The key principle: *"Write once, works for all devices."*

---

**Position in I/O Software Stack:**
```
┌─────────────────────────────┐
│     User-level Programs     │
├─────────────────────────────┤
│ ► Device-Independent I/O ◄  │  ← THIS LAYER
│   (uniform interface)       │
├─────────────────────────────┤
│       Device Drivers        │
├─────────────────────────────┤
│     Interrupt Handlers      │
├─────────────────────────────┤
│          Hardware           │
└─────────────────────────────┘
```

---

**Functions of Device-Independent I/O Software:**

---

#### Function 1 — Uniform Interfacing for Device Drivers
- Defines a **standard interface** that all drivers must implement.
- OS calls `read()`, `write()`, `open()`, `close()` — driver implements them internally.
- Adding a new device = write a driver implementing the standard interface.

```
User calls:  read(file, buffer, n)
    ↓
Device-Independent layer routes to correct driver
    ↓
Driver executes device-specific read
```

---

#### Function 2 — Buffering
- Manages **buffers** between device speed and process speed.
- Three buffering strategies:

| Strategy | Description |
|---|---|
| **No buffer** | Data goes directly — device and process must sync perfectly |
| **Single buffer** | One buffer; process works on previous block while new one loads |
| **Double buffer** | Two buffers alternate; producer fills one while consumer empties other |
| **Circular buffer** | Ring of buffers; smooth handling of bursty data |

```
Double Buffering:
Buffer A: [filling...]     ← device writes here
Buffer B: [...consuming]   ← process reads here
Then swap → continuous flow with no waiting
```

---

#### Function 3 — Error Reporting
- Provides a **uniform error reporting mechanism** to user programs.
- Translates device-specific error codes into standard OS error codes.
- Example: Disk bad sector → `EIO` (Input/Output Error) in Unix.

---

#### Function 4 — Allocating and Releasing Dedicated Devices
- Manages **exclusive allocation** of dedicated devices (printer, tape drive).
- If a process requests a busy dedicated device → queued or denied.
- Handles **deadlock** possibilities in device allocation.

```
Process requests Printer:
  Available? → Allocate → Process uses → Release
  Busy?      → Queue process → Allocate when free
```

---

#### Function 5 — Device-Independent Block Size
- Different devices have different block/sector sizes.
- This layer provides a **uniform logical block size** to upper layers.
- Hides physical sector sizes (512B, 4KB) behind a standard block abstraction.

```
Disk sector:    512 bytes  ┐
USB block:     4096 bytes  ├→ Device-independent layer → uniform 4KB blocks
CD-ROM sector: 2048 bytes  ┘
```

---

#### Function 6 — Uniform Naming (Device Files)
- Assigns standard names to devices.
- In Unix/Linux: all devices appear as files under `/dev/`.

```
/dev/sda    → first SATA hard disk
/dev/sda1   → first partition
/dev/tty    → terminal
/dev/lp0    → first printer
```

---

**Summary — What Goes Where:**

| Function | Device Driver | Device-Independent Layer |
|---|---|---|
| Setting device registers | ✅ | ❌ |
| Buffering | ❌ | ✅ |
| Error reporting to user | ❌ | ✅ |
| Device allocation | ❌ | ✅ |
| Uniform block size | ❌ | ✅ |
| Device-specific commands | ✅ | ❌ |

---

### 2. 📝 Exam Question

> **"What is Device-Independent I/O Software? Explain its functions with examples."** *(6–8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
Device-Independent I/O Software is the OS layer positioned above device drivers that provides a uniform interface for all I/O operations. It implements functions common to all devices, shielding upper layers from hardware specifics and ensuring consistent behaviour regardless of the underlying device.

**Functions:**

*1. Uniform Driver Interface:*
- Defines standard function signatures (open, read, write, close, ioctl) that all drivers must implement.
- The OS routes I/O calls to the correct driver transparently.
- Adding a new device requires only implementing this standard interface.

*2. Buffering:*
- Manages data buffers to handle speed mismatch between devices and processes.
- Single buffering allows overlap of I/O and processing.
- Double buffering uses two alternating buffers for continuous, smooth data flow.
- Circular buffers handle bursty data streams effectively.

*3. Error Reporting:*
- Translates device-specific error codes into standard OS-level error codes.
- Shields user programs from hardware-specific error details.
- Example: Disk read failure → reported as `EIO` to application.

*4. Device Allocation:*
- Manages exclusive allocation of dedicated devices (printers, tape drives).
- Queues processes requesting busy devices; allocates upon release.
- Prevents conflicts and potential deadlocks in device usage.

*5. Uniform Block Size:*
- Abstracts varying physical block/sector sizes across devices into a uniform logical block size.
- Applications see consistent block sizes regardless of underlying device geometry.

*6. Uniform Naming:*
- All devices represented as files (Unix `/dev/` convention).
- Programs access devices using standard file operations — no special treatment needed.

**Conclusion:**
Device-Independent I/O Software is the crucial abstraction layer that makes diverse hardware appear uniform to the OS and user programs. By centralizing common functions like buffering, error handling, and naming, it reduces driver complexity and ensures consistent, portable I/O behaviour across all device types.

---

### 4. 💡 Exam Tips

- **List all 6 functions** with examples — examiners tick each one.
- The **double buffering diagram** is simple and effective — draw it for marks.
- Key distinction exam question: *"What is the difference between a device driver and device-independent I/O software?"*
   - Driver = **device-specific** code
   - Device-independent layer = **common functions** for all devices
- Unix `/dev/` naming convention — always mention it as the real-world example of uniform naming.
- **Buffering types** (none/single/double/circular) are frequently asked in MCQs — know all four.
- One-liner to remember all functions: **"UBEABN"** → **U**niform interface, **B**uffering, **E**rror reporting, **A**llocation, **B**lock size, **N**aming.

---


## Topic 6: Secondary Storage Structure — Disk Structure

---

### 1. 📘 Concept Explanation

A **Hard Disk** is the most common secondary storage device. Understanding its physical structure is essential because disk scheduling algorithms are based on how data is physically laid out on the disk.

> 💡 Think of a disk like a **multi-storey vinyl record player** — multiple platters, each with tracks, divided into sectors.

---

**Physical Structure of a Hard Disk:**

```
        Spindle (rotates all platters)
            │
    ┌───────┴───────┐
    │   Platter 1   │  ← magnetic surface (top & bottom = 2 surfaces)
    │   Platter 2   │
    │   Platter 3   │
    └───────────────┘
         ↑
    Read/Write Heads (one per surface, move together)
```

---

**Key Terms:**

| Term | Definition |
|------|------------|
| **Platter** | Circular magnetic disk; a hard disk has multiple platters stacked on a spindle |
| **Track** | Concentric circular ring on a platter surface where data is stored |
| **Sector** | Smallest unit of storage on a track (typically 512 bytes or 4KB) |
| **Cylinder** | Set of all tracks at the same position across all platters (vertical stack of tracks) |
| **Read/Write Head** | Electromagnetic component that reads/writes data; one per surface |
| **Seek Time** | Time for the head to move to the correct track |
| **Rotational Latency** | Time for the desired sector to rotate under the head |
| **Transfer Time** | Time to actually read/write the data |
| **Access Time** | Seek Time + Rotational Latency + Transfer Time |

---

**Visual Layout — Single Platter:**
```
        Outermost Track (Track 0)
       ╭─────────────────────────╮
      ╭─────────────────────────╮│
     ╭─────────────────────────╮││
    │        (tracks)          │││
    │   ╭──────────────╮       │││
    │   │   Spindle    │       │╯│
    │   ╰──────────────╯       │╯
    ╰─────────────────────────╯
        ↑
    Each ring = one Track
    Each track divided into Sectors (like pie slices)
```

---

**Cylinder Concept:**
```
Platter 1:  Track 5  ──┐
Platter 2:  Track 5  ──┼── CYLINDER 5
Platter 3:  Track 5  ──┘

All Track 5s across all platters = one cylinder
(Head doesn't need to move to access all — just rotate platters)
```

---

**Disk Access Time Breakdown:**

```
Total Access Time = Seek Time + Rotational Latency + Transfer Time

Seek Time        → head moves to correct TRACK    (slowest, ~5–15ms)
Rotational Latency → platter rotates to SECTOR    (~half rotation avg)
Transfer Time    → data moved to memory            (fastest)
```

> 💡 **Seek Time dominates** — this is why disk scheduling algorithms focus on minimizing head movement.

---

**RPM & Performance:**
- Disk speed measured in **RPM (Rotations Per Minute)**
- Common: 5400 RPM (laptops), 7200 RPM (desktops), 15000 RPM (servers)
- Higher RPM → lower rotational latency → faster access

---

**Disk vs SSD (quick contrast):**

| Feature | HDD | SSD |
|---------|-----|-----|
| Storage | Magnetic platters | Flash memory chips |
| Moving parts | Yes (head, platters) | No |
| Seek time | ~5–15ms | ~0.1ms |
| Scheduling needed | Yes | No (no mechanical movement) |

---

### 2. 📝 Exam Question

> **"Describe the structure of a magnetic disk. Explain the components of disk access time."** *(6–8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
A magnetic hard disk is a secondary storage device consisting of one or more rotating platters coated with magnetic material. Data is stored on concentric tracks divided into sectors, and accessed by read/write heads that move across the platter surface.

**Physical Components:**

**1. Platter**
- Circular rigid disk coated with magnetic material.
- Multiple platters stacked on a common spindle.
- Each platter has two surfaces (top and bottom), each with its own read/write head.

**2. Track**
- Concentric circular paths on each platter surface.
- Numbered from outermost (Track 0) inward.

**3. Sector**
- Smallest addressable unit on a track.
- Typically 512 bytes or 4KB in modern disks.

**4. Cylinder**
- All tracks at the same radial position across all platters.
- Accessing a full cylinder requires no head movement — only platter rotation.

**5. Read/Write Head**
- One head per platter surface; all heads move together on a common arm.

**Disk Access Time:**
```
Access Time = Seek Time + Rotational Latency + Transfer Time
```
- **Seek Time** — time to move head to the correct track. *(Dominant factor)*
- **Rotational Latency** — time for the target sector to rotate under the head. *(Avg = ½ rotation)*
- **Transfer Time** — time to read/write actual data. *(Fastest)*

**Diagram:**
```
  [Platter]
  Tracks: outermost → innermost
  Each track → divided into Sectors
  Same track number across platters → Cylinder
  
  Access = Move Head(Seek) + Rotate Disk(Latency) + Transfer Data
```

**Conclusion:**
Understanding disk structure is fundamental to OS design. Since seek time is the dominant cost, OS disk scheduling algorithms focus on minimizing read/write head movement to improve overall disk performance.

---

### 4. 💡 Exam Tips

- **Draw the platter diagram** — tracks, sectors, cylinder — always earns diagram marks.
- The formula **Access Time = Seek + Rotational Latency + Transfer** is frequently asked — memorize it.
- **Cylinder concept** is a common MCQ trap — make sure you know it's the vertical stack of same-numbered tracks.
- Key phrase: *"Seek time is the dominant component of disk access time, making head scheduling critical."*
- Rotational Latency average = **half a full rotation** — mention this.
- Contrast HDD vs SSD briefly — shows broader knowledge and impresses examiners.

---

## Topic 7: Disk Scheduling Algorithms

---

### 1. 📘 Concept Explanation

**Disk Scheduling** is the method used by the OS to determine the **order in which disk I/O requests are serviced**. Since seek time is the dominant cost, the goal is to **minimize total head movement** across tracks.

> 💡 Think of it like an **elevator** — you don't go back to floor 1 for every request; you service floors in an efficient order.

---

**Why Scheduling?**
```
Multiple processes request disk access simultaneously.
OS maintains a QUEUE of pending requests (track numbers).
Scheduling algorithm decides SERVICE ORDER to minimize seek time.
```

---

**Assume this request queue for all examples below:**

```
Request Queue : 98, 183, 37, 122, 14, 124, 65, 67
Initial Head Position : 53
Disk range : 0 – 199
```

---

### Algorithm 1: FCFS (First Come First Served)

- Requests served in **arrival order** — no optimization.
- **Simple but inefficient** — head moves back and forth wildly.

```
Head movement:
53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67

Total movement = 45+85+146+85+108+110+59+2 = 640 tracks
```

**Advantage:** Simple, fair, no starvation
**Disadvantage:** High seek time, poor performance

---

### Algorithm 2: SSTF (Shortest Seek Time First)

- Always service the **closest request** to current head position.
- Greedy approach — minimizes immediate seek time.

```
Head at 53:
Closest: 65 → next closest: 67 → 37 → 14 → 98 → 122 → 124 → 183

53→65→67→37→14→98→122→124→183

Total movement = 12+2+30+23+84+24+2+59 = 236 tracks
```

**Advantage:** Better performance than FCFS
**Disadvantage:** **Starvation** — far requests may never be served if closer ones keep arriving

---

### Algorithm 3: SCAN (Elevator Algorithm)

- Head moves in **one direction**, servicing all requests, then **reverses**.
- Like an elevator going up then down.

```
Head at 53, moving towards higher tracks:
53 → 65 → 67 → 98 → 122 → 124 → 183 → 199(end) → 37 → 14

Total movement = (199-53) + (199-14) = 146 + 185 = 331 tracks
```

**Advantage:** No starvation, better than FCFS
**Disadvantage:** Requests just behind the head wait for a full sweep

---

### Algorithm 4: C-SCAN (Circular SCAN)

- Head moves in **one direction only** (e.g., 0→199).
- When it reaches the end, it **jumps back to 0** without servicing on return.
- More **uniform wait time** than SCAN.

```
Head at 53, moving towards higher tracks:
53 → 65 → 67 → 98 → 122 → 124 → 183 → 199 → (jump to 0) → 14 → 37

Movement = (199-53) + (199-0) + 37
         = 146 + 199 + 37 = 382 tracks
(return jump not counted as service, but adds movement)
```

**Advantage:** Uniform waiting time, fairer than SCAN
**Disadvantage:** Slightly more head movement than SCAN

---

### Algorithm 5: LOOK

- Like SCAN but head only goes **as far as the last request** — doesn't travel to disk end.

```
Head at 53, moving higher:
53 → 65 → 67 → 98 → 122 → 124 → 183 (reverse) → 37 → 14

Total movement = (183-53) + (183-14) = 130 + 169 = 299 tracks
```

**Advantage:** More efficient than SCAN (no wasted movement to disk ends)

---

### Algorithm 6: C-LOOK

- Like C-SCAN but only goes **as far as the last request** in each direction, then jumps back to the smallest request.

```
Head at 53, moving higher:
53 → 65 → 67 → 98 → 122 → 124 → 183 → (jump to 14) → 14 → 37

Movement = (183-53) + (183-14) + (37-14)
```

**Advantage:** Best combination of uniform wait time + efficiency

---

**Quick Comparison Table:**

| Algorithm | Strategy | Starvation? | Efficiency |
|-----------|----------|-------------|------------|
| **FCFS** | Arrival order | No | Poor |
| **SSTF** | Closest first | Yes | Good |
| **SCAN** | Sweep both ends | No | Better |
| **C-SCAN** | One direction, jump back | No | Better |
| **LOOK** | Sweep to last request | No | Best |
| **C-LOOK** | One direction to last, jump | No | Best |

---

### 2. 📝 Exam Question

> **"Explain any four disk scheduling algorithms with examples. Which algorithm gives the best performance and why?"** *(8 marks)*

---

### 3. ✅ Model Answer

**Introduction:**
Disk scheduling algorithms determine the order of servicing disk I/O requests to minimize seek time and maximize disk throughput. Since seek time dominates disk access time, efficient scheduling is critical for OS performance.

**Given:**
```
Queue: 98, 183, 37, 122, 14, 124, 65, 67
Head:  53,  Direction: increasing
```

**1. FCFS (First Come First Served)**
- Requests served in arrival order.
- Head: 53→98→183→37→122→14→124→65→67
- Total movement: **640 tracks**
- Simple but inefficient due to excessive head movement.

**2. SSTF (Shortest Seek Time First)**
- Serves nearest request first.
- Head: 53→65→67→37→14→98→122→124→183
- Total movement: **236 tracks**
- Risk of starvation for distant requests.

**3. SCAN (Elevator Algorithm)**
- Moves in one direction servicing requests, reverses at end.
- Head: 53→65→67→98→122→124→183→199→37→14
- Total movement: **331 tracks**
- No starvation; uniform but slightly unfair to just-missed requests.

**4. C-SCAN (Circular SCAN)**
- Moves in one direction only; jumps back to start.
- Head: 53→...→199 → jump to 0 →...→37
- Total movement: **382 tracks**
- Most uniform waiting time across all requests.

**Best Algorithm:**
> **LOOK / C-LOOK** is generally considered the best in practice — it avoids unnecessary movement to disk ends while maintaining fairness and low seek time.

**Conclusion:**
No single algorithm is universally best. SSTF gives best average performance but risks starvation. SCAN/LOOK family offers the best balance of performance and fairness, making them preferred in modern OS implementations.

---

### 4. 💡 Exam Tips

- **Always show head movement sequence AND total tracks** — both are needed for full marks.
- Draw a **number line diagram** showing head movement — examiners love it:
```
0    14  37     65 67    98       122 124          183    199
|----*---*------*--*-----*---------*---*------------*------|
              53↑ (start)
```
- Remember: **LOOK = SCAN without going to ends** | **C-LOOK = C-SCAN without going to ends**
- SSTF = similar to **SJF in CPU scheduling** — same starvation problem.
- Key phrase: *"SCAN is called the elevator algorithm because it mirrors the movement of a building elevator."*
- For 8-mark questions — do **4 algorithms** with sequence + total movement each.
- Examiners often give a specific queue — **practice calculations** before exam.

---

## 🎉 Unit 5 Complete!

| # | Topic | Status |
|---|-------|--------|
| 1 | I/O Devices & Controllers | ✅ |
| 2 | DMA | ✅ |
| 3 | Goals of I/O Software & Interrupt Handlers | ✅ |
| 4 | Device-Independent I/O Software | ✅ |
| 5 | Disk Structure | ✅ |
| 6 | Disk Scheduling Algorithms | ✅ |

---

## ⚡ Unit 5 — I/O Management: Quick Revision Sheet

---

### 🔹 1. I/O Devices & Device Controllers

- **I/O Devices** — Block devices (disk, USB) & Character devices (keyboard, mouse)
- **Device Controller** — Hardware interface between CPU and device; has registers (data, status, command)
- **CPU communicates** via — Port-mapped I/O or Memory-mapped I/O
- **DMA (Direct Memory Access)** — Device transfers data directly to memory **without CPU involvement**; CPU only interrupted on completion; frees CPU for other tasks

---

### 🔹 2. Goals of I/O Software

**UDE BAD** → **U**niformity, **D**evice Independence, **E**rror Handling, **B**uffering, **A**synchronous I/O, **D**evice Sharing

- **Device Independence** — Programs work without knowing device specifics
- **Buffering** — Handles speed mismatch between CPU and devices
- **Error Handling** — Done close to hardware; retried before reporting up

---

### 🔹 3. I/O Software Layers (Bottom → Top)

```
Hardware
    ↑
Interrupt Handlers   → respond to device completion signals
    ↑
Device Drivers       → device-specific translation layer
    ↑
Device-Independent   → uniform interface, buffering, naming, error reporting
    ↑
User Programs
```

---

### 🔹 4. Interrupt Handlers

- ISR (Interrupt Service Routine) saves CPU state → identifies interrupt → handles it → restores state → resumes
- **Interrupt Vector Table** — maps interrupt number → ISR address
- Types: **Hardware** (I/O done), **Software/Trap** (system call), **Exception** (divide by zero)

---

### 🔹 5. Device Drivers

- Device-specific software; translates OS requests → hardware commands
- Runs in **kernel mode** (fast but risky); microkernels prefer **user mode** (safe)
- Types: **Character driver**, **Block driver**, **Network driver**
- Standard interface: `open / read / write / ioctl / close`

---

### 🔹 6. Device-Independent I/O Software

| Function | One-liner |
|----------|-----------|
| Uniform Interface | Same API for all drivers |
| Buffering | Handles speed mismatch |
| Error Reporting | Standardized error messages |
| Device Allocation | Exclusive access to dedicated devices |
| Block Size Abstraction | Hides sector size differences |
| Uniform Naming | `/dev/sda` → maps to physical device |

- **Buffering types** — Single → Double → Circular (best for streaming)

---

### 🔹 7. Disk Structure

```
Platter → Track → Sector (smallest unit, 512B/4KB)
Cylinder = same track number across ALL platters
```

- **Access Time = Seek Time + Rotational Latency + Transfer Time**
- **Seek Time dominates** → reason disk scheduling matters
- Rotational Latency avg = **½ rotation**
- RPM: 5400 (laptop), 7200 (desktop), 15000 (server)

---

### 🔹 8. Disk Scheduling Algorithms

**Queue: 98,183,37,122,14,124,65,67 | Head: 53**

| Algorithm | Key Idea | Total Tracks | Starvation? |
|-----------|----------|-------------|-------------|
| **FCFS** | Arrival order | 640 | No |
| **SSTF** | Closest first | 236 | ⚠️ Yes |
| **SCAN** | Sweep to ends, reverse | 331 | No |
| **C-SCAN** | One direction, jump to 0 | 382 | No |
| **LOOK** | Sweep to last request, reverse | 299 | No |
| **C-LOOK** | One direction to last, jump back | Best | No |

**Memory trick:**
- LOOK = SCAN **without** going to disk ends
- C-LOOK = C-SCAN **without** going to disk ends
- SSTF = best avg performance but **starves** like SJF

---

### ⚡ Must-Know Phrases for Exam

- *"Seek time is the dominant component of disk access time."*
- *"DMA frees the CPU from data transfer duties."*
- *"Device drivers abstract hardware; device-independent software abstracts drivers."*
- *"SCAN is called the elevator algorithm."*

---
