# Verilog

## Setup

* Installer l'extension VS Code ``mshr-h.VerilogHDL`` de Masahiro Hiramori.

```bash
sudo apt update
sudo apt install iverilog gtkwave verilator
```
## Code

* code ``multiplexer_2_1.v`` :

```verilog
module multiplexer_2_1(
  input a,
  input b,
  input select,
  output y
);

assign y = (select)?b:a;
endmodule
```

* Testbench ``multiplexer_2_1_tb.v`` :

```verilog
`timescale 1ns/100ps 
module multiplexer_2_1_tb;

 //inputs
 reg a, b, select;
 //outputs
 wire y;

 multiplexer_2_1 u0_DUT(
  .a(a),
  .b(b),
  .select(select),
  .y(y)
 );

 //initialize inputs

 initial begin
//simulation files dumped to the test_2_1mux file
  $dumpfile("test_2_1mux.vcd");
  $dumpvars(0,multiplexer_2_1_tb);
  a=1'b0;b=1'b0; select=1'b0;
  #5 a=1'b1; 
  #5 select = 1'b1;
  #5 b=1'b1;
  #5 a=1'b0;
  #5 $finish;
 end
endmodule
```

## Simulation

```bash
# Compilation
iverilog -o mux_wave_2_1 multiplexer_2_1.v multiplexer_2_1_tb.v
# run
vvp mux_wave_2_1
# Visualize
gtkwave test_2_1mux.vcd
```

## Syntaxe

### Module

```verilog
module and_gate (
    input a, // wire by default
    input b,
    output c,
    inout d
);

  assign c = a & b;
  assign d = d & c;

endmodule
```
