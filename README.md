# ⚕ RHEA-Λ GATE FAMILY ♇♏
## Reversible · Multi-Radix · Glyph-Carrying Computational Cells  
for the RHEA-UCM Hamiltonian Symbolic Framework

<div align="center">

**Prior Art Established:** 1 December 2025  
**DOI:** [10.5281/zenodo.17783138](https://doi.org/10.5281/zenodo.17783138)  
**License:** [RHEA-Core Public Grant v1.0](LICENSE) – Attribution Required · No Derivatives · Sovereign IP  
**U.S. Provisional Patent Pending:** #63/796,404  

</div>

### Overview — The First Pentavalent Reversible Cell

The **RHEA-Λ (Lambda) Gate** is the world’s first:

- **reversible**  
- **multi-radix** (binary / ternary / pentary)  
- **entropy-aware**  
- **glyph-carrying**  
- **Hamiltonian-aligned**  
- **CMOS-compatible today**

A single physical gate dynamically switches between three thermodynamic faces:

| Mode | Meaning                            | Domain                  | States | Thermodynamic Profile      |
|------|------------------------------------|-------------------------|--------|----------------------------|
| 00   | Binary CMOS NAND                   | {0,1}³                  | 8      | Irreversible (clustered erasure) |
| 01   | Ternary reversible                 | ℤ₃ × ℤ₃ × ℤ₅            | 45     | **Zero-erasure**           |
| 10   | Pentary reversible                 | ℤ₅³                     | 125    | **Zero-erasure**           |

In higher-radix modes, the Λ-gate becomes a **finite-state Hamiltonian microflow** — perfectly bijective, divergence-free, and entropy-neutral.

This is the hardware embodiment of:  
- RHEA-UCM  
- Pentavalent Homeostatic Paradigm  
- RHEA-IC symbolic processors  
- Zadeian Sentinel trust-entropy computation  

### Repository Structure

| Path                                          | Description                                                                    |
|-----------------------------------------------|--------------------------------------------------------------------------------|
| [`paper/Roe_RHEA_Lambda_Reversible_Cell_2025.pdf`](paper/Roe_RHEA_Lambda_Reversible_Cell_2025.pdf) | Full 8-page specification paper (LaTeX source available on request) |
| [`src/rhea_reversible_gate.v`](src/rhea_reversible_gate.v)                 | Behavioral Verilog – Yosys / Vivado / Quartus / Genus compatible            |
| [`verification/check_reversibility.py`](verification/check_reversibility.py) | Exhaustive bijectivity proof (45 + 125 states)                             |
| [`LICENSE`](LICENSE)                                                        | RHEA-Core Public Grant v1.0 – full sovereign license text                 |

### Mathematical Core of the RHEA-Λ Gate

#### Ternary Reversible Mapping (ℤ₃ × ℤ₃ × ℤ₅)

**Forward**
```text
A' = A
B' = (B + A) mod 3
G' = (G + B) mod 5
Inverse (triangular → automatically bijective)
textA  = A'
B  = (B' − A') mod 3
G  = (G' − B)  mod 5

45 states
ΔS_env = 0
Hamiltonian-compatible

Pentary Reversible Mapping (ℤ₅³)
Forward
textA' = A
B' = (B + A) mod 5
G' = (G + B) mod 5
Inverse
textA  = A'
B  = (B' − A') mod 5
G  = (G' − B)  mod 5

125 states
Perfectly reversible

Pentavalent Homeostatic Cycle
textΨ — Perception        : A_in
               ↓
          Φ — Internal Model    : B_in
               ↓
          Δ — Adaptive Delta    : B_out - B_in
               ↓
          T — Trust Update      : G_out - B_in
               ↓
          S — Entropy/Glyph     : G_in
               ↑
        ← RHEA-Λ Hamiltonian Loop →
Verilog Implementation (Behavioral)
verilogmodule rhea_reversible_gate #(
    parameter DATA_WIDTH_BIN = 1
)(
    input  wire [1:0] mode,   // 00 binary, 01 ternary, 10 pentary
    input  wire [2:0] A_in,
    input  wire [2:0] B_in,
    input  wire [2:0] G_in,
    output reg  [2:0] A_out,
    output reg  [2:0] B_out,
    output reg  [2:0] G_out
);
    // mod-3 and mod-5 helpers
    function automatic [1:0] add_mod3(input [1:0] x, y);
        automatic reg [2:0] s = x + y;
        add_mod3 = (s >= 3) ? s - 3 : s[1:0];
    endfunction

    function automatic [2:0] add_mod5(input [2:0] x, y);
        automatic reg [3:0] s = x + y;
        add_mod5 = (s >= 5) ? s - 5 : s[2:0];
    endfunction

    always @* begin
        case (mode)
            2'b00: begin // binary NAND
                A_out = {2'b00, ~(A_in[0] & B_in[0])};
                B_out = 3'b000;
                G_out = G_in;
            end
            2'b01: begin // ternary
                A_out = {1'b0, A_in[1:0]};
                B_out = {1'b0, add_mod3(B_in[1:0], A_in[1:0])};
                G_out = add_mod5(G_in, {1'b0, B_in[1:0]});
            end
            2'b10: begin // pentary
                A_out = A_in;
                B_out = add_mod5(B_in, A_in);
                G_out = add_mod5(G_in, B_in);
            end
            default: {A_out,B_out,G_out} = {A_in,B_in,G_in};
        endcase
    end
endmodule
Reversibility Validation (Python)
Pythondef ternary_step(A,B,G): return A, (B+A)%3, (G+B)%5
def pentary_step(A,B,G): return A, (B+A)%5, (G+B)%5

def check_bijective(step_fn, sizes):
    nA,nB,nG = sizes
    seen = {}
    for A in range(nA):
        for B in range(nB):
            for G in range(nG):
                out = step_fn(A,B,G)
                if out in seen:
                    print("Collision:", (A,B,G), "vs", seen[out])
                    return
                seen[out] = (A,B,G)
    print(f"Bijective over {nA*nB*nG} states.")

check_bijective(ternary_step, (3,3,5))
check_bijective(pentary_step, (5,5,5))
Synthesis
Works today with Yosys, Vivado, Quartus, Cadence Genus
Bashyosys -p "read_verilog src/rhea_reversible_gate.v; synth -top rhea_reversible_gate; write_verilog synth.v"
License — RHEA-Core Public Grant v1.0

Attribution Required
No Derivatives
No Military or Commercial Use Without Explicit Written License
Sovereign IP — © 2025 Paul M. Roe

Full text: LICENSE
Author
Paul M. Roe
Architect of RHEA-UCM · RHEA-Λ · Zadeian Sentinel · RHEA-Crypt
TecKnows, Inc. — Lewiston, Maine, USA
ORCID: 0009-0005-6159-947X
U.S. Provisional Patent #63/796,404
“We don’t fight entropy. We shape it.”
Citation
bibtex@techreport{Roe2025RHEALambda,
  author      = {Paul M. Roe},
  title       = {The {RHEA}-Λ Gate Family: A Binary–Ternary–Pentary Reversible Gate for Hamiltonian Symbolic Computation in the {RHEA}-{UCM} Framework},
  year        = {2025},
  month       = dec,
  institution = {TecKnows, Inc.},
  doi         = {10.5281/zenodo.17783138},
  url         = {https://doi.org/10.5281/zenodo.17783138}
}
Welcome to the Pentavalent Era.
Post-Landauer computing begins here.
