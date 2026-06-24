# **Master Diagram Study Guide: Computer Organization & Microprocessors**

This guide contains the essential block diagrams and key points for all 5 units.

## **UNIT 1: Computer Evolution and Performance**

### **1\. Von Neumann vs. Harvard Architecture**

**Von Neumann Architecture**

flowchart LR  
    CPU\[CPU\\n- ALU\\n- Control Unit\\n- Registers\] \<--\>|Single Shared Bus| MEM\[Main Memory\\nData \+ Instructions\]  
    CPU \<--\> IO\[I/O Devices\]

* **Imp Points:**  
  * **Single Memory Space:** Data and Instructions are stored in the exact same memory.  
  * **Shared Bus:** Uses a single set of lines for both data and instructions.  
  * **Von Neumann Bottleneck:** CPU cannot read an instruction and read/write data simultaneously. Execution is strictly sequential.

**Harvard Architecture**

flowchart LR  
    DMEM\[Data Memory\] \<--\>|Data Bus| CPU\[CPU\]  
    IMEM\[Instruction Memory\] \<--\>|Instruction Bus| CPU

* **Imp Points:**  
  * **Physically Separate:** Distinct memory modules for Data and Instructions.  
  * **Separate Buses:** Two independent sets of buses.  
  * **High Performance:** Allows simultaneous fetching of instructions and data, eliminating the Von Neumann bottleneck.

## **UNIT 2: Memory Management**

### **2\. The Memory Hierarchy**

flowchart TD  
    A\[Registers\\nIn CPU, Fastest, Smallest\] \--\> B\[Cache Memory\\nSRAM, Very Fast, Small\]  
    B \--\> C\[Main Memory / RAM\\nDRAM, Moderate Speed, Moderate Size\]  
    C \--\> D\[Magnetic Disk / SSD\\nExternal, Slowest, Largest Capacity\]

* **Imp Points:**  
  * As you move **down** the pyramid: Cost per bit decreases, capacity increases, speed decreases (access time increases).  
  * CPU only interacts directly with Registers and Cache.  
  * Operates on the **Principle of Locality** (Spatial and Temporal).

### **3\. RAID 5 (Distributed Parity)**

flowchart TD  
    subgraph Disk 1  
        D1A\[Block A1\]  
        D1B\[Block B1\]  
        D1C\[Block C1\]  
        D1P\[Parity D\]  
    end  
    subgraph Disk 2  
        D2A\[Block A2\]  
        D2B\[Block B2\]  
        D2P\[Parity C\]  
        D2D\[Block D1\]  
    end  
    subgraph Disk 3  
        D3A\[Block A3\]  
        D3P\[Parity B\]  
        D3C\[Block C2\]  
        D3D\[Block D2\]  
    end  
    subgraph Disk 4  
        D4P\[Parity A\]  
        D4B\[Block B3\]  
        D4C\[Block C3\]  
        D4D\[Block D3\]  
    end

* **Imp Points:**  
  * **Block-Level Striping:** Data is broken into blocks and spread across multiple drives.  
  * **Distributed Parity:** Parity (error-correction) data is not on one single disk; it is spread evenly across all disks.  
  * **Fault Tolerance:** Can survive the failure of exactly 1 hard drive without data loss. Solves the write-bottleneck of RAID 4\.

## **UNIT 3: Introduction to 8086 Microprocessor**

### **4\. Internal Architecture of 8086 (BIU & EU)**

flowchart TB  
    subgraph BIU \[Bus Interface Unit \- BIU\]  
        SEG\[Segment Registers\\nCS, DS, SS, ES\]  
        IP\[Instruction Pointer\]  
        Q\[6-Byte Instruction Queue\]  
        BUS\[Address Generation\\n& Bus Control\]  
    end  
      
    subgraph EU \[Execution Unit \- EU\]  
        CTRL\[Control System\]  
        ALU\[16-bit ALU\]  
        FLAGS\[Flag Register\]  
        GPR\[General Registers\\nAX, BX, CX, DX\\nPointers: SP, BP, SI, DI\]  
    end

    Q \--\>|Fetches Instructions| CTRL  
    BUS \<--\>|System Bus| RAM\[Memory / IO\]  
    BIU \<--\>|Internal Bus| EU

* **Imp Points:**  
  * **Two Independent Units:** BIU handles memory/bus communication; EU executes instructions.  
  * **Pipelining:** BIU fetches up to 6 bytes into the queue *while* the EU is executing the current instruction, drastically improving speed.  
  * **16-bit Processing:** The ALU and all internal registers are 16 bits wide.

### **5\. 8086 Programmer's Model (Registers)**

block-beta  
  columns 4  
  block:GPR:2  
    columns 2  
    AH AL BH BL CH CL DH DL  
  end  
  block:PTR:1  
    columns 1  
    SP BP SI DI  
  end  
  block:SEG:1  
    columns 1  
    CS DS SS ES  
  end  
  block:IP\_FLAG:4  
    columns 2  
    IP FLAGS  
  end

* **Imp Points:**  
  * **14 Registers Total:** All are 16-bit.  
  * **General Purpose:** AX, BX, CX, DX (can be split into 8-bit High/Low halves, e.g., AH and AL).  
  * **Pointers/Index:** SP (Stack), BP (Base), SI (Source), DI (Destination).  
  * **Segments:** CS (Code), DS (Data), SS (Stack), ES (Extra).

## **UNIT 4: Memory Organization and Interrupts**

### **6\. Physical Address Generation (Segmentation)**

flowchart LR  
    A\[Segment Register\\n16-bit\] \--\>|Shift Left 4 bits\\nMultiply by 10H| B(Shifted Segment\\n20-bit)  
    C\[Offset / Pointer\\n16-bit\] \--\> D(( \+ ))  
    B \--\> D  
    D \--\> E\[Physical Address\\n20-bit output to Bus\]

* **Imp Points:**  
  * 8086 has 16-bit registers but a 20-bit address bus (1 MB physical memory).  
  * Memory is divided into 64 KB logical Segments.  
  * **Formula:** Physical Address \= (Segment Register \* 10H) \+ Offset

### **7\. 8259 Programmable Interrupt Controller (PIC)**

flowchart LR  
    HW\[Hardware Devices\\nKeyboard, Timer, etc.\] \--\>|IR0 to IR7| IRR\[IRR\\nInterrupt Request Reg\]  
    IMR\[IMR\\nInterrupt Mask Reg\] \--\> PR\[Priority Resolver\]  
    IRR \--\> PR  
    PR \--\> ISR\[ISR\\nIn-Service Reg\]  
    ISR \--\> CTRL\[Control Logic\]  
    CTRL \<--\>|INT / INTA| CPU\[8086 CPU\]

* **Imp Points:**  
  * **Multiplexer:** Funnels up to 8 hardware interrupts into the 8086's single INTR pin.  
  * **IRR:** Keeps track of which lines are requesting service.  
  * **IMR:** Used by the OS to mask (disable) specific interrupts.  
  * **Priority Resolver:** Decides which interrupt gets sent to the CPU first based on programmed priority.

## **UNIT 5: Parallel Organization**

### **8\. Flynn’s Taxonomy**

flowchart TD  
    A\[Computer Architecture\] \--\> B\[SISD\]  
    A \--\> C\[SIMD\]  
    A \--\> D\[MISD\]  
    A \--\> E\[MIMD\]  
      
    B \-.-\> B1(Single Core PC)  
    C \-.-\> C1(GPUs \- Graphics)  
    D \-.-\> D1(Fault Tolerant Systems)  
    E \-.-\> E1(Multi-Core CPUs / Clusters)

* **Imp Points:**  
  * **SISD:** Single Instruction, Single Data. (Old PCs).  
  * **SIMD:** Single Instruction, Multiple Data. One command applies to thousands of data points at once (GPUs).  
  * **MIMD:** Multiple Instruction, Multiple Data. Independent cores doing independent tasks (Modern computing).

### **9\. UMA vs. NUMA Memory Architectures**

**UMA (Uniform Memory Access)**

flowchart TD  
    C1\[CPU 1\] \--\> BUS\[Shared System Bus\]  
    C2\[CPU 2\] \--\> BUS  
    C3\[CPU n\] \--\> BUS  
    BUS \<--\> MEM\[Shared Main Memory\]

* **Imp Points:** All CPUs share the same memory. Access time is identical for every CPU. Shared bus becomes a bottleneck with too many CPUs.

**NUMA (Non-Uniform Memory Access)**

flowchart LR  
    subgraph Node 1  
        C1\[CPU 1\] \<--\> M1\[Local Memory 1\]  
    end  
    subgraph Node 2  
        C2\[CPU 2\] \<--\> M2\[Local Memory 2\]  
    end  
    Node 1 \<--\>|Interconnect| Node 2

* **Imp Points:** Each CPU has its own local memory. Local memory access is extremely fast. Remote memory access (checking Node 2's memory from CPU 1\) is slow. Highly scalable for large servers.

### **10\. RISC Architecture Concept (Pipelining & Registers)**

flowchart LR  
    A\[Large Register File\\n32 to 192 Registers\] \<--\>|Fast Math Operations| ALU\[Simplified ALU\\nSingle Cycle Execution\]  
    ALU \--\>|Load/Store ONLY| RAM\[Main Memory\]

* **Imp Points:**  
  * **Load/Store Architecture:** Math can ONLY be done on variables already inside registers. You cannot Add directly to memory.  
  * **Large Register File:** Needed to minimize slow RAM access. Uses overlapping register windows for fast function calls.  
  * **Fixed Length Instructions:** Makes fetching and pipelining incredibly smooth compared to CISC.