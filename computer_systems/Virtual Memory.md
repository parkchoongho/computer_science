### Three memory problem

- Not enough RAM
- Holes in our address space
- Programs writing over each other

### What is virtual memory?

- Indirection
- How does it solve the problems?
- Page Tables and Translation

### Implementing virtual memory

- Where do we store the page tables?
- Making translation fast

### Virtual memory and caches





## 1. What if we don't have enough money?

- MIPS gives each program its own **32-bit address space**
- Programs can access any byte in their **32-bit address space**
- What if you don't have 4GB of memory?





## 2. Holes in our address space

- How do programs share the memory?
- Where do we put them?

Memory fragmentation: 메모리 크기는 문제가 없으나 그게 분리되어 있어 같이 쓸수 없는 상태?



## 3. How do we keep programs secure?

- Each program can access any 32-bit memory address
- What if multiple programs access the same address?
  - **sw R2, 1024(R0)** will write to address 1024 regardless of the program that's running
- They can **corrupt and crash** each other: security and reliability



### Problems with memory

- If all programs have access to the 32-bit memory space:
  - Can **crash** if less than 4GB of RAM memory in the system
  - Can **run out of space** if we run multiple programs
  - Can **corrupt** other program's data

- How do we solve this?
  - Key to the problem: "**same memory space**"
  - Can we give each program its **own** **virtual memory space**?
  - If so, we can:
    - Separately **map** each **program's memory space (virtual memory)** to the **RAM memory** space => Mapping gives us **flexibility** in how we use the physical RAM memory
    - (and even move it to disk if we run out of memory)



## What is virtual memory (VM)?

### Virtual memory is a layer of indirection

"Any problem in computer science can be solved by adding indirection"

Virtual memory takes **program addresses** and **maps** them to **RAM addresses**

- **Without Virtual Memory** (Program Address = RAM Address)
  - No VM: Crash if we try to access more RAM than we have
- **With Virtual Memory** (Program Address Maps to RAM Address)
  - VM: Mapping gives us flexibility in how we use the RAM

### Solving the problems: 1. not enough memory

- **Map** some of the **program's address space** to the **disk**
- When we need it, we bring it into memory
- **Mapping lets us use our disk to give the illusion of unlimited memory**

### Solving the problems: 2. holes in the address space

- How do we use the holes left when programs quit?
- We can **map** a **program's addresses** to **RAM addresses** however we like
- **Each program has its own mapping. Mappings let us put our program data wherever we want in the RAM.**

### Solving the problems: 3. keeping programs secure

- **Program 1's** and **Program 2's** addresses **map to** different **RAM addresses**
- Because each program has its own address space, they cannot access each other's data: security and reliability!
- **Program 1** stores your bank balance at address 1024 1B. **VM maps** it to **RAM address 1**
- **Program 2** stores your video game score at address 1024 2B. **VM maps** it to **RAM address 5**
- **Neither can touch the other's data!**



## How does VM work?

- Basic Idea: separate memory spaces
- **Virtual Memory**: what the **program** sees
  - e.g. Id R4, 1024(R0) accesses **virtual address** R0+1024=1024
- **Physical Memory**: the **physical RAM** in the computer
  - e.g. if you have 2GB of **RAM** installed, you have **physical addresses** 0 to 2^31 - 1
- **Virtual Addresses(VA)**
  - What the program uses
  - In MIPS, this is the full 32-bit address space: 0 to 2^32 - 1
- **Physical Addresses(PA)**
  - What the hardware uses to talk to the RAM
  - Address space determined by how much RAM is installed



## How does a program access memory?

1. Program executes a load with specifying a **virtual address(VA)**
2. Computer **translates** the address to the **physical address(PA)** in memory
3. (If the **physical address(PA)** is not in memory, the operating system loads it in from **disk**)
4. The computer then **reads the RAM** using the **physical address(PA)** and returns the data to the program



## Page Tables (Keeping track of VA -> PA mappings)

The map from **Virtual Addresses(VA)** to **Physical Addresses(PA)** is the **Page Table**. So far we have had one **Page Table Entry (PTE)** for every **Virtual Address** 

Q **How many entries do we need in our Page Table**?

- 1 for every byte = 2^32 = 4 billions
- 1 for every word = 2^30 = 1billion
- 1 for every register = 32

=> 1 for every word = 2^30 = 1billion

We have a word-aligned memory so we need to be able to address every word.

**Note**: each entry needs the PA which is 32 bits, so that's 1GB of memory for the Page Table alone!

(1 word = 4 bytes?)



## Page Table Size

- We need to translate every possible address:
  - Our programs have **32-bit Virtual Address** spaces
  - That's 2^30 words that need **Page Table Entries** (1 billion entries!) 
  - (If they don't have a **Page Table Entry** then we can't access them because we can't find the **physical address**.)
- How can we make this more manageable?
  - What if we divided memory up into chunks(**pages**) instead of words?

**Fine-grain**: Maps each word address 2^30 words to map

**Coarse-grain**: Maps chunks of address Fewer mappings



## Coarse-grained: pages instead of words

- The **Page Table** manages larger chunks (pages) of data:
  - Fewer **Page Table Entries** needed to cover the whole address space
  - But, less flexible in how to use the RAM (have to move a page at a time)
- Today:
  - Typically 4kB pages (1024 words per page)
  - Sometimes 2MB pages (524,288 words per page)

Q **How many entries do we need in our Page Table with 4kB pages on a 32-bit machine?** 

- 1 for every word = 2^30 = 1billion
- 1 for every 1024 words = 1 million
- 1 for every 4096 words = 262,144

=> 1 for every 1024 words = 1 million