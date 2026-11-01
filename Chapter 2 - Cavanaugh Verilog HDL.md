2.3 Design a structural module that will generate a high output z1 if a 4-bit binary number x1x2x3x4 has a value less than or equal to four or greater than eleven. Generate a Karnaugh map and obtain the equation for z1 in a sum-of-products form and for z2 in a product-of-sums form. Instantiate dataflow modules for the logic gates into the structural module. Obtain the design module, the test bench module for all combinations of the inputs, and the outputs

|     | 00  | 01  | 11  | 10  |
| --- | --- | --- | --- | --- |
| 00  | 1   | 1   | 1   | 1   |
| 01  | 1   | 0   | 0   | 0   |
| 11  | 1   | 1   | 1   | 1   |
| 10  | 0   | 0   | 0   | 0   |

$z_1 = x_1'x_2'+x_1x_2+x_2x_3'x_4'$

$z_2=(x_1'+x_2)(x_1+x_2'+x_4')(x_1+x_2'+x_3')$

```verilog
module top_level (x1, x2, x3, x4, z1, z2);
input   x1, x2, x3, x4;
output  z1, z2;

wire net1, net2, net3, net5, net6, net7
//make z1
and inst1 (net1, ~x1, ~x2);
    inst2 (net2, x1, x2);
    inst3 (net3, x2, ~x3, ~x4);
or  inst4 (z1, net1, net2, net3);
//make z2
or  inst5 (net5, ~x1, x2);
    inst6 (net6, x1, ~x2, ~x4);
    inst7 (net7, x1, ~x2, ~x3);
and inst8 (z2, net5, net6, net7);
endmodule
```
 
Obtain the Karnaugh map that represents the equation shown below. Then obtain the design module using built-in primitives, the test bench module, and the outputs for the equation shown below. In the same design module obtain the Verilog code for an equivalent equation using only exclusive-OR gates with logic gates that were designed using dataflow modeling.

$z1 = x1' x2' x3' x4 + x1' x2' x3x4' + x1' x2x3' x4' + x1' x2x3x4 + x1x2x3' x4 + x1x2x3x4' + x1x2' x3' x4' + x1x2' x3x4$

$z1=x3x4(x1x2' + x1'x2)+x1x2(x3'x4+x3x4')+x3'x4'(x1x2'+x1'x2)+x1'x2'(x3'x4+x4'x3)$
$=(x1 \oplus x2)(x3x4+x3'x4')+(x3 \oplus x4)(x1x2 + x1'x2')$
$=(x1 \oplus x2)(x3 \oplus x4)'+(x3 \oplus x4)(x1 \oplus x2)'$
$=(x1 \oplus x2 \oplus x3 \oplus x4)$

```verilog
module top_level (x1, x2, x3, x4, z1);
input   x1, x2, x3, x4;
output  z1;

wire net1, net2, net3, net4, net5, net6, net7, net8,
	 net9, net10, net11, net12, net13, net14, net15, net16;
	 
buf  (net1, x1);
not  (net2, x1);

buf  (net3, x2);
not  (net4, x2);

buf  (net5, x3);
not  (net6, x3);

buf  (net7, x4);
not  (net8, x4);

and  (net9, net2, net4, net6, net7),
	 (net10, net2, net4, net5, net8),
	 (net11, net2, net3, net6, net8),
	 (net12, net2, net3, net5, net7),
	 (net13, net1, net3, net6, net7),
	 (net14, net1, net3, net6, net7),
	 (net15, net1, net4, net6, net8),
	 (net16, net1, net4, net5, net7);

or   (z1, net9, net8, net9, net10, net11,
	 net12, net13, net14, net15, net16);
endmodule
```

```verilog

```

```verilog
//only use xor gates
module top_level_xor (x1, x2, x3, x4, z1);
input   x1, x2, x3, x4;
output  z1;

xor (z1, x1, x2, x3, x4);
endmodule
```
