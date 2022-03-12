---
layout: default
title: SystemVerilog
parent: Code
nav_order: 2
---

# SystemVerilog 

---

### Genesys-ZU-5EV

* Increased Pipeline example
* uses more flipfops 
* nicer on timing
* 100MHz implementation
* TERRIBLE FOR VERIFCATION - DO NOT RUN FOR LONG

```verilog
`timescale 1ns / 1ps

module euclids16Bit
(
  input wire logic en, rst, engage,
  output     logic [15:0] finalResult,
  input wire logic [15:0] xINPUT, yINPUT,
  input wire logic mainClk,
  output     logic DONE_FLAG
    );

    //assign led[3:0] = sw[3:0];

    localparam width = 16;

    //logic [width - 1 : 0] x = 16'd32337;
    //logic [width - 1 : 0] y = 16'd27;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;

    logic [width - 1 : 0] a;
    logic [width - 1 : 0] b;

    bit firstPos = 0;

    logic [width - 1 : 0] tempResult = 16'h0;

    //cut timing - *lame first attempt lol*
    logic [5:0] delays = 0;
    logic [15:0] bOnePlus = 0;

    logic [15:0] aAndJ = 0;
    logic [15:0] bAndJ = 0;

    bit aAndJ_Zero = 0;
    bit bAndJ_Zero = 0;

    bit rdy = 0;
    //end of timing attempts

    initial begin //test initial block
      DONE_FLAG <= 0;
    end

    always_ff @(posedge mainClk) begin

      case(rst)

      1'b0: begin

        if(en)begin

          if(engage) begin

            case(delays[5:0])

              3'b010: begin
                bOnePlus <= b + 1;
              end

              3'b011: begin
                rdy <= 1;

                aAndJ <= a % j;
                bAndJ <= b % j;
              end

              3'b100: begin
                if(!aAndJ) begin
                  aAndJ_Zero <= 1;
                end

                if(!bAndJ) begin
                  bAndJ_Zero <= 1;
                end
              end

              3'b111: begin
                rdy <= 0;
                aAndJ_Zero <= 0;
                bAndJ_Zero <= 0;
              end
            endcase

          if(xINPUT > yINPUT) begin
              a <= xINPUT;
              b <= yINPUT;
            end else begin
              a <= yINPUT;
              b <= xINPUT;
            end
            firstPos <= 1;
          end

          if(DONE_FLAG == 0 && engage && firstPos)begin
            if(rdy)begin
              
              if(j != bOnePlus) begin
                j <= j + 1;
                if(aAndJ_Zero) begin
                  if(bAndJ_Zero) begin
                    tempResult <= j;
                  end
                end
              end else begin //once j is equal to b + 1
                finalResult <= tempResult;
                DONE_FLAG <= 1;
              end
            
            end 
          end

        end

        if(!engage) begin
          j <= 16'b0000_0000_0001;
          firstPos <= 0;

          if(DONE_FLAG) DONE_FLAG <= 0;
        end

      end

      1'b1: begin
        DONE_FLAG <= 0;
        finalResult <= 0;
        j <= 16'b0000_0000_0000_0001;
        firstPos <= 0;
      end

      endcase

    end

    always_ff @(posedge mainClk) begin
      delays[2:0] <= delays[2:0] + 1;
    end

endmodule
```

```verilog
`timescale 1ns / 1ps

module top(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic CLK_RESET = 0;
    clk clk100MHz
    (
      .clk_out1(mainClk),
      .reset(CLK_RESET), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    bit enable = 1;
    bit rst = 0; //active high
    bit engage; //0 - write new values in, 1 - set new numbers and run program, repeat...
    logic [15:0] a = 16'd100;
    logic [15:0] b = 16'd15;
    bit DONE_ONE;
    bit DONE_TWO;
    bit DONE_THREE;
    bit DONE_FOUR;
    bit DONE_FIVE;
    bit DONE_SIX;
    bit DONE_SEVEN;
    bit DONE_EIGHT;
    bit DONE_NINE;
    bit DONE_TEN;
    bit DONE;


    logic [15:0] OUTPUT_ONE;
    logic [15:0] OUTPUT_TWO;
    logic [15:0] OUTPUT_THREE;
    logic [15:0] OUTPUT_FOUR;
    logic [15:0] OUTPUT_FIVE;
    logic [15:0] OUTPUT_SIX;
    logic [15:0] OUTPUT_SEVEN;
    logic [15:0] OUTPUT_EIGHT;
    logic [15:0] OUTPUT_NINE;
    logic [15:0] OUTPUT_TEN;

    assign engage = sw[0];
    //assign ledCheck[7:0] = OUTPUT_ONE[7:0] && OUTPUT_TWO[7:0] && OUTPUT_THREE[7:0] && OUTPUT_FOUR[7:0] && OUTPUT_FIVE[7:0] && OUTPUT_SIX[7:0] && OUTPUT_SEVEN[7:0] && OUTPUT_EIGHT[7:0] && OUTPUT_NINE[7:0] && OUTPUT_TEN[7:0];
    assign DONE = DONE_ONE && DONE_TWO && DONE_THREE && DONE_FOUR && DONE_FIVE && DONE_SIX && DONE_SEVEN && DONE_EIGHT && DONE_NINE && DONE_TEN;

    //make sure all hardware becomes implemented (verification)
    
    logic [29:0] storj = 0;
    logic [4:0] track = 0;
    always_ff @(posedge mainClk) begin
      led[0] <= DONE_TEN;
    end 

    euclids16Bit instance_one 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_ONE),
      .xINPUT(a),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_ONE)
    );

      euclids16Bit instance_two 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_TWO),
      .xINPUT(OUTPUT_ONE),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_TWO)
    );

      euclids16Bit instance_three 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_THREE),
      .xINPUT(OUTPUT_TWO),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_THREE)
    );

      euclids16Bit instance_four 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_FOUR),
      .xINPUT(OUTPUT_THREE),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_FOUR)
    );

      euclids16Bit instance_five 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_FIVE),
      .xINPUT(OUTPUT_FOUR),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_FIVE)
    );

      euclids16Bit instance_six 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_SIX),
      .xINPUT(OUTPUT_FIVE),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_SIX)
    );

        euclids16Bit instance_seven 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_SEVEN),
      .xINPUT(OUTPUT_SIX),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_SEVEN)
    );

        euclids16Bit instance_eight 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_EIGHT),
      .xINPUT(OUTPUT_SEVEN),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_EIGHT)
    );

        euclids16Bit instance_nine 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_NINE),
      .xINPUT(OUTPUT_EIGHT),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_NINE)
    );

        euclids16Bit instance_ten 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT_TEN),
      .xINPUT(OUTPUT_NINE),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE_TEN)
    );
    
endmodule
```

---

### Genesys-ZU-5EV

* Calling on Euclids algorithm as a dedicated module

```verilog
`timescale 1ns / 1ps

module top(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic CLK_RESET = 0;
    clk clk100MHz
    (
      .clk_out1(mainClk),
      .reset(CLK_RESET), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    bit enable = 1;
    bit rst = 0; //active high
    bit engage; //0 - write new values in, 1 - set new numbers and run program, repeat...
    logic [15:0] OUTPUT;
    logic [15:0] a = 16'd100;
    logic [15:0] b = 16'd15;
    bit DONE;

    assign engage = sw[0];
    assign led[0] = DONE;
    assign ledCheck[7:0] = OUTPUT[7:0];


    euclids16Bit instance_one 
    (
      .en(enable),
      .rst(rst),
      .engage(engage),
      .finalResult(OUTPUT),
      .xINPUT(a),
      .yINPUT(b),
      .mainClk(mainClk),
      .DONE_FLAG(DONE)
    );
    
endmodule
```

```verilog
`timescale 1ns / 1ps

module euclids16Bit
(
  input wire logic en, rst, engage,
  output     logic [15:0] finalResult,
  input wire logic [15:0] xINPUT, yINPUT,
  input wire logic mainClk,
  output     logic DONE_FLAG
    );

    //assign led[3:0] = sw[3:0];

    localparam width = 16;

    //logic [width - 1 : 0] x = 16'd32337;
    //logic [width - 1 : 0] y = 16'd27;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;

    logic [width - 1 : 0] a;
    logic [width - 1 : 0] b;

    bit firstPos = 0;

    logic [width - 1 : 0] tempResult = 16'h0;

    initial begin //test initial block
      DONE_FLAG <= 0;
    end

    always_ff @(posedge mainClk) begin

      case(rst)

      1'b0: begin

        if(en)begin

          if(engage) begin
            if(xINPUT > yINPUT) begin
              a <= xINPUT;
              b <= yINPUT;
            end else begin
              a <= yINPUT;
              b <= xINPUT;
            end
            firstPos <= 1;
          end

          if(DONE_FLAG == 0 && engage && firstPos)begin
            if(j != (b + 1)) begin
              j <= j + 1;
              if(a % j == 0) begin
                if(b % j == 0) begin
                  tempResult <= j;
                end
              end
            end else begin //once j is equal to b + 1
              finalResult <= tempResult;
              DONE_FLAG <= 1;
            end

          end

        end

        if(!engage) begin
          j <= 16'b0000_0000_0001;
          firstPos <= 0;

          if(DONE_FLAG) DONE_FLAG <= 0;
        end

      end

      1'b1: begin
        DONE_FLAG <= 0;
        finalResult <= 0;
        j <= 16'b0000_0000_0000_0001;
        firstPos <= 0;
      end

      endcase

    end

endmodule
```

---

### Genesys-ZU-5EV

* Manual installation of Block Ram for Xilinx US+ 
* Write random data to addresses
* Runs once

```verilog
`timescale 1ns / 1ps

module top(
  output     logic [15:0] gpio,
  input wire logic sysclk,
  input wire logic [3:0] sw,
  output     logic [3:0] led
    );

    assign led[0] = sw[0];
    logic clkHz;

  clk clk200MHz
     (
      .clk_out1(clkHz),
      .reset(sw[0]),
      .locked(led[1]),
      .clkINPUT(sysclk)
      );

  logic [15:0] bAddr = 0;
  logic [15:0] bWord = 0;
  wire logic [15:0] dOut;

  bit wenable = 0;
  bit enable = 1;

  //Calc size: 2^words * wordSize
  // 2^16 * 16 = 1,048,576 Bits
  // 1048576 / 8 = 131,072 Bytes
  blk_mem_gen_0 bramA (
        .clka(clkHz),    // input wire clka
        .ena(enable),      // input wire ena (CLOCK ENABLE - Enables read, write, and rst operations)
        .wea(wenable),      // input wire [0 : 0] wea (WRITE ENABLE)
        .addra(bAddr),  // input wire [15 : 0] addra
        .dina(bWord),    // input wire [15 : 0] dina
        .douta(dOut)  // output wire [15 : 0] douta
      );

  bit complete = 0;
  always_ff @(posedge clkHz) begin
    if(complete != 1) begin
      wenable <= 1;
      bAddr <= bAddr + 1;
      bWord <= 16'hAAAA;
      if(bAddr == 16'hFFFF) begin
        complete <= 1;
        wenable <= 0;
      end
    end else begin
      bAddr <= 16'h000A;
      gpio[15:0] <= dOut[15:0];
    end
  end

endmodule
```

---

### Genesys-ZU-5EV

* Working Euclids algorithm for multiple numbers

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000;
    logic [width - 1 : 0] denom = 16'h0000;

    logic [width - 1 : 0] x = 16'd32337;
    logic [width - 1 : 0] y = 16'd27;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;

    logic [width - 1 : 0] a;
    logic [width - 1 : 0] b;

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    always_ff @(posedge mainClk) begin


      if(!sw[3])begin

        if(!firstPos) begin
          if(x > y) begin
            a <= x;
            b <= y;
          end else begin
            a <= y;
            b <= x;
          end
          firstPos <= 1;
        end

        if(j != (b + 1)) begin
          j <= j + 1;
          if(a % j == 0) begin
            if(b % j == 0) begin
              ledCheck[7:0] <= j[7:0];
            end
          end
        end

      end else begin //if sw[3] is high
        //prep values for next run
        firstPos <= 0;
        j <= 16'b0000_0000_0001;

        case(sw[2:0])
          3'b000: begin
            x <= 16'd12335;
            y <= 16'd440; 
            //expected gcf : 5
          end

          3'b001: begin
            x <= 16'd54; 
            y <= 16'd936;
            //gcf : 18
          end

          3'b010: begin
            x <= 16'd1174;
            y <= 16'd432;
            //gcf : 2
          end

          3'b011: begin
            x <= 16'd32337;
            y <= 16'd27; 
            //gcf : 9 
          end

          3'b100: begin
            x <= 16'd100;
            y <= 16'd15;
            //gcf : 5
          end

        endcase
      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]


    dividend / divisor = num
    
      logic [15 : 0] remainder; //for divider IP
      logic [15 : 0] remainderSecondary; //for divider IP

      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      ); */

endmodule
```

---

### Genesys-ZU-5EV

* Working Euclids algorithm
* No use of Xilinx IP cores - besides CLK wizard
* 100MHz
* Euclids of two 16 bit numbers

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;

    logic [width - 1 : 0] x = 16'd32337;
    logic [width - 1 : 0] y = 16'd27;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;
    logic [15 : 0] remainder; //for divider IP
    logic [15 : 0] remainderSecondary; //for divider IP

    logic [15:0] a;
    logic [15:0] b;

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    logic [4:0] gcfComputeLoop = 5'b00000;

    always_ff @(posedge mainClk) begin
      if(!firstPos) begin
        if(x > y) begin
          a <= x;
          b <= y;
        end else begin
          a <= y;
          b <= x;
        end
        firstPos <= 1;
      end

      if(j != (b + 1)) begin
        j <= j + 1;
        if(a % j == 0) begin
          if(b % j == 0) begin
            ledCheck <= j;
            led[3:0] <= j[3:0];
          end
        end
      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]

    dividend / divisor = num
    */
    
      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Another failed Euclids algorithm implementation
* -> Timing? <-

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;
    logic [15 : 0] remainder; //for divider IP
    logic [15 : 0] remainderSecondary; //for divider IP

    logic [15:0] a;
    logic [15:0] b;

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    logic [4:0] gcfComputeLoop = 5'b00000;

    always_ff @(posedge mainClk) begin
      if(!firstPos) begin
        if(x > y) begin
          a <= x;
          b <= y;
        end else begin
          a <= y;
          b <= x;
        end
        firstPos <= 1;
        j <= a % y;
      end

      if(gcfComputeLoop == 2) begin
        if(j != 0) begin
          a <= b;
        end
      end

      if(gcfComputeLoop == 3)begin
        if(j != 0) begin
          b <= j;
        end
      end

      if(gcfComputeLoop == 4)begin
        if(j != 0)begin
          j <= a%b;
        end
      end

      ledCheck[7:0] <= b;


      gcfComputeLoop <= gcfComputeLoop + 1;


    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]

    dividend / divisor = num
    */
      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Binary value of *10000* is expected on ledCheck: CORRECT
* 16 in decimal

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;
    logic [15 : 0] remainder; //for divider IP
    logic [15 : 0] remainderSecondary; //for divider IP

    logic [15:0] a;
    logic [15:0] b;

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    logic [1:0] gcfComputeLoop = 2'b00;

    always_ff @(posedge mainClk) begin
      if(!firstPos) begin
        if(x > y) begin
          a <= x;
          b <= y;
        end else begin
          a <= y;
          b <= x;
        end
        firstPos <= 1;
      end

      if((!secondPos) && (firstPos)) begin
        if(j != (b + 1)) begin
          j <= j + 1;
        end else begin
          ledCheck[7:0] <= j;
        end
      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]

    dividend / divisor = num
    */
      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Validated Xilinx IP dividing core implementation 

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;
    logic [15 : 0] remainder; //for divider IP
    logic [15 : 0] remainderSecondary; //for divider IP

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    logic [1:0] gcfComputeLoop = 2'b00;

    always_ff @(posedge mainClk) begin
      if(btn[0]) begin
        dividend <= x;
        divisor <= y;
        //remainder 10 -> 0b1010
      end

      if(btn[1]) begin
        dividend <= 16'd2773;
        divisor <= 16'd32;
        //remainder 21 -> 0b10101
      end

      if(sw[3]) begin
        ledCheck[7:0] <= remainder[7:0];
      end
    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]

    dividend / divisor = num
    */
      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Failed attempt of implementing Euclids algo in SV
* -> Timing issue <-
* Uses Xilinx dividing core
* 350MHz clock - Xilinx clk wizard

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;
    bit checked = 0;
    bit e = 0;

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'b0000_0000_0000_0001;
    logic [width - 1 : 0] result = 16'h0000;
    logic [15 : 0] remainder; //for divider IP
    logic [15 : 0] remainderSecondary; //for divider IP

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;
    bit tValidOutSecondary;

    logic [1:0] gcfComputeLoop = 2'b00;

    always_ff @(posedge mainClk) begin

      if(sw[3]) begin

        e <= 1;

        case(sw[2:0])
          3'b000: begin
            num = 16'd1000;
            denom = 16'd15;
          end

          3'b001: begin
            num = 16'd912;
            denom = 16'd86;
          end

          default: begin
            num = 16'd0;
            denom = 16'd0;
          end
        endcase

      end

          if(e)begin

            if (num > denom) begin
              x <= num;
              y <= denom;
            end else begin
              x <= denom;
              y <= num;
            end

            checked <= 1;
            e <= 0;
            led[3:0] <= denom;

          end


      if(checked && (sw[3] == 0)) begin
        //ledCheck[7:0] <= remainder[7:0];
        //checked <= 0;

        case(gcfComputeLoop[1:0])
          2'b00:begin
            if(j != (y + 1))begin
              j <= j + 1;

              dividend <= j;
              divisor <= x;

              dividendSecondary <= j;
              divisorSecondary <= y;
            end
          end

          2'b10: begin //skip a cycle to allow loading
            if((remainder == 0) && (remainderSecondary == 0)) begin
              result[7:0] <= j[7:0];
              ledCheck[7:0] <= result[7:0]; //constantly gets set until j == (y+1)
            end
          end
        endcase

        gcfComputeLoop[1:0] <= gcfComputeLoop[1:0] + 1;

      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]

    dividend / divisor = num
    */
      logic [15:0] dividend = 1;
      logic [15:0] divisor = 1;
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisor),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividend),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

      logic [15:0] dividendSecondary = 1;
      logic [15:0] divisorSecondary = 1;
      div_gen_1 bitDividerSecondary (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(divisorSecondary),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(dividendSecondary),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOutSecondary),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainderSecondary)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Finding the remainder of a 16-bit number
* Implementation of Xilinx Divider IP core
* Faster demo

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;
    bit checked = 1;
    bit e = 0;

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'h0000;
    logic [15 : 0] remainder; //for divider IP

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;

    logic [1:0] gcfComputeLoop = 2'b00;

    always_ff @(posedge mainClk) begin

      if(sw[3]) begin

        e <= 1;

        case(sw[2:0])
          3'b000: begin
            num = 16'd1000;
            denom = 16'd15;
          end

          3'b001: begin
            num = 16'd912;
            denom = 16'd86;
          end

          default: begin
            num = 16'd0;
            denom = 16'd0;
          end
        endcase

      end

          if(e)begin

            if (num > denom) begin
              x <= num;
              y <= denom;
            end else begin
              x <= denom;
              y <= num;
            end

            checked <= 1;
            e <= 0;

          end


      if(checked) begin
        ledCheck[7:0] <= remainder[7:0];
        checked <= 0;
      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]
    */
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(y),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(x),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

---

### Genesys-ZU-5EV

* Finding the remainder of a 16-bit number
* Implementation of Xilinx Divider IP core

```verilog
`timescale 1ns / 1ps

module fractionSimple
  #(
  parameter numeratorOne = 16'd3_232,
  parameter denominatorOne = 16'd9_328,

  parameter numeratorTwo = 16'd32_1235,
  parameter denominatorTwo = 16'd12_998
  )(
  input wire logic [3:0] sw,
  input wire logic [4:0] btn,
  output     logic [3:0] led,
  output     logic [7:0] ledCheck,
  input wire logic sysclk
    );

    logic mainClk;
    logic lck;
    logic rst = 0;
    clk clk200MHz
    (
      .clk_out1(mainClk),
      .reset(rst), //input
      .locked(lck), //output
      .clkINPUT(sysclk)
    );

    /*logic [1999:0] num = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [1999:0] denom = 2000'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [1999:0] x = num[1999:0];
    logic [1999:0] y = denom[1999:0];
    logic [1999:0] r;*/

    //----------------------------------------------------------------------------------

    /*logic [127:0] num = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    logic [127:0] denom = 128'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;

    logic [127:0] x = num[127:0];
    logic [127:0] y = denom[127:0];
    logic [127:0] r;*/

    //----------------------------------------------------------------------------------

    /* beating HP-prime requirement 16384bit representation
       the bare minimum to beat is 2^8600bits (8600 bits)*/




    //practical high end
    //64 byte number - 264256 LUTs
    /*
    localparam e = 512;
    logic [e-1:0] num = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;
    //128 Fs
    logic [e-1:0] denom = 512'hFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF;*/

    /* stackoverflow:
    You have to think in hardware.

    When you write a <= b/c you are saying to the synthesis tool
    "I want a divider that can provide a result every clock cycle and has no intermediate pipline registers".

    If you work out the logic circuit required to create that it's very complex,
    especially for higher bit counts. Generally FPGAs won't have specialist hardware blocks for division
    so it would have to be implemented out of generic logic resources.
    It's likely to be both big (lots of luts) and slow (low fmax).
    */

    localparam width = 16;

    //64 bit hex stored - 64'h0000_0000_0000_0000;
    logic [width - 1 : 0] num = 16'h0000_;
    logic [width - 1 : 0] denom = 16'h0000;
    bit checked = 1;

    logic [28:0] e = 28'd0; //counter

    logic [width - 1 : 0] x = 16'd0100;
    logic [width - 1 : 0] y = 16'd0015;
    logic [width - 1 : 0] j = 16'h0000;
    logic [15 : 0] remainder; //for divider IP

    bit firstPos = 0;
    bit secondPos = 0;
    bit tValid = 1;
    bit tValidOut;

    logic [1:0] gcfComputeLoop = 2'b00;

    always_ff @(posedge mainClk) begin

      if(sw[3]) begin
        led[3] <= 1;
        //num = 16'd1000;
        //denom = 16'd15;

        case(sw[2:0])
          3'b000: begin
            num = 16'd1000;
            denom = 16'd15;
          end

          3'b001: begin
            num = 16'd912;
            denom = 16'd86;
          end

          default: begin
            num = 16'd0;
            denom = 16'd0;
          end
        endcase

      end

      if(led[3] == 1) begin                 //blink LED - notify user new data is loaded
        e <= e + 1;

        if(e[28]) begin
          led[3] <= 0;
          e <= 0;

          if (num > denom) begin
            x <= num;
            y <= denom;
          end else begin
            x <= denom;
            y <= num;
          end

          checked <= 1;

        end
      end

      if(checked) begin
        ledCheck[7:0] <= remainder[7:0];
        checked <= 0;
      end

    end

    /*m_axis_dout mapping:
    quotient [31:16]
    remainder [15:0]
    */
      div_gen_0 bitDivider (
        .aclk(mainClk),                                      // input wire aclk
        .s_axis_divisor_tvalid(tValid),                     // input wire s_axis_divisor_tvalid
        .s_axis_divisor_tdata(y),        // input wire [15 : 0] s_axis_divisor_tdata
        .s_axis_dividend_tvalid(tValid),                    // input wire s_axis_dividend_tvalid
        .s_axis_dividend_tdata(x),      // input wire [15 : 0] s_axis_dividend_tdata
        .m_axis_dout_tvalid(tValidOut),            // output wire m_axis_dout_tvalid
        .m_axis_dout_tdata(remainder)               // output wire [31 : 0] m_axis_dout_tdata
      );

endmodule
```

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