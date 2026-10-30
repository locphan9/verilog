1.1 Given the equation shown below, obtain the minimized equation for z1 in a product-of-sums notation and implement the equation using NAND gate built-in primitives. Obtain the design module, the test bench module, and the outputs. Output z1 is asserted high
$$z_1(x_1, x_2, x_3, x_4, x_5)=\sum_m(1,4,7,9,11,13)+\sum_d(5,14,15)$$

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 1   | 0   | 0   |
| 01  | 1   | -   | 1   | 0   |
| 11  | 0   | 1   | -   | -   |
| 10  | 0   | 1   | 1   | 0   |
For simplification we will make (11,10) = 0
We have (00,00), (00, 10), (10, 10), (10, 00) = 0, sum term

| Group                                      | Sum term         |
| ------------------------------------------ | ---------------- |
| (00,00), (00, 10), (10, 10), (10, 00) = 0  | $(x_2+x_4)$      |
| (00,11), (00,10) = 0                       | $(x_1+x_2+x_3')$ |
| (00,10), (01, 10), (11, 10), (10, 10) = 0  | $(x_3'+x_4)$     |
| (11, 00), (11, 10), (10, 10), (10, 00) = 0 | $(x_1'+x_4)$     |
So the Product of Sum would be $z_1=(x_2+x_4)(x_1+x_2+x_3')(x_3'+x_4)(x_1'+x_4)$

```verilog
//design for z1
module top_level_module_of_nand (x1, x2, x3, x4);
input     x1, x2, x3, x4;
output    z1;

nand  inst1 (net1, ~x2, ~x4);
	  inst2 (net2, ~x1, ~x2, x3);
	  inst3 (net3, x3, ~x4);
	  inst4 (net4, x1, ~x4);
	  inst5 (net5, net1, net2, net3, net4);
	  inst6 (z1, net5, net5);
endmoudle

```

```verilog
//testbench for z1
module tb_top_level_module_of_nand;
reg x1, x2, x3, x4;
wire z1;
top_level_module_of_nand (
	.x1(x1),
	.x2(x2),
	.x3(x3),
	.x4(x4),
	.z1(z1)
	);
initial
begin
	wire [4:0] invect;
	for (invect = 0; invect < 16; invect = invect + 1)
		begin
			{x1, x2, x3, x4} = invect [4:0];
			#10 $display ("x1 x2 x3 x4 = %b, z1 = %b",
						{x1, x2, x3, x4}, z1);
		end
end
endmodule
```

1.2 Obtain the design module using built-in primitives for the equations shown below. Obtain the test bench and outputs.
a) (x1 ⊻x2)x3'
b) (x1 ⊻x2)'⊻x3


|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 0   | 0   | 0   | 0   | 1   |
| 1   | 1   | 0   | 0   | 0   |
$z_1=x_1x_2'x_3'+x_1'x_2x_3'$
```verilog
module top_level_2_1 (x1, x2, x3, z1);
input x1, x2, x3;
output z1;

and inst1 (net1, x1, ~x2, ~x3);
	inst2 (net2, ~x1, x2, ~x3);
or  inst3 (z1, net1, net2);
endmodule

```

```verilog
module tb_top_level_2_1;
reg x1, x2, x3;
wire z1;

tb_top_level_2_1 (
	.x1(x1),
	.x2(x2),
	.x3(x3),
	.z1(z1)
	);
initial
begin
	wire [3:0] invect;
	for (invect = 0; invect < 8; invect = invect + 1) 
		begin
			{x1, x2, x3} = invect[4:0];
			#10 $display ("x1 x2 x3 = $b, z1 = %b",
							{x1, x2, x3}, z1);
		end
end
endmodule
```

b)

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 0   | 1   | 0   | 1   | 0   |
| 1   | 0   | 1   | 1   | 0   |
$z_1=(x_1+x_2+x_3')(x_1'+x_3)(x_2'+x_3)$
```verilog
//using AND and OR primitive
module top_level_2_1 (x1, x2, x3, z1);
input x1, x2, x3;
output z1;

or  inst1 (net1, x1, x2, x3);
	inst2 (net2, ~x1, x3);
	inst3 (net3, ~x2, x3);
or  inst4 (z1, net1, net2, net3);
endmodule

```

```verilog
//using AND, NOT, and XOR primitive
module top_level_2_1 (x1, x2, x3, z1);
input x1, x2, x3;
output z1;

xor  inst1 (net1, x1, x2);
not	 inst2 (net2, net1);
and	 inst3 (z1, net2, x3);

endmodule

```

```verilog
module tb_top_level_2_1;
reg x1, x2, x3;
wire z1;

tb_top_level_2_1 (
	.x1(x1),
	.x2(x2),
	.x3(x3),
	.z1(z1)
	);
initial
begin
	wire [3:0] invect;
	for (invect = 0; invect < 8; invect = invect + 1) 
		begin
			{x1, x2, x3} = invect[4:0];
			#10 $display ("x1 x2 x3 = $b, z1 = %b",
							{x1, x2, x3}, z1);
		end
end
endmodule
```


1.3 Use AND gate and OR gate built-in primitives to implement a circuit in a sum-of-products form that will generate an output z1 if an input is greater than or equal to 2 and less than 5; and also greater than or equal to 12 and less than 15. Then obtain the design module, test bench module, and outputs.


|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 0   | 1   | 1   |
| 01  | 1   | 0   | 0   | 0   |
| 11  | 1   | 1   | 0   | 1   |
| 10  | 0   | 0   | 0   | 0   |
$z_1=x_1'x_2'x_3+x_2x_3'x_4'+x_1x_2x_3'+x_1x_2x_4'$

```verilog
//only use built-in AND and OR primitive
module top_level (x1, x2, x3, x4);
input x1, x2, x3, x4;
output z1;

and inst1 (and1, ~x1, ~x2, ~x3);
	inst2 (and2, x2, ~x3, ~x4);
	inst3 (and3, x1, x2, ~x3);
	inst4 (and4, x1, x2, ~x4);
or  inst5 (z1, and1, and2, and3, and4);
endmodule

```

```verilog
//testbench for z1
module tb_top_level_module_of_nand;
reg x1, x2, x3, x4;
wire z1;
top_level_module_of_nand (
	.x1(x1),
	.x2(x2),
	.x3(x3),
	.x4(x4),
	.z1(z1)
	);
initial
begin
	wire [4:0] invect;
	for (invect = 0; invect < 16; invect = invect + 1)
		begin
			{x1, x2, x3, x4} = invect [4:0];
			#10 $display ("x1 x2 x3 x4 = %b, z1 = %b",
						{x1, x2, x3, x4}, z1);
		end
end
endmodule
```

1.4
Obtain the equation for a logic circuit that will generate a logic 1 on output z1 if a 4-bit unsigned binary number N = x1x2x3x4 satisfies the following criteria, where x4 is the low-order bit 2 < N <=6 or 11 <= N < 14 Use NOR user-defined primitives. Obtain the design module, the test bench module, and outputs.

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 0   | 1   | 0   |
| 01  | 1   | 1   | 0   | 1   |
| 11  | 1   | 1   | 0   | 0   |
| 10  | 0   | 0   | 1   | 0   |
$z_1=x_2x_3' + x_2'x_3x_4+x_1'x_2x_4'$

```verilog
module top_level_nor (x1, x2, x3, x4, z1);
input x1, x2, x3, x4;
output z1;

nor inst1 (net1, ~x2, x3);
	inst2 (net2, x2, ~x3, ~x4);
	inst3 (net3, x1, x4, ~x2);
	inst4 (net4, net1, net2, net3);
	inst5 (z1, net4, net4);
endmodule

```

```verilog
//testbench for z1
module tb_top_level_nor;
reg x1, x2, x3, x4;
wire z1;
top_level_module_of_nand (
	.x1(x1),
	.x2(x2),
	.x3(x3),
	.x4(x4),
	.z1(z1)
	);
initial
begin
	wire [4:0] invect;
	for (invect = 0; invect < 16; invect = invect + 1)
		begin
			{x1, x2, x3, x4} = invect [4:0];
			#10 $display ("x1 x2 x3 x4 = %b, z1 = %b",
						{x1, x2, x3, x4}, z1);
		end
end
endmodule
```

1.5  Obtain the minimal Boolean expression for a logic circuit that generates an output z1 whenever a 4-bit unsigned binary number N meets the following requirements: 
N is an odd number or N is evenly divisible by four. 
The format for N is: N = x1 x2 x3 x4, where x4 is the low-order bit. Then obtain the design module using user-defined primitives, the test bench module, and the outputs.

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 1   | 1   | 1   | 0   |
| 01  | 1   | 1   | 1   | 0   |
| 11  | 1   | 1   | 1   | 0   |
| 10  | 1   | 1   | 1   | 0   |
|     |     |     |     |     |


```verilog
primitive udp_odd (z1, x1, x2, x3, x4);
output z1;
input  x1, x2, x3, x4;

table

// x1 x2 x3 x4 : z1 
	? ? ? 1 : 1; // Any value ending with 1 is odd 
	? ? ? 0 : 0; // Any value ending with 0 is even
endtable

endprimitive

```

```verilog
primitive udp_divisible_4 (z1, x1, x2, x3, x4);
output z1;
input  x1, x2, x3, x4;

table
// x1 x2 x3 x4 : z1
   ?  ?  0  0  : 1;
   ?  ?  0  1  : 0;
   ?  ?  1  0  : 0;
   ?  ?  1  1  : 0;
endtable
endprimitive
```

```verilog
module top_level (x1, x2, x3, x4);
input x1, x2, x3, x4;
output z1;

udp_divisible_4 inst1 (net1, x1, x2, x3, x4);
udp_odd inst2 (net2, x1, x2, x3, x4);
or inst3 (z, net1, net2);

endmodule

```
refer to testbench of question 1.3

1.6 Design a modulo-8 counter using the D flip-flop that was designed in the edge-sensitive user-defined primitives section of this chapter. Use additional logic gate UDPs as necessary. Obtain the design module, the test bench module, and the outputs

```verilog
primitive udp_dff_edge1 (q, d, clk, rst_n); 
input d, clk, rst_n; 
output q; 
reg q; //q is internal storage 
//initialize q to 0 initial q = 0;
initial
	q = 0;
table
// d     clk   rst_n  :  q  :  q+;
0 (01) 1 : ? : 0; //(01) is rising edge 
1 (01) 1 : ? : 1; //rst_n = 1 means no rst 
1 (0x) 1 : 1 : 1; //(0x) is no change 
0 (0x) 1 : 0 : 0; 
? (?0) 1 : ? : -; //ignore negative edge //reset case when rst_n is 0 and clk has any transition 
? (??) 0 : ? : 0; //rst_n = 0 means reset //reset case when rst_n is 0. d & clk can be anything, q+=0 
? ? 0 : ? : 0; //reset case when 0 --> 1 transition on rst_n. Hold q+ state 
? ? (01) : ? : -; //non-reset case when d has any trans, but clk has no trans (??) ? 1 : ? : -; //clk = ?, means no edge
endtable
endprimitive

```

```verilog
module mod8_udp (rst_n, clk, y1, y2, y3);
input clk, rst_n;
output y1, y2, y3;
//flip-flop y1
and inst1 (net1, ~y1, y2, y3);
	inst2 (net2, y1, ~y2);
	inst3 (net3, y1, ~y3);
or  inst4 (net4, net1, net2, net3);
udp_dff_edge inst5 (y1, net4, clk, rst_n);
//flip-flop y2
xor inst6 (net5, y2, y3);
udp_dff_edgel inst7 (y2, net5, clk, rst_n);
//flip-flop y3
udp_dff_edgel inst8 (y3, ~y3, clk, rst_n);

endmodule
```

```verilog
//testbench for modulus
```


1.10 Design a 4:1 multiplexer using the conditional operator. The multiplexer inputs are defined as vectors. The select inputs are: select[1:0], the data inputs are: in [3:0], and the output is out. Obtain the design module, the test bench module, and outputs.

```verilog
module multiplex_4(in, out, select);
input [3:0] in;
input [1:0] select;
output out;

assign out = (select == 0) ? in[0] :
			(select == 1) ? in[1] : 
			(select == 2) ? in[2] : in[3];
endmodule

```

```verilog
module tb_multiplex_4;

    reg  [3:0] in;
    reg  [1:0] select;
    wire       out;

    // Instantiate Design Under Test (DUT)
    multiplex_4 uut (
        .in(in),
        .select(select),
        .out(out)
    );

    initial begin
        $monitor("Time = %0t | select = %b (%0d) | in = %b | out = %b", 
                 $time, select, select, in, out);

        // Test with fixed pattern in = 4'b1010
        in = 4'b1010;
        select = 2'b00; #10;
        select = 2'b01; #10;
        select = 2'b10; #10;
        select = 2'b11; #10;

        // Test with another pattern in = 4'b0101
        in = 4'b0101;
        select = 2'b00; #10;
        select = 2'b01; #10;
        select = 2'b10; #10;
        select = 2'b11; #10;

        $finish;
    end

endmodule
```

1.14 Design a binary-to-excess-3 code converter using user-defined primitives of the following types: udp_and2, udp_and3, udp_or3, and udp_or4. The binary code is labelled a, b, c, d, and the excess-3 code is labelled w, x, y, z, where d and z are the low-order bits of their respective codes. Binary codes above 1100 produce an excess-3 code of 0000. Obtain the design module, the test bench module, and outputs.
Example of excess-3 code converter truth table: https://www.geeksforgeeks.org/digital-logic/excess-3-code/


| a   | b   | c   | d   | w   | x   | y   | z   |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 0   | 0   | 0   | 0   | 0   | 0   | 1   | 1   |
| 0   | 0   | 0   | 1   | 0   | 1   | 0   | 0   |
| 0   | 0   | 1   | 0   | 0   | 1   | 0   | 1   |
| 0   | 0   | 1   | 1   | 0   | 1   | 1   | 0   |
| 0   | 1   | 0   | 0   | 0   | 1   | 1   | 1   |
| 0   | 1   | 0   | 1   | 1   | 0   | 0   | 0   |
| 0   | 1   | 1   | 0   | 1   | 0   | 0   | 1   |
| 0   | 1   | 1   | 1   | 1   | 0   | 1   | 0   |
| 1   | 0   | 0   | 0   | 1   | 0   | 1   | 1   |
| 1   | 0   | 0   | 1   | 1   | 1   | 0   | 0   |
| 1   | 0   | 1   | 0   | 1   | 1   | 0   | 1   |
| 1   | 0   | 1   | 1   | 1   | 1   | 1   | 0   |
| 1   | 1   | 0   | 0   | 1   | 1   | 1   | 1   |
| 1   | 1   | x   | 1   | 0   | 0   | 0   | 0   |
| 1   | 1   | 0   | 1   | 0   | 0   | 0   | 0   |

```verilog
primitive udp_and2(a, b, z);
input a, b;
output z;
assign z  = a & b;
endmodule
```

```verilog
primitive udp_and3(a, b, c, z);
input   a, b, c;
output  z;
assign z  = a & b & c;
endmodule
```

```verilog
primitive udp_or3(a, b, c, z);
input a, b, c;
output z;
assign z  = a | b | c; 
endmodule
```

```verilog
primitive udp_or4(a, b, c, d, z);
input a, b, c, d;
output z;
assign z  = a | b | c | d; 
endmodule
```

Table for $w$

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 0   | 0   | 0   |
| 01  | 0   | 1   | 1   | 1   |
| 11  | 1   | 0   | 0   | 0   |
| 10  | 1   | 1   | 1   | 1   |
$w=(a+b)(c+d+a)(a'+b'+d')(a'+b'+c')$
Table for x

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 1   | 1   | 1   |
| 01  | 1   | 0   | 0   | 0   |
| 11  | 1   | 0   | 0   | 0   |
| 10  | 0   | 1   | 1   | 1   |
$x=(c+d+b)(d'+b')(b'+c')$
Table for y

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 1   | 0   | 0   | 1   |
| 01  | 1   | 0   | 0   | 1   |
| 11  | 1   | 0   | 0   | 0   |
| 10  | 1   | 0   | 0   | 1   |
$y=(d')(a'+b'+c')$
Table for z

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 1   | 0   | 1   | 0   |
| 01  | 1   | 0   | 1   | 0   |
| 11  | 1   | 0   | 0   | 0   |
| 10  | 1   | 0   | 1   | 0   |
$z=(c+d')(c'+d)(a'+b'+d')$

```verilog
//use only udp_and2, udp_and3, udp_or3, and udp_or4
module binary_to_excess_3 (a, b, c, d, x, w, y, z);
input   a, b, c, d;
output  x, w, y, z;
//instantiate w=(a+b)(c+d+a)(a'+b'+d')(a'+b'+c')
udp_or2 inst1 (net1, a, b); 
udp_o3  inst2 (net2, ~a, ~b, ~d);
		inst3 (net3, c, d, a);
		inst4 (net4, ~a, ~b, ~c);
udp_and2 inst5 (net5, net, net2);
		 inst6 (net6, net3, net4);
		 inst7 (w, net5, net6);
//instantiate x=(c+d+b)(d'+b')(b'+c')
udp_or2 inst8 (net8, ~d, ~b);
		inst9 (net9, ~b, ~c);
udp_or3 inst10 (net10, c, b, d);
udp_and3 inst11 (x, net8, net9, net10);
//instantiate y=(d')(a'+b'+c')
udp_or3 inst12 (net12, ~a, ~b, ~c);
udp_and2 inst13 (y, net12, ~d);
//z=(c+d')(c'+d)(a'+b'+d')
udp_or2 inst14 (net14, c, ~d);
		inst15 (net15, ~c, d);
udp_or3 inst16 (net16, ~a, ~b, ~d);
udp_and3 inst17 (z, net15, net16);
endmodule
```
Refer to 1.3 for 4-input testbench

1.15 Perform left and right shift operations on the following two unsigned 8-bit operands: a_reg [7:0] and b_reg [7:0]. Execute a shift left of four bits on a_reg [7:0] and a shift right of three bits on b_reg [7:0]. Obtain the design module, the test bench module, and outputs.


```verilog
module shift_right(a_reg, b_reg);
input    [7:0] a_reg, b_reg;
output   [7:0] a_reg_out, b_reg_out;

assign a_reg_out = a_reg << 4;
assign b_reg_out = b_reg >> 3;

endmodule
```

```verilog
//refer to previous exercises for testbench
```
1.16 Use the conditional statements to design a circuit for the following expression: z1 = (x1 & x4) + (x2 & x3) + (x2 ~ ^ x4). Obtain the design module, the test bench module, and outputs.

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 0   | 0   | 0   | 0   |
| 01  | 1   | 0   | 1   | 1   |
| 11  | 1   | 1   | 1   | 1   |
| 10  | 0   | 0   | 0   | 0   |
$z1=(x1+x2)(x1+x3+x4')(x1'+x2)$
```verilog
module top_level_case_circuit (x1, x2, x3, x4);
input   x1, x2, x3, x4;
output  z1;
assign z1 = ({x1, x2} = 2'b11) ? 1'b1 :
			({x1, x2} = 2'b01) ? ({x3, x4} = 2'b01 ? 1'b0 : 1'b1) : 
			1'b0;
endmodule
```

```verilog
module tb_top_level_case_circuit;

reg x1, x2, x3, x4;
wire z1;


```

1.19 Design a module to execute the four shift operations of shift left logical (SLL), shift left algebraic (SLA), shift right logical (SRL), and shift right algebraic (SRA) using the case statement. The operands to be shifted are 8-bit operands. Obtain the test bench providing four shift amounts for each shift operation and obtain the outputs.

```verilog
module top_level (a, shift_code, shift_amount, shift_result);

input   [7:0] a;
input   [1:0] shift_code;
input   [2:0] shift_amount;
output  [7:0] shift_result;

reg   [7:0] reg_a;
reg   [7:0] shift_result;
reg   [15:0] sra_reg;

parameter sll = 2'b00,
		  sla = 2'b01,
		  srl = 2'b10,
		  sra = 2'b11;
		  
always @ (a or shift_code)
begin
	case (shift_code)
	sll:
	begin
	reg_a = a << shift_amount;
	shift_result = reg_a;
	end
	sla:
	begin
	reg_a = a;
	reg_a = reg_a << shift_ammount;
	reg_a[7] = a[7];
	shift_result = reg_a;
	end
	srl:
	begin
	reg_a = a >> shift_amount;
	shift_result = reg_a;
	end
	sra:
	begin
	pad_reg = {{8{a[7]}},a};
	pad_reg = pad_reg >> shift_amount;
	shift_result = pad_reg[7:0];
	end
ends

endmodule
```