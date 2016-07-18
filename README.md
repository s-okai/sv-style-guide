# sv-style-guide
A SystemVerilog style guide.

## Packages, `import`, and `include`

### Do Not Use `include`
Using `include` to share code with other files should be avoided.
```
`include "sample_pkg.sv"
```
The `include` keyword creates a new definition of the code `sample_pkg.sv` each time it is used. This because `include` is a macro rather than a proper import, and simply "copies" the text contents of `sample_pkg.sv` into the file. This creates several issues, such as when using custom types as ports for modules or as the arguments of functions, as you have conflicting between the top-level/caller and the module/callee.

### Use Packages and `import`
`include` is a hold-over from Verilog, and should be replaced with packages and `import` in SystemVerilog.

Packages can be defined in the following manner:
```
package sample_pkg;
  // Definitions here...
endpackage
```

Symbols in packages can either be referenced using the package name
```
assign value = sample_pkg::SAMPLE_CONSTANT;
```
or by importing either individual symbols from the package
```
import sample_pkg::SAMPLE_CONSTANT;
...
assign value = SAMPLE_CONSTANT;
```
or the entire package
```
import sample_pkg::*;
...
assign value = SAMPLE_CONSTANT;
```

## `always`, `always_ff`, and `always_comb`

### Do Not Use `always`
Using `always` for sequential and combinational logic should be avoided.
```
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
```
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

## References
1. [SystemVerilog packages](http://www.asic-world.com/systemverilog/hierarchy1.html)
