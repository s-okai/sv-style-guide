# sv-style-guide
A SystemVerilog style guide.

## Packages, `import`, and `include`

### Do Not Use `include`
Using `include` to share code with other files should be avoided.
```verilog
`include "sample_pkg.sv"
```
The `include` keyword creates a new definition of the code `sample_pkg.sv` each time it is used. This because `include` is a macro rather than a proper import, and simply "copies" the text contents of `sample_pkg.sv` into the file. This creates several issues, such as when using custom types as ports for modules or as the arguments of functions, as you have conflicting between the top-level/caller and the module/callee.

### Use Packages and `import`
`include` is a hold-over from Verilog, and should be replaced with packages and `import` in SystemVerilog.

Packages can be defined in the following manner:
```systemverilog
package sample_pkg;
  // Definitions here...
endpackage
```

Symbols in packages can either be referenced using the package name
```systemverilog
assign value = sample_pkg::SAMPLE_CONSTANT;
```
or by importing either individual symbols from the package
```systemverilog
import sample_pkg::SAMPLE_CONSTANT;
...
assign value = SAMPLE_CONSTANT;
```
or the entire package
```systemverilog
import sample_pkg::*;
...
assign value = SAMPLE_CONSTANT;
```

## `always`, `always_ff`, and `always_comb`

### Do Not Use `always`
Using `always` for sequential and combinational logic should be avoided.
```verilog
// Old Verilog-style sequential block.
always @(posedge clk)
  begin
  ...
end

// Old Verilog-style combinational block.
always @(*)
  begin
  ...
end
```
Verilog-style `always` are not checked at compile time to verify that the code included synthesizes to true flip-flops or combinational logic. Warnings are often generated in synthesis tools when code does not map properly to flip-flops or combinational logic, but are often missed, and simulation tools will often remain silent.

### Use `always_ff` and `always_comb`
Use `always_ff` and `always_comb` instead. `always_ff` and `always_comb` blocks are verified by most tool chains at compile time and will throw errors if the code does not map to flip-flops or combinational logic. For example, using `always_comb` will cause compilation to fail if the code infers a latch while `always @(*)` will only throw a warning.
```systemverilog
// New SystemVerilog-style sequential block.
always_ff @(posedge clk)
  begin
  ...
end

// New SystemVerilog-style combinational block.
always_comb
  begin
  ...
end
```

## `wire`, `reg`, and `logic`

### Do Not Use `wire` and `reg`
Using `wire` and `reg` should be avoided.
```verilog
wire internal_wire;
reg internal_reg;
...
assign internal_wire = value_0;

always @(*)
  begin
  internal_reg = value_1;
end
```
The common convention in Verilog is to use `wire` for nodes driven outside of `always` blocks (such as those driven via `assign`), and `reg` for nodes driven in `always` blocks. This is convention is tedious and makes refactoring code difficult. It also fails to add any clarity, since `reg` could be a node driven by either combinational or sequential logic.

### Use `logic`
Use `logic` instead of `wire` and `reg`. Nodes of type `logic` can be driven either inside or outside of `always` blocks.
```systemverilog
logic node_0;
logic node_1;
...
assign node_0 = value_0;

always_comb
  begin
  node_1 = value_1;
end
```
It is also good practice to use `logic` for all output port, as it also allows them to be driven either inside or outside of `always` blocks.
```systemverilog
module sample_module (
    output logic output_0,
    output logic output_1
  );
  ...
  assign output_0 = value_0;
  
  always_comb
    begin
    output_1 = value_1;
  end
endmodule
```

## References
1. [SystemVerilog packages](http://www.asic-world.com/systemverilog/hierarchy1.html)
