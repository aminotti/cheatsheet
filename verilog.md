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

```verilog
module top_mod (input in1, input in2, output cc, inout dd);

and_gate instance1(in1, in2, cc, dd); // instancie et appel par ordre
// ou
and_gate instance1(.c(cc), .d(dd), .a(in1), .b(in2)); // instancie et appel par nom

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
reg signed [0:7] signed_data;       // big-endian
wire [3:-2] z;  // négative are allowed
reg unsigned [7:0] unsigned_data;   // An unsigned 8-bit register
integer a;    // A 32-bit integer
real a;       // A real number

// ATTENTION déclaration de wire implicite toujours 1 bit
wire [2:0] a;
assign a = 3'b101;  // a = 101
assign b = a; // b = 1 et non 101 car b pas déclaré donc implicite donc 1 bit

// user defined
typedef enum {RED, GREEN, BLUE} color_t; // Define an enumerated type
color_t color;                           // Variable of type color_t

// Array
reg [7:0] mem [0:255]; // An array of 256 8-bit registers (256 unpacked elements, each of which is a 8-bit packed vector of reg)
reg [3:0] matrix [0:3][0:3]; // A 4x4 array of 4-bit registers

// Concaténation
input [15:0] in;
output [23:0] out;
assign {out[7:0], out[15:8]} = in;         // Swap two bytes. Right side and left side are both 16-bit vectors.
assign out = {in[7:0], in[15:8]};    // This is the same thing.
assign out = {in[7:0], 8'b10101010, in[15:8]};

// input [4:0] a, b, c, d, e, f
// output [7:0] w, x, y, z
assign {w, x, y, z} = {a, b, c, d, e, f, 2'b11};

// replication operator : {num{vector}}
{5{1'b1}}           // 5'b11111
{2{a,b,c}}          // The same as {a,b,c,a,b,c}
{3'd5, {2{3'd6}}}
```

* Access vector

```verilog
// bitwise operator can be used on whole vector
module bitwise_and_8x8 (
    input [7:0] a,
    input [7:0] b,
    output [7:0] c
);

assign c = a & b;

endmodule

// or on a slice
module bitwise_and_4x4 (
    input [7:0] a,
    output [3:0] c
);

assign c = a[3:0] & a[7:4];

endmodule

// or on a single bit
module bitwise_and_4x4 (
    input [1:0] a,
    output c
);

assign c = a[0] & a[1];

endmodule

// Slice vector part select
{in[sel*4+3], in[sel*4+2], in[sel*4+1], in[sel*4+0]};
// Equivaut à
in[sel*4+3 -: 4];
// Equivaut à
in[sel*4 +: 4];
```

### Expressions

| Type                   | Operators                                                   | Description                                       |
|------------------------|-------------------------------------------------------------|---------------------------------------------------|
| Arithmetic Expressions | `+`, `-`, `!` (boolea not), `/`, `%`                        | Used for mathematical operations                  |
| Logical Operators      | `&&`, `\|\|`, `!`                                           | Used for logical operations                       |
| Relational Expressions | `==`, `!=`, `<`, `<=`, `>`, `>=`                            | Used to compare values                            |
| Bitwise Expressions    | `&`, `\|`, `~` (bitwise not), `^`, `<<`, `>>`, `<<<`, `>>>` | Operate on individual bits of operands            |
| Conditional Expressions| `? :`                                                       | Similar to the ternary operator in C              |
| Autres                 | (), {}                                                      | priorité et concaténation                         |

*``()`` can be used too*

*A logical operation treats the operand as a boolean value (true = non-zero, false = zero) and produces a 1-bit output.*

### Assignments

 * continus vs Procedural
 * continus : only ``=`` and can assigned only to type wire
 * procedural (initial or always block) : ``=`` blockant ou ``<=`` non blockant (non blockant valeur assignée simultanément en sortie de block et donc modification dispo au prochain cycle)

```verilog
module constant_one (
    output wire d
 );
    reg a, b, c;

    initial begin
        a = 0;
        b = 1;
        c = a | b;
    end

    assign d = c;

endmodule
```

```verilog
module example (
    input wire clk,
    input wire a,
    input wire b,
    output wire o
);
    reg a_reg, b_reg;  // Declare reg types for internal logic
    reg o_reg;          // Output stored in reg

    // avec <= les registre sont updated apres la fin du block alway donc apres le clock edge
    always @(posedge clk) begin
        a_reg <= a;     // Non-blocking assignment
        b_reg <= b;     // Non-blocking assignment
        o_reg <= a_reg & b_reg;  // Non-blocking assignment,  a_reg & b_reg ancienne valeurs 
    end

    assign o = o_reg;  // temps réel, o changera de valeur dès que o_reg changera sa valeur

endmodule
```

### Abstraction levels

* Gate level (``not``, ``and``, ``or``, ``xor``, ``nand``, ``nor``, ``xnor``, ``buf``)
* Flow level (``assign``)
* Behavior level (procedural) : ``always`` block

#### Combinational vs Sequential

Sequential logic can be viewed as an extension of combinational logic that incorporates memory elements.

* Combinational Logic :
  * ``assign``
  * ``always @(*)``
* Sequential logic :
  * ``always @(posedge clk)`` or ``always @(negedge clk)``

***The left side of the assignment in an always or intial block must always be a reg type.***

#### Statement

##### If-Else Statement

 Multiple else if blocks (or even simple if-else statements) are synthesized as a multiplexer.

```verilog
module if_else (
    input wire [1:0] sel,
    input wire in,
    output reg out
);

always @(*) begin
    if (sel == 2'b00) begin
        out = in;
    end else if (sel == 2'b01) begin
        out = in;
    end else begin
        out = !in;
    end
end

endmodule

// Can be simplifly in case of one line statement
if (condition)
    // Single Statement
else
    // Single Statement
```

##### For Loop Statement

```verilog
module shift_register (
    input wire clk,
    input wire new_data,
    output reg [7:0] shift_reg
);

    integer i; 

    always @(posedge clk) begin
        for (i = 7; i > 0; i = i - 1) begin
            shift_reg[i] <= shift_reg[i - 1];
        end
        shift_reg[0] <= new_data;
    end

endmodule
```

##### Case Statement

The case statement should be the preferred option when selective execution is required. Unlike the if-else statement, the case statement is typically more efficiently synthesized and does not result in a chain of multiplexers.

```verilog
module arithmetic_unit (
    input wire [3:0] a,
    input wire [3:0] b,
    input wire operation,
    output reg [7:0] result
);

always @(*) begin
    case (operation)
        1'b0: result = a + b;
        1'b1:  begin
	                // Multiple statements
                  result = a - b;
               end
        default: result = '1;
    endcase
end

endmodule
```

### Macro & literals

```bash
<size>'<base><value>
```

* ``size`` : number of bits
* ``base`` : Number base of the literal:
 * ``'b`` = binary
 * ``'d`` = decimal
 * ``'h`` = hexadecimal
 * ``'o`` = octal
* ``value`` : The actual number in the specified base

```verilog
// Example
4'b1010
16'hABCD
8'bx // 8-bit unknown value
8'bz // 8-bit high-impedance value
8'b1010_1101 // You can use _ as a visual separator
out = '1; // Special literal, all bits of out are set to 1 ('0, 'x, and 'z are also valid)
```

```verilog
// constants.vh
`define ALU_MOD_ADD 3'b000
`define ALU_MOD_INC 3'b001
`define ALU_MOD_SUB 3'b010
```

```verilog
`include "constants.vh"

always @ (posedge i_clk) begin
	case(op_code)
		`ALU_MOD_ADD: {carry, o_c} = i_a + i_b;
		`ALU_MOD_INC: {carry, o_c} = i_a + 1;
		`ALU_MOD_SUB: o_c = i_a - i_b;
	endcase
end
```

## Implémentations

* Une **comparaison** revient à effecter un not **logique** sur un xnor **bitwise** ``eq = !(a ^ 3'b101)`` (``a`` etant sur 3 bits également, le not logic ``!`` renvoi 1 si égal mais le not bitwise``~`` renverait ``7`` aka ``3'b111``)
* **Addition** de 2 bits avec une retenue en entrée : ``sum = a & b & Cin``
* **Retenue** suite à une addition de 2 bits : ``Cout = (a & b) | (Cin & (a | b))``
* Desing en *pipeline* pour augmenter la fréquence
