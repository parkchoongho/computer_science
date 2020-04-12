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