# 107_2_ComputerOrganization_Final
107-2 計算機組織 期末專案

> 老師：朱守禮  
> 組別：第十三組  
> 班級：資訊二甲  
> 學生：10627107  黃立至  
> 學生：10627109  陳力維   
> 學生：10627150  林易賢  
> 學生：10627152  吳承宗  

## 背景
以ModelSim撰寫，撰寫語法為Verilog，以Midterm project所設計的ALU Design為基礎設計一個Pipellined MIPS-Lite CPU，如下
- 期中  
ALU、Muitplier、Shifters、HiLo、Mux、Total ALU。
- 期末
  - Use Four stage to implement five steps for Pipelined Datapath (IF/ID、ID/EX、EX/MEM、MEM/WB)。
  - Integer arithmetic :  addiu。
  - Integer Memory Access :  lw、sw。
  - Integer Branch:  beq、bne、j。
  - Integer Multiply/Divide :  multu。
  - Other Instructions :  mfhi、mflo、nop。
  
### 32-bit ALU
包含 `AND`, `OR`, `ADD`, `SUB`, `SLT`, `ADDIU`等功能，`32-bit Alu` 由`32`個`one-bit ALU`組成，其中`AND`, `OR`閘為較簡單的邏輯電路，而`ADD`, `SUB`兩項功能則多由`XOR`閘組成，`SUB`指令實作類似`ADD`，只是在減數部分需要多做一次`XOR`閘去invert，在做一般的`ADD`指令，`SLT`指令是判斷兩輸入的大小。

### Multiplier
乘法器主要由第一版乘法器方式設計，即每次乘法只看乘數的第`0`位元與被乘數的和，連續累加且每做下一次乘法之前將被乘數左移一位元( `<<` )，乘數右移一位元( `>>` )，直至乘數的`32`位元皆右移為`0`，將做完的積放入`HiLo`暫存器。

### Shifter
主要以Barrel Shifter的方式實現，透過`Mux_2to1`此函式對每1 `bit`進行移位，移位方式分成五種: 1、2、4、8、16 bits移位，其中`inputB`的最後五個位元為判斷是否執行此五種位移的條件，`inputB`為`intruction[10:6]`部份。

### Mux2
利用不同訊號去判斷要輸出哪一個輸出。

## 方法
### MIPS instruction formats
- `opcode`: instruction opcode
- `rs`, `rt`: source register
- `rd`: destination register
- `shamt`: shift amout
- `funct`: function code (works with opcode to implement ALU operation)
- `offset`/`immediate`: immediate value
- `address`: target address for `jump`  
![image](https://user-images.githubusercontent.com/51331397/183282855-c773ff39-6c3b-4dbd-92b8-9d063f440e9d.png)  

### MIPS instruction execution
1. Instruction Fetch (IF)
2. Decode instruction and read register (ID)
3. If necessary, perform an ALU operation (EX)
4. If `load` or `store`, do memory access (MEM)
5. Write result back to register file and increment PC (WB)

### Processor design five steps
1. Analysis instruction set, get datapath requirements.
2. Select datapath components and esablish clocking methodology.
3. Assemble datapath that meet requirements.
4. Determine control signal values for each instruction.
5. Assemble control logic to generate control signals.

### Components
![image](https://user-images.githubusercontent.com/51331397/183283064-45f174ba-f649-4d1b-b4e9-0e4d173ae3c1.png)  
![image](https://user-images.githubusercontent.com/51331397/183283069-3ffcaffa-c959-49e2-96c6-c2a93c047dc3.png)  
![image](https://user-images.githubusercontent.com/51331397/183283080-ef8db241-bdf5-435d-829e-f68752cca7bb.png)  
![image](https://user-images.githubusercontent.com/51331397/183283088-d81a6f61-4d53-4259-b1ab-3fd137ee00fc.png) 
![image](https://user-images.githubusercontent.com/51331397/183283096-74f493dd-37bb-49df-bf4a-f527cff26521.png)  
![image](https://user-images.githubusercontent.com/51331397/183283102-0b7ff663-6904-46cd-9f98-90ccf6dddf1b.png)  
![image](https://user-images.githubusercontent.com/51331397/183283109-30b19b7d-c15a-475c-96b1-eef65b909398.png)  
![image](https://user-images.githubusercontent.com/51331397/183283114-0cf5740c-7491-4c61-9410-f631653b06b1.png)  

### Datapath
![image](https://user-images.githubusercontent.com/51331397/183283142-78b44054-ab8a-462f-bc69-84e5122522d3.png)  
![image](https://user-images.githubusercontent.com/51331397/183283147-41de762d-953e-422d-9e03-3b652ba3a86c.png)  
![image](https://user-images.githubusercontent.com/51331397/183283150-a55e9315-1120-4c2a-b9ef-2eaa92bb14ac.png)  
![image](https://user-images.githubusercontent.com/51331397/183283155-38e81ee5-c291-4f15-b923-3d0f988fbcd9.png)  
![image](https://user-images.githubusercontent.com/51331397/183283165-a7e79975-6057-4489-b2f5-e262fe3642a6.png)  

## 結果
### Input
![image](https://user-images.githubusercontent.com/51331397/183283265-85d834f0-0db6-48cf-9f45-44d62dbe589e.png)  

### Waveform (`add $at, $v0, $v1)
- IF/ID  
![image](https://user-images.githubusercontent.com/51331397/183283429-014737da-d09c-49c3-b701-fdedceee408c.png)
- ID/EX  
![image](https://user-images.githubusercontent.com/51331397/183283454-8db932d3-019b-415f-b491-ff37d75cf972.png)
- EX/MEM  
![image](https://user-images.githubusercontent.com/51331397/183283464-62c269d4-e4c4-4846-8dc0-1a2f025be5ed.png)
- MEM/WB  
![image](https://user-images.githubusercontent.com/51331397/183283472-a4dd68a9-9419-4c85-a4d8-4c8d80556ba6.png)

### 乘法器實做
- 30 * 9 = 270 (0x10e)
![image](https://user-images.githubusercontent.com/51331397/183283507-e687232e-0bca-4b3a-9374-b7ee5e8e35bc.png)  
- 5615 * 1563146 = 8777064790 (0x20b276156)
![image](https://user-images.githubusercontent.com/51331397/183283538-0d5602d5-9a33-4067-bd6d-197a17d19d5b.png)

## 討論
藉由上次的期中作業，我們對verilog已經有所了解，但這次實作CPU的過程中，我們也遇到了不少問題的。確認是否接好線是最繁瑣的，因為元件很多很複雜，然後有些線因為通過了好幾個stage才做事，所以在debug的時候，花了不少時間在找線哪裡沒接好。還有就是我們宣告的時候沒有注意到變數大小，像是function裡宣告5位元，但在testbench裡卻宣告32位元。再來就是時間軸的問題，像是乘法需要的時間跟其他指令比較不一樣，為了配合乘法，讓各個stage能夠都一樣的時間輸出。直到最後，我們還是沒有處理好時序問題，整個處理器都需要去配合乘法器，因為需要32個clock乘法器才能輸出。除了乘法器之外，老師要求之指令都實做出來並都確認過其waveform為正確。這次的作業，我們花了相當多的精力在上面，每個人都有去理解作業的細節，也都有提出自己的想法，在這次作業上大家都盡心盡力了。
