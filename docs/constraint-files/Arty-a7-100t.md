---
layout: default
title: Arty A7-100T
parent: Constraint Files
nav_order: 2
---

# Arty A7-100T

```
set_property -dict { PACKAGE_PIN E3    IOSTANDARD LVCMOS33 } [get_ports { CLK100MHZ }]; #IO_L12P_T1_MRCC_35 Sch=gclk[100]
create_clock -add -name sys_clk_pin -period 10.00 -waveform {0 5} [get_ports { CLK100MHZ }];

set_property -dict { PACKAGE_PIN D9    IOSTANDARD LVCMOS33 } [get_ports { rst }]; #IO_L6N_T0_VREF_16 Sch=btn[0]
set_property -dict { PACKAGE_PIN C9    IOSTANDARD LVCMOS33 } [get_ports { btn_rst }]; #IO_L11P_T1_SRCC_16 Sch=btn[1]
set_property -dict { PACKAGE_PIN B9    IOSTANDARD LVCMOS33 } [get_ports { btn[1] }]; #IO_L11N_T1_SRCC_16 Sch=btn[2]
set_property -dict { PACKAGE_PIN B8    IOSTANDARD LVCMOS33 } [get_ports { btn[2] }]; #IO_L12P_T1_MRCC_16 Sch=btn[3]

set_property -dict { PACKAGE_PIN A8    IOSTANDARD LVCMOS33 } [get_ports { sw[0] }]; #IO_L12N_T1_MRCC_16 Sch=sw[0]
set_property -dict { PACKAGE_PIN C11   IOSTANDARD LVCMOS33 } [get_ports { sw[1] }]; #IO_L13P_T2_MRCC_16 Sch=sw[1]
set_property -dict { PACKAGE_PIN C10   IOSTANDARD LVCMOS33 } [get_ports { sw[2] }]; #IO_L13N_T2_MRCC_16 Sch=sw[2]
set_property -dict { PACKAGE_PIN A10   IOSTANDARD LVCMOS33 } [get_ports { sw[3] }]; #IO_L14P_T2_SRCC_16 Sch=sw[3]

set_property -dict { PACKAGE_PIN H5    IOSTANDARD LVCMOS33 } [get_ports { led[0] }]; #IO_L24N_T3_35 Sch=led[4]
set_property -dict { PACKAGE_PIN J5    IOSTANDARD LVCMOS33 } [get_ports { led[1] }]; #IO_25_35 Sch=led[5]
set_property -dict { PACKAGE_PIN T9    IOSTANDARD LVCMOS33 } [get_ports { led[2] }]; #IO_L24P_T3_A01_D17_14 Sch=led[6]
set_property -dict { PACKAGE_PIN T10   IOSTANDARD LVCMOS33 } [get_ports { led[3] }]; #IO_L24N_T3_A00_D16_14 Sch=led[7]

set_property -dict { PACKAGE_PIN N14   IOSTANDARD LVCMOS33 } [get_ports { dip[0] }]; #IO_L8P_T1_D11_14 Sch=ck_io[36]
set_property -dict { PACKAGE_PIN U17   IOSTANDARD LVCMOS33 } [get_ports { dip[1] }]; #IO_L17P_T2_A14_D30_14 Sch=ck_io[37]
set_property -dict { PACKAGE_PIN T18   IOSTANDARD LVCMOS33 } [get_ports { dip[2] }]; #IO_L7N_T1_D10_14 Sch=ck_io[38]
set_property -dict { PACKAGE_PIN R18   IOSTANDARD LVCMOS33 } [get_ports { dip[3] }]; #IO_L7P_T1_D09_14 Sch=ck_io[39]
set_property -dict { PACKAGE_PIN P18   IOSTANDARD LVCMOS33 } [get_ports { dip[4] }]; #IO_L9N_T1_DQS_D13_14 Sch=ck_io[40]
set_property -dict { PACKAGE_PIN N17   IOSTANDARD LVCMOS33 } [get_ports { dip[5] }]; #IO_L9P_T1_DQS_14 Sch=ck_io[41]
set_property -dict { PACKAGE_PIN F5    IOSTANDARD LVCMOS33 } [get_ports { dip[6] }]; #IO_0_35 Sch=ck_a[0]
set_property -dict { PACKAGE_PIN D8    IOSTANDARD LVCMOS33 } [get_ports { dip[7] }]; #IO_L4P_T0_35 Sch=ck_a[1]
set_property -dict { PACKAGE_PIN C7    IOSTANDARD LVCMOS33 } [get_ports { dip[8] }]; #IO_L4N_T0_35 Sch=ck_a[2]
set_property -dict { PACKAGE_PIN E7    IOSTANDARD LVCMOS33 } [get_ports { dip[9] }]; #IO_L6P_T0_35 Sch=ck_a[3]
set_property -dict { PACKAGE_PIN D7    IOSTANDARD LVCMOS33 } [get_ports { dip[10] }]; #IO_L6N_T0_VREF_35 Sch=ck_a[4]
set_property -dict { PACKAGE_PIN D5    IOSTANDARD LVCMOS33 } [get_ports { dip[11] }]; #IO_L11P_T1_SRCC_35 Sch=ck_a[5]
set_property -dict { PACKAGE_PIN B7    IOSTANDARD LVCMOS33 } [get_ports { dip[12]  }]; #IO_L2P_T0_AD12P_35 Sch=ad_p[12]
set_property -dict { PACKAGE_PIN B6    IOSTANDARD LVCMOS33 } [get_ports { dip[13]  }]; #IO_L2N_T0_AD12N_35 Sch=ad_n[12]
set_property -dict { PACKAGE_PIN E6    IOSTANDARD LVCMOS33 } [get_ports { dip[14]  }]; #IO_L5P_T0_AD13P_35 Sch=ad_p[13]
set_property -dict { PACKAGE_PIN E5    IOSTANDARD LVCMOS33 } [get_ports { dip[15]  }]; #IO_L5N_T0_AD13N_35 Sch=ad_n[13]

#set_property -dict { PACKAGE_PIN V15   IOSTANDARD LVCMOS33 } [get_ports { freqPinOne  }]; #IO_L16P_T2_CSI_B_14 Sch=ck_io[0]
#set_property -dict { PACKAGE_PIN U16   IOSTANDARD LVCMOS33 } [get_ports { freqPinTwo  }]; #IO_L18P_T2_A12_D28_14 Sch=ck_io[1]
set_property -dict { PACKAGE_PIN V15   IOSTANDARD LVCMOS33 } [get_ports { outWire[0]  }]; #IO_L16P_T2_CSI_B_14 Sch=ck_io[0]
set_property -dict { PACKAGE_PIN U16   IOSTANDARD LVCMOS33 } [get_ports { outWire[1]  }]; #IO_L18P_T2_A12_D28_14 Sch=ck_io[1]
set_property -dict { PACKAGE_PIN P14   IOSTANDARD LVCMOS33 } [get_ports { outWire[2]  }]; #IO_L8N_T1_D12_14 Sch=ck_io[2]
set_property -dict { PACKAGE_PIN T11   IOSTANDARD LVCMOS33 } [get_ports { outWire[3]  }]; #IO_L19P_T3_A10_D26_14 Sch=ck_io[3]
set_property -dict { PACKAGE_PIN R12   IOSTANDARD LVCMOS33 } [get_ports { outWire[4]  }]; #IO_L5P_T0_D06_14 Sch=ck_io[4]
set_property -dict { PACKAGE_PIN T14   IOSTANDARD LVCMOS33 } [get_ports { outWire[5]  }]; #IO_L14P_T2_SRCC_14 Sch=ck_io[5]
set_property -dict { PACKAGE_PIN T15   IOSTANDARD LVCMOS33 } [get_ports { outWire[6]  }]; #IO_L14N_T2_SRCC_14 Sch=ck_io[6]
set_property -dict { PACKAGE_PIN T16   IOSTANDARD LVCMOS33 } [get_ports { outWire[7]  }]; #IO_L15N_T2_DQS_DOUT_CSO_B_14 Sch=ck_io[7]
set_property -dict { PACKAGE_PIN N15   IOSTANDARD LVCMOS33 } [get_ports { outWire[8]  }]; #IO_L11P_T1_SRCC_14 Sch=ck_io[8]
set_property -dict { PACKAGE_PIN M16   IOSTANDARD LVCMOS33 } [get_ports { outWire[9]  }]; #IO_L10P_T1_D14_14 Sch=ck_io[9]
set_property -dict { PACKAGE_PIN V17   IOSTANDARD LVCMOS33 } [get_ports { outWire[10] }]; #IO_L18N_T2_A11_D27_14 Sch=ck_io[10]
set_property -dict { PACKAGE_PIN U18   IOSTANDARD LVCMOS33 } [get_ports { outWire[11] }]; #IO_L17N_T2_A13_D29_14 Sch=ck_io[11]
set_property -dict { PACKAGE_PIN R17   IOSTANDARD LVCMOS33 } [get_ports { outWire[12] }]; #IO_L12N_T1_MRCC_14 Sch=ck_io[12]
set_property -dict { PACKAGE_PIN P17   IOSTANDARD LVCMOS33 } [get_ports { outWire[13] }]; #IO_L12P_T1_MRCC_14 Sch=ck_io[13]
set_property -dict { PACKAGE_PIN U11   IOSTANDARD LVCMOS33 } [get_ports { outWire[14] }]; #IO_L19N_T3_A09_D25_VREF_14 Sch=ck_io[26]
set_property -dict { PACKAGE_PIN V16   IOSTANDARD LVCMOS33 } [get_ports { outWire[15] }]; #IO_L16N_T2_A15_D31_14 Sch=ck_io[27]
set_property -dict { PACKAGE_PIN M13   IOSTANDARD LVCMOS33 } [get_ports { outWire[16] }]; #IO_L6N_T0_D08_VREF_14 Sch=ck_io[28]
set_property -dict { PACKAGE_PIN R10   IOSTANDARD LVCMOS33 } [get_ports { outWire[17] }]; #IO_25_14 Sch=ck_io[29]
set_property -dict { PACKAGE_PIN R11   IOSTANDARD LVCMOS33 } [get_ports { outWire[18] }]; #IO_0_14 Sch=ck_io[30]
set_property -dict { PACKAGE_PIN R13   IOSTANDARD LVCMOS33 } [get_ports { outWire[19] }]; #IO_L5N_T0_D07_14 Sch=ck_io[31]
set_property -dict { PACKAGE_PIN R15   IOSTANDARD LVCMOS33 } [get_ports { outWire[20] }]; #IO_L13N_T2_MRCC_14 Sch=ck_io[32]
set_property -dict { PACKAGE_PIN P15   IOSTANDARD LVCMOS33 } [get_ports { outWire[21] }]; #IO_L13P_T2_MRCC_14 Sch=ck_io[33]
set_property -dict { PACKAGE_PIN R16   IOSTANDARD LVCMOS33 } [get_ports { outWire[22] }]; #IO_L15P_T2_DQS_RDWR_B_14 Sch=ck_io[34]
set_property -dict { PACKAGE_PIN N16   IOSTANDARD LVCMOS33 } [get_ports { notifyOut }]; #IO_L11N_T1_SRCC_14 Sch=ck_io[35]

#vga stuff
#set_property -dict {PACKAGE_PIN U14 IOSTANDARD LVCMOS33} [get_ports {vga_hsync}];
#set_property -dict {PACKAGE_PIN V14 IOSTANDARD LVCMOS33} [get_ports {vga_vsync}];
#set_property -dict {PACKAGE_PIN E15 IOSTANDARD LVCMOS33} [get_ports {vga_r[0]}];
#set_property -dict {PACKAGE_PIN E16 IOSTANDARD LVCMOS33} [get_ports {vga_r[1]}];
#set_property -dict {PACKAGE_PIN D15 IOSTANDARD LVCMOS33} [get_ports {vga_r[2]}];
#set_property -dict {PACKAGE_PIN C15 IOSTANDARD LVCMOS33} [get_ports {vga_r[3]}];
#set_property -dict {PACKAGE_PIN U12 IOSTANDARD LVCMOS33} [get_ports {vga_g[0]}];
#set_property -dict {PACKAGE_PIN V12 IOSTANDARD LVCMOS33} [get_ports {vga_g[1]}];
#set_property -dict {PACKAGE_PIN V10 IOSTANDARD LVCMOS33} [get_ports {vga_g[2]}];
#set_property -dict {PACKAGE_PIN V11 IOSTANDARD LVCMOS33} [get_ports {vga_g[3]}];
#set_property -dict {PACKAGE_PIN J17 IOSTANDARD LVCMOS33} [get_ports {vga_b[0]}];
#set_property -dict {PACKAGE_PIN J18 IOSTANDARD LVCMOS33} [get_ports {vga_b[1]}];
#set_property -dict {PACKAGE_PIN K15 IOSTANDARD LVCMOS33} [get_ports {vga_b[2]}];
#set_property -dict {PACKAGE_PIN J15 IOSTANDARD LVCMOS33} [get_ports {vga_b[3]}];
```