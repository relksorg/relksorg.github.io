---
layout: default
title: SystemVerilog
parent: Code
nav_order: 1
---

# SystemVerilog 

---

### Genesys-ZU-5EV

* Variable Square-wave generation (1KHz, 500Hz, 250Hz | 1Hz, 500mHz, 250mHz)
* Clk IP core, 125MHz sysclk input, 50MHz out
* Switch clk reset *sw[3]*
* BTN clk variation control: *btn[0], *btn[1]*
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