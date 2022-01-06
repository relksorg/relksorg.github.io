---
layout: default
title: Verilog
parent: Code
nav_order: 2
---

# Verilog

---

### Orangecrab r0.2 (LFE5U-25F-8MG285C)
* Test blink LEDs attached to GPIO: 13, 12, 11, 10, 9, 6, 5

```verilog
`default_nettype none

/*
 *  led experimentations
 */

module top (
    input clk48,
    output rgb_led0_r,
    output rgb_led0_g,
    output rgb_led0_b,
    output gpio_13, gpio_12, gpio_11, gpio_10, gpio_9, gpio_6, gpio_5
);
    // Create a 27 bit register
    reg [26:0] counter = 0;

    // Every positive edge increment register by 1
    always @(posedge clk48) begin
        counter <= counter + 1;
    end

    // Output inverted values of counter onto LEDs
    assign rgb_led0_r = ~counter[24];
    assign rgb_led0_g = ~counter[25];
    assign rgb_led0_b = 1;

    assign gpio_13 = counter[26];
    assign gpio_12 = counter[25];
    assign gpio_11 = counter[26];
    assign gpio_10 = counter[25];
    assign gpio_9 = counter[26];
    assign gpio_6 = counter[25];
    assign gpio_5 = counter[26];


endmodule


```
