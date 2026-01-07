# Concurrency

- Event-Triggered Scheduling using Interrupts
- Static Schedule (Cyclic Executive)
- Dynamic Scheduling
  - Dynamic RTC Schedule
  - Dynamic Preemptive Schedule

---

# Interrupts

## CPU's Hardwired Exception Processing

1. **Finish current instruction**
   - Except for lengthy instructions [Load Multiple (LDM), Store Multiple (STM), Push, Pop, MULS]

2. **Push context (8 32-bit words) onto current stack (MSP or PSP [Based on operating mode, CONTROL register bit 1])**
   - `[SP_OLD, xPSR, Return address, LR (R14), R12, R3, R2, R1, R0, SP]` → Smaller address

3. **Switch to handler/privileged mode**
   - Use MSP

3.1. **Load IPSR with exception number**
   - Update IPSR (xPSR) with Exception Number ( `0x10000XX` )

4. **Load PC with address of exception handler**
   - Example: ORTD ISR is IRQ #31 (0x1F), so vector to handler begins at `0x40+4*0x1F = 0xBC` → `0455` → `PC = 0454`

5. **Load LR with EXC_RETURN code**

   | EXC_RETURN    | Return Mode   | Return Stack | Description                    |
   |---------------|---------------|--------------|--------------------------------|
   | 0xFFFF_FFF1   | 0 (Handler)   | 0 (MSP)      | Return to exception handler    |
   | 0xFFFF_FFF9   | 1 (Thread)    | 0 (MSP)      | Return to thread with MSP      |
   | 0xFFFF_FFFD   | 1 (Thread)    | 1 (PSP)      | Return to thread with PSP      |

   - Which SP to restore registers from? MSP (0) or PSP (1) → Previous value of SPSEL
   - Which mode to return to? Handler (0) or Thread (1) → Another exception handler may have been running when this exception was requested

6. **Start executing code of exception handler**
   - Exception handler starts running, unless preempted by a higher-priority exception
   - Exception handler may save additional registers on stack
     - If handler may call a subroutine, LR and R4 must be saved

---

## Exiting an Exception Handler

1. **Execute instruction triggering exception return processing**
   - `BX LR` used if EXC_RETURN is still in LR
   - If EXC_RETURN has been saved on stack, then use POP

2. **Select return stack, restore context from that stack**
   - Check EXC_RETURN (bit 2) to determine from which SP to pop the context
   - Pop the registers from that stack

3. **Resume execution of code at restored address**

---

## Types of Interrupts

- **Hardware interrupts** - Asynchronous
  - Not related to what code the processor is currently executing
- **Exceptions, Faults, software interrupts** - Synchronous
  - Are the result of specific instructions executing
- We can enable and disable (mask) most interrupts as needed (maskable), others are non-maskable

---

## Interrupt Service Routine (ISR)

- Subroutine which processor is forced to execute to respond to a specific event

---

## Nested Vectored Interrupt Controller (NVIC)

NVIC manages and prioritizes external interrupts for Cortex M0+

### Priority

- Allows program to prioritize response if both interrupts are requested simultaneously
- Interrupt Priority Registers (IPR0-7 registers): two bits per interrupt source, four interrupt sources per register
- Set priority to 0 (highest priority), 64, 128 or 192 (lowest)
- CMSIS: `NVIC_SetPriority(IRQnum, priority)`

### Enable

- Allows interrupt to be recognized
- Accessed through two registers (set bits for interrupts)
  - Set enable with `NVIC_ISER`, clear enable with `NVIC_ICER`
  - CMSIS Interface: `NVIC_EnableIRQ(IRQnum)`, `NVIC_DisableIRQ(IRQnum)`

### Pending

- Interrupt has been requested but is not yet serviced
- CMSIS: `NVIC_SetPendingIRQ(IRQnum)`, `NVIC_ClearPendingIRQ(IRQnum)`

### PRIMASK

- Similar to "Global interrupt disable" bit in other MCUs
- **Bit 0: PM Flag (Priority Mask Flag)**
  - Set to 1 to prevent activation of all exceptions with configurable priority
  - Clear to 0 to allow activation of all exceptions
- Exception mask register (CPU core)
- Use to prevent data race conditions with code needing atomicity

### Prioritization

- Exceptions are prioritized to order the response simultaneous requests (smaller number = higher priority)
- Priorities of some exceptions are fixed:
  - **Reset:** -3, highest priority
  - **NMI:** -2
  - **Hard Fault:** -1
- Priorities of other (peripheral) exceptions are adjustable
  - Value is stored in the interrupt priority register (IPR0-7)

#### Special Cases of Prioritization

- **Simultaneous exception requests?**
  - Lowest exception type number is serviced first
- **New exception requested while a handler is executing?**
  - New priority higher than current priority?
    - New exception handler preempts current exception handler
  - New priority lower than or equal to current priority?
    - New exception held in pending state
    - Current handler continues and completes execution
    - Previous priority level restored
    - New exception handled if priority level allows

---

## Example Using Port Module and External Interrupts

> **📌 Look Example**

---

## Interrupt Response Latency

## Maximum Interrupt Rate

- **F_Max_Int**: maximum interrupt frequency
- **F_CPU**: CPU clock frequency
- **C_ISR**: Number of cycles ISR takes to execute
- **C_Overhead**: Number of cycles of overhead for saving state, vectoring, restoring state, etc.
- **F_Max_Int = F_CPU / (C_ISR + C_Overhead)**
- Note that model applies only when there is one interrupt in the system
- When processor is responding to interrupts, it isn't executing our other code
  - **U_Int**: Utilization (fraction of processor time) consumed by interrupt processing
  - **U_Int = 100% × F_Int × (C_ISR + C_Overhead) / F_CPU**
  - CPU looks like it's running the other code with CPU clock speed of **(1 - U_Int) × F_CPU**

---

## Sharing Data

### Volatile Data

- Need to tell compiler which variables may change outside of its control
- Use `volatile` keyword to force compiler to reload these vars from memory for each use

### Non-Atomic Shared Data

- Prevent preemption within critical section
- If an ISR can write to the shared data object, need to disable interrupts
  - Save current interrupt masking state in m: `m = __get_PRIMASK();`
  - Disable interrupts: `__disable_irq();`
  - Restore previous state afterwards: `__set_PRIMASK(m);`

---

# GPIO

## Control Registers

### PDDR: Port Data Direction

- Each bit can be configured differently
- **Input:** 0
- **Output:** 1
- Reset clears port bit direction to 0

### Writing Output Port Data

| Register | Operation                   |
|----------|-----------------------------|
| PDOR     | Direct: write value to      |
| PTOR     | Toggle: write 1 to          |
| PCOR     | Clear (to 0): write 1       |
| PSOR     | Set (to 1): write 1 to      |

### Reading Input Port Data

- Read from `PDIR`
- Corresponding bit holds value which was read

---

## Mask

| Operation | Code | Description |
|-----------|------|-------------|
| Overwrite | `= MASK(foo);` | Overwrite existing value in n with mask |
| Set | `\|= MASK(foo);` | Set in n all the bits which are one in mask, leaving others unchanged |
| Complement | `~MASK(foo);` | Complement the bit value of the mask |
| Clear | `&= MASK(foo);` | Clear in n all the bits which are zero in mask, leaving others unchanged |

### Example

```c
&= ~MASK(foo)           // Clear
|= MASK(foo)            // Set
PTA->PDIR & MASK(SW1_POS)  // Check Switch
PTA->PDDR |=            // Enable Output
PTA->PDDR &= ~          // Enable Input
```

---

## Interfacing

- Input signal's value is determined by voltage
- Output voltage depends on current drawn by load on pin
- Output Example: Driving LEDs

### Pull-up and Pull-down Resistors

- Used to ensure input signal voltage is pulled to correct value when high-impedance
- **PE:** Pull Enable. 1 enables the pull resistor
- **PS:** Pull Select. 1 pulls up, 0 pulls down.

---

# Serial Communications

## Synchronous Full-Duplex Serial Data Bus

- Use two serial data lines - one for reading, one for writing.

## Synchronous Half-Duplex Serial Data Bus

- Doesn't allow simultaneous send and receive - is half-duplex communication

## Asynchronous Serial Communication

- Transmitter and receiver must generate clock locally
- Transmitter must add start bit (always same value) to indicate start of each data frame
- Receiver detects leading edge of start bit, then uses it as a timing reference for sampling data line to extract each data bit N at time `T_bit × (N + 1.5)`
- Stop bit is also used to detect some timing errors
- **Data frame fields:**
  - Start bit (one bit)
  - Data (LSB first or MSB, and size – 7, 8, 9 bits)
  - Optional parity bit is used to make total number of ones in data even or odd
  - Stop bit (one or two bits)

---

## Serial Communications and Interrupts

- Main program (and subroutines it calls)
- **Transmit ISR** – executes when serial interface is ready to send another character
- **Receive ISR** – executes when serial interface receives a character

---

## Software Structure – Parsing Messages

---

## Asynchronous Serial (UART) Communications

### Sending Basics

- If no data to send, keep sending 1 (stop bit) – idle line
- When there is a data word to send:
  - Send a 0 (start bit) to indicate the start of a word
  - Send each data bit in the word (use a shift register for the transmit buffer)
  - Send a 1 (stop bit) to indicate the end of the word

### Receiver Basics

1. Wait for a falling edge (beginning of a Start bit)
2. Then wait ½ bit time
3. Do the following for as many data bits in the word:
   - Wait 1 bit time
   - Read the data bit and shift it into a receive buffer (shift register)
4. Wait 1 bit time
5. Read the bit:
   - If 1 (Stop bit), then OK
   - If 0, there's a problem!

### Input Data Oversampling

- When receiving, UART oversamples incoming data line
  - Extra samples allow voting, improving noise immunity
  - Better synchronization to incoming data, improving noise immunity

### Baud Rate Generator

- Need to divide module clock frequency down to desired baud rate × oversampling factor
- **Example:**
  - 24 MHz → 4800 baud with 16x oversampling
  - Division factor = 24E6 / (4800 × 16) = 312.5. Must round to closest integer value (312 or 313), will have a slight frequency error.

---

## Software for Polled Serial Communication

> **📌 Look Example**

---

## Software for Interrupt-Driven Serial Communication

> **📌 Look Example**
