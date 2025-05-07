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
### Data type

| Value | Represents              | Description                                                                        |
|-------|-------------------------|------------------------------------------------------------------------------------|
| 0     | A logic zero, or false  | Represents a logic zero, indicating a false condition in the circuit.              |
| 1     | A logic one, or true    | Represents a logic one, indicating a true condition in the circuit.                |
| x     | An unknown logic value  | Denotes an unknown logic value, used when the logic state can't be determined.     |
| z     | A high-impedance state  | Represents a high-impedance state, meaning the output is effectively disconnected. |

```verilog
// net type : to represent connections between hardware components
wire a;       // A single wire
wire [3:0] b; // A 4-bit wire
tri a;       // A single wire, A tri-state wire that can be driven by multiple sources. It can be in one of three states: high, low, or high impedance (Z).

// Register types : to store values. Unlike nets, registers can hold their value until explicitly changed.
reg a;       // A single reg
reg [3:0] b;  // A 4-bit reg
reg signed [7:0] signed_data;       // A signed 8-bit register
reg unsigned [7:0] unsigned_data;   // An unsigned 8-bit register
integer a;    // A 32-bit integer
real a;       // A real number

// user defined
typedef enum {RED, GREEN, BLUE} color_t; // Define an enumerated type
color_t color;                           // Variable of type color_t

// Array
reg [7:0] memory [0:255]; // An array of 256 8-bit registers
reg [3:0] matrix [0:3][0:3]; // A 4x4 array of 4-bit registers
```

### Expressions

| Type                   | Operators                                                  | Description                                       |
|------------------------|------------------------------------------------------------|---------------------------------------------------|
| Arithmetic Expressions | `+`, `-`, `!` (boolea not), `/`, `%`                       | Used for mathematical operations                  |
| Logical Operators      | `&&`, `\|\|`, `!`                                            | Used for logical operations                       |
| Relational Expressions | `==`, `!=`, `<`, `<=`, `>`, `>=`                           | Used to compare values                            |
| Bitwise Expressions    | `&`, `\|`, `~` (bitwise not), `^`, `<<`, `>>`, `<<<`, `>>>` | Operate on individual bits of operands            |
| Conditional Expressions| `? :`                                                      | Similar to the ternary operator in C              |

*``()`` can be used too*
