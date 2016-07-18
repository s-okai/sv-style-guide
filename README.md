# sv-style-guide
A SystemVerilog style guide.

## Packages, `import`, and `include`

### Do Not Use `include`
Using `include` to share code with other files should be avoided.
```
`include "sample_pkg.sv"
```
The `include` keyword creates a new definition of the code `sample_pkg.sv` each time it is used. This because `include` is a macro rather than a proper import, and simply "copies" the text contents of `sample_pkg.sv` into the file. This creates several issues, such as when using custom types as ports for modules or as the arguments of functions, as you have conflicting between the top-level/caller and the module/callee.

## References
1. [SystemVerilog packages](http://www.asic-world.com/systemverilog/hierarchy1.html)
