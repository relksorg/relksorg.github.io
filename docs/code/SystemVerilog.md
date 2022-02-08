---
layout: default
title: SystemVerilog
parent: Code
nav_order: 1
---

# SystemVerilog 

---

### Arty A7-100T

* experimentation with typedef enum statements
* grey-counter testing
* Assumes 100MHz clock

```verilog
`timescale 1ns / 1ps

module top
#(
parameter clkFreq = 27'd100_000_000
)(
    input logic [3:0] sw,
    input logic [2:0] btn,
    input logic rst,
    input logic CLK100MHZ,
    input logic [15:0] dip,
    output logic [3:0] led,
    output logic freqPinOne, freqPinTwo,
    output logic [6:0] outWire
    );
    
logic stb;
logic [26:0] cnt = 0; 
always_ff @(posedge CLK100MHZ) begin
    if(cnt != clkFreq-1) begin
        stb <= 0;
        cnt <= cnt + 1;
    end else begin
        stb <= 1;    
        cnt <= 0;
    end
end

//---------- 

logic [3:0] greyCnt = 0;
bit hold = 0;

always_ff @(posedge stb or negedge rst) begin
    if(!rst) greyCnt <= 0; else begin
        if(hold == 0) begin
            greyCnt <= greyCnt + 1'b1;
            if(greyCnt == 4'b1110) hold <= hold + 1;
        end else if (hold == 1) begin
            greyCnt <= greyCnt - 1'b1;
            if(greyCnt == 4'b0001) hold <= hold - 1;
        end
    end  
end

assign led[3:0] = greyCnt[3:0];

typedef enum logic [2:0] {NOP, ADD, SUB, MULT, DIV, AAA, AAB, ABA} opcode_t;
                       //[000, 001, 010, 011, 100,  101, 110, 111]

always_comb begin 
    case(greyCnt)
        NOP: outWire[6:0] = 6'b111111;
        ABA: outWire[6:0] = 6'b111111; 
        
        default: outWire[6:0] = 6'b000000; 
    endcase
end



//timing stuff
always_ff @(posedge CLK100MHZ) begin
    if(stb) begin
        freqPinTwo <= freqPinTwo + 1;
    end
end

always_ff @(posedge stb) freqPinOne <= freqPinOne + 1;    
    
endmodule
```

---

### Arty A7-100T

* prime factorization 
* stores results in Distributed RAM
* cycle and output results
* Assumes 100MHz clock

```verilog
`timescale 1ns / 1ps

module primeFactors
#(
parameter clkFreq = 27'd100,
parameter slowClkFreq = 27'd100_000_000,
parameter sampleNumber = 64'd2_487_125_557_145_446,
parameter max64Num = 64'd9_223_372_036_854_775_807
)(
    input logic [3:0] sw,
    input logic [2:0] btn,
    input logic rst,
    input logic CLK100MHZ,
    input logic [15:0] dip,
    output logic [3:0] led,
    output logic [22:0] outWire,
    output logic notifyOut
    //output logic freqPinOne, freqPinTwo,
    //output logic [6:0] outWire
    );
    
    logic stb;
    logic [26:0] clkCnt = 0; 
    
    logic cycleStb;
    logic [26:0] cycleClkCnt = 0; 
    always_ff @(posedge CLK100MHZ) begin
        if(clkCnt != clkFreq-1) begin
            stb <= 0;
            clkCnt <= clkCnt + 1;
        end else begin
            stb <= 1;    
            clkCnt <= 0;
        end
       
       //slower clock pulse - 1 second
        
       if(cycleClkCnt != slowClkFreq-1) begin
            cycleStb <= 0;
            cycleClkCnt <= cycleClkCnt + 1;
        end else begin
            cycleStb <= 1;    
            cycleClkCnt <= 0;
        end
    end
    
    //------------- is a number prime
    
    /*
    logic [63:0] cnt = 2; //needs to be 2, cause # % 1 = 0
    bit notPrime = 0;
    bit prime = 0;
    bit ran = 0;
    
    always_ff @(posedge stb) begin

        if((sampleNumber % cnt) == 0) begin
            notPrime <= notPrime + 1'b1;
        end
        
        if(cnt != (sampleNumber - 1)) cnt <= cnt + 1'b1; //# % # = 0
        else prime <= prime + 1'b1;
        
        led[0] <= prime;
        led[1] <= notPrime;
        
    end */
    
    //--- prime factors ---
    logic [15:0] L = 0;
    logic [3:0] cycleResults = 0;
    
    logic [127:0] i = 2;
    logic [15:0] n;
    logic [22:0] factors [15:0]; 
    bit ran = 0;
    
    logic [3:0] resetStorage = 0;
    always_ff @(posedge stb) begin
    if(sw[2]) begin 
        n <= dip[15:0];
        i <= 2;
        L <= 0;
        ran <= 0;
        if(resetStorage != 4'hF) begin
            resetStorage <= resetStorage + 1;
            factors[resetStorage] <= 0;
        end
        
    end
    if(sw[0]) begin
            if ((i * i) <= n) begin
                if (n % i) i <= i + 1;
                else begin
                    n <= n / i;
                    L <= L + 1;
                    factors[L] <= i;
                end
            end
            
            if ((n > 1) && !((i*i) <= n)) begin
                if(ran != 1) begin
                    L <= L + 1;
                    factors[L] <= n;
                    ran = ran + 1;
                end
            end
        end
    end
    
    bit disp = 0;
    always_ff @(posedge cycleStb) begin   
    
        if(sw[2]) cycleResults <= 0;
        
        if(sw[1]) begin
            outWire[22:0] <= factors[cycleResults];
            cycleResults <= cycleResults + 1;
            disp <= disp + 1;
            notifyOut <= disp;
        end
    end
    
    assign led[3:0] = sw[3:0];
    
endmodule
```

---

### Genesys-ZU-5EV

* 1 to 5KHz & 10 to 50KHz square wave generation - improved design
* parallel counting 
* assumes input clk is 100MHz

```verilog
`timescale 1ns / 1ps

module oneToFiftyKHz
#( 
parameter oneKhzSpeed = 20'd100_000,
parameter twoKhzSpeed = 20'd50_000,
parameter threeKhzSpeed = 20'd31_250, 
parameter fourKhzSpeed = 20'd25_000,
parameter fiveKhzSpeed = 20'd20_000,

//*10

parameter tenKhzSpeed = 20'd10_000,
parameter twentyKhzSpeed = 20'd5_000,
parameter thirtyKhzSpeed = 20'd3_125,
parameter fortyKhzSpeed = 20'd2_500,
parameter fiftyKhzSpeed = 20'd2_000
)(
    input wire logic [3:0] clkSelect,
    input wire logic clkIn, clkRst,
    output logic clkOut, rstStatus
    );
    
    logic [20:0] cntOne = 0;
        logic [20:0] cntTwo = 0;
            logic [20:0] cntThree = 0;
                logic [20:0] cntFour = 0;
                    logic [20:0] cntFive = 0;
    logic [20:0] cntTen = 0;
        logic [20:0] cntTwenty = 0;
            logic [20:0] cntThirty = 0;
                logic [20:0] cntForty = 0;
                    logic [20:0] cntFifty = 0;
    
    
    
    logic stbOne;
    logic stbTwo;
    logic stbThree;
    logic stbFour;
    logic stbFive;
    logic stbTen;
    logic stbTwenty;
    logic stbThirty;
    logic stbForty; 
    logic stbFifty;
    
    always_ff @(posedge clkIn) begin
        
        rstStatus <= clkRst;
        
        case(clkSelect) 
        
            4'd1: begin 
                if(clkRst != 1) begin
                    clkOut <= stbOne;
                end
            end
            4'd2: begin 
                if(clkRst != 1) begin
                    clkOut <= stbTwo;
                end
            end
            4'd3: begin 
                if(clkRst != 1) begin
                    clkOut <= stbThree;
                end
            end
            4'd4: begin 
                if(clkRst != 1) begin
                    clkOut <= stbFour;
                end
            end
            4'd5: begin 
                if(clkRst != 1) begin
                    clkOut <= stbFive;
                end
            end
            4'd6: begin 
                if(clkRst != 1) begin
                    clkOut <= stbTen;
                end
            end
            4'd7: begin 
                if(clkRst != 1) begin
                    clkOut <= stbTwenty;
                end
            end
            4'd8: begin 
                if(clkRst != 1) begin
                    clkOut <= stbThirty;
                end
            end
            4'd9: begin 
                if(clkRst != 1) begin
                    clkOut <= stbForty;
                end
            end
            4'd10: begin 
                if(clkRst != 1) begin
                    clkOut <= stbFifty;
                end
            end
            
            default: begin 
                if(clkRst != 1) begin
                    clkOut <= stbOne;
                end
            end
        
        endcase

                    if (cntOne != oneKhzSpeed - 1) begin
                        stbOne <= 0;
                        cntOne <= cntOne + 1;
                    end else begin
                        stbOne <= 1;
                        cntOne <= 0;
                    end
                    
                    
                    if (cntTwo != twoKhzSpeed - 1) begin
                        stbTwo <= 0;
                        cntTwo <= cntTwo + 1;
                    end else begin
                        stbTwo <= 1;
                        cntTwo <= 0;
                    end
                    
                    
                    if (cntThree != threeKhzSpeed - 1) begin
                        stbThree <= 0;
                        cntThree <= cntThree + 1;
                    end else begin
                        stbThree <= 1;
                        cntThree <= 0;
                    end
                    
                    if (cntFour != fourKhzSpeed - 1) begin
                        stbFour <= 0;
                        cntFour <= cntFour + 1;
                    end else begin
                        stbFour <= 1;
                        cntFour <= 0;
                    end
                    
                    
                    if (cntFive != fiveKhzSpeed - 1) begin
                        stbFive <= 0;
                        cntFive <= cntFive + 1;
                    end else begin
                        stbFive <= 1;
                        cntFive <= 0;
                    end
                    
                    
                    if (cntTen != tenKhzSpeed - 1) begin
                        stbTen <= 0;
                        cntTen <= cntTen + 1;
                    end else begin
                        stbTen <= 1;
                        cntTen <= 0;
                    end
                    
                    if (cntTwenty != twentyKhzSpeed - 1) begin
                        stbTwenty <= 0;
                        cntTwenty <= cntTwenty + 1;
                    end else begin
                        stbTwenty <= 1;
                        cntTwenty <= 0;
                    end
                    
                    
                    if (cntThirty != thirtyKhzSpeed - 1) begin
                        stbThirty <= 0;
                        cntThirty <= cntThirty + 1;
                    end else begin
                        stbThirty <= 1;
                        cntThirty <= 0;
                    end
                    
                    
                    if (cntForty != fortyKhzSpeed - 1) begin
                        stbForty <= 0;
                        cntForty <= cntForty + 1;
                    end else begin
                        stbForty <= 1;
                        cntForty <= 0;
                    end
                    
                    if (cntFifty != fiftyKhzSpeed - 1) begin
                        stbFifty <= 0;
                        cntFifty <= cntFifty + 1;
                    end else begin
                        stbFifty <= 1;
                        cntFifty <= 0;
                    end 
    end
    
endmodule
```
---

### Genesys-ZU-5EV

* Variable Square-wave generation (1KHz, 500Hz, 250Hz, 1Hz, 500mHz, 250mHz)
* Clk IP core, 125MHz sysclk input, 50MHz out
* Switch clk reset *sw[3]*
* BTN clk variation control: *btn[0], btn[1]*
* **FUTURE PATCHES NEEDED:** Generate clock circuitry for each timing mode, instead of changing dividing num

```verilog
`timescale 1ns / 1ps

module top
#(parameter clkSpeed = 29'd50_000_000, // for 1 hz
  parameter khzSpeed = 29'd50_000, // for 1 Khz
  parameter sampleTwo = 2'b10
)(
    output  logic [7:0] ledCheck,
    output  logic [3:0] led,
    output  logic clkOutPrimary, clkOutSecondary,
    input wire logic [3:0] sw,
    input wire logic [4:0] btn,
    input wire logic sysclk //25MHz or 125MHz lmao
    );

    //Clocking:
    
   clk_wiz_0 myClk
   (
    // Clock out ports
    .clk_out1(clk_out1),     // output clk_out1
    // Status and control signals
    .reset(sw[3]), // input reset
    .locked(led[1]),       // output locked
   // Clock in ports
    .clk_in1(sysclk));      // input clk_in1
      
    logic [29:0] useKhzSpeed = 0;
    logic [29:0] useHzSpeed = 0;
            
    always_ff @(posedge sysclk) begin
        case (btn)
            5'b00001: begin 
                useKhzSpeed = khzSpeed * 2;
                useHzSpeed = clkSpeed * 2;
            end
            5'b00010: begin
                useKhzSpeed = khzSpeed * 4;
                useHzSpeed = clkSpeed * 4;
            end
            
            default: begin
                useKhzSpeed = khzSpeed;
                useHzSpeed = clkSpeed;
            end
        endcase
    end
      
    //JC2 PMOD
    logic stb_s;
    logic [29:0] cnt_s = 0;
    always_ff @(posedge clk_out1) begin
        if (cnt_s != useKhzSpeed - 1) begin
            stb_s <= 0;
            cnt_s <= cnt_s + 1;
        end else begin
            stb_s <= 1;
            cnt_s <= 0;
        end
    end
    
    //JC1 PMOD
    logic stb;
    logic [29:0] cnt = 0;
    always_ff @(posedge clk_out1) begin
        if (cnt != useHzSpeed - 1) begin
            stb <= 0;
            cnt <= cnt + 1;
        end else begin
            stb <= 1;
            cnt <= 0;
        end
    end
    
    //1KHz clock on secondary
    
    logic oneKHz;
    always_ff @(posedge clk_out1) begin
        if (stb_s) begin
            if (sw[0])begin
                oneKHz <= oneKHz + 1;
                clkOutSecondary <= oneKHz;
            end
        end
    end

    //1Hz clock on primary
    logic oneHz;
    always_ff @(posedge clk_out1) begin
        if (stb) begin
            if (sw[1])begin
                oneHz <= oneHz + 1;
                clkOutPrimary <= oneHz;
            end
        end
    end
    
    //Programs n' stuff:
    
endmodule
```

---

### Genesys-ZU-5EV

* Square-wave generation 
* PMOD output testing (made a custom LED PMOD device)
* Clk IP core, 125MHz sysclk input, 100MHz out

```verilog
`timescale 1ns / 1ps

module top
#(parameter clkSpeed = 29'd500_000_000,
  parameter sampleTwo = 2'b10
)(
    output  logic [7:0] ledCheck,
    output  logic [3:0] led,
    output  logic clkOutPrimary, clkOutSecondary,
    input wire logic [3:0] sw,
    input wire logic sysclk //25MHz
    );
    
    always_comb begin
       case (sw)
            4'b0001 : ledCheck[7:0] = 8'b0000_0001;
            4'b0010 : ledCheck[7:0] = 8'b0000_0010;
            4'b0011 : ledCheck[7:0] = 8'b0000_0100;
            4'b0100 : ledCheck[7:0] = 8'b0000_1000;
            4'b0101 : ledCheck[7:0] = 8'b0001_0000;
            4'b0110 : ledCheck[7:0] = 8'b0010_0000;
            4'b0111 : ledCheck[7:0] = 8'b0100_0000;
            4'b1000 : ledCheck[7:0] = 8'b1000_0000;
            
            default : ledCheck[7:0] = 8'b1111_1111;
       endcase
    end

    //Timed events:
    
   clk_wiz_0 myClk
   (
    // Clock out ports
    .clk_out1(clk_out1),     // output clk_out1
    // Status and control signals
    .reset(sw[1]), // input reset
    .locked(led[1]),       // output locked
   // Clock in ports
    .clk_in1(sysclk));      // input clk_in1
    
    //JC1 PMOD
    logic [8:0] clockCounter = 0;
    always @(posedge sysclk) begin
        if (clockCounter == 8'd100) begin
            clockCounter <= 0;
            clkOutPrimary <= ~ clkOutPrimary;
        end else begin
            clockCounter <= clockCounter + 1;
        end
    end
    
    //JC2 PMOD
    logic [8:0] clockCounterSeconary = 0;
    always @(posedge clk_out1) begin
        if (clockCounterSeconary == 8'd100) begin
            clockCounterSeconary <= 0;
            clkOutSecondary <= ~ clkOutSecondary;
        end else begin
            clockCounterSeconary <= clockCounterSeconary + 1;
        end
    end
    //set the PMOD JC1 to be the clock source so we can read it with scope
    
    logic stb;
    logic [29:0] cnt = 0;
    always_ff @(posedge clk_out1) begin
        if (cnt != clkSpeed-1) begin
            stb <= 0;
            cnt <= cnt + 1;
        end else begin
            stb <= 1;
            cnt <= 0;
        end
    end

    always_ff @(posedge clk_out1) begin
        if (stb) begin
            led[0] <= led[0] + 1;
        end
    end
    
endmodule
```