# FPGA Programming

This repository provides a **minimal, fast, and fully open-source Makefile-based FPGA build system** for the **Sipeed Tang Nano 9K (GW1NR-LV9QN88PC6/I5)** development board using:

- **Yosys** → Synthesis
- **nextpnr-himbaechel (gowin)** → Place & Route
- **gowin_pack** → Bitstream generation
- **openFPGALoader** → FPGA programming

This flow avoids vendor IDEs entirely and enables **scriptable, reproducible, CI-friendly FPGA builds**.

---

## Features

- 🚀 Fully open-source toolchain
- ⚡ One-command FPGA build
- 🧱 Clean separation of synthesis, P&R, and packing
- 🔁 Optional simulation flow
- 🔌 Direct USB programming using openFPGALoader
- 📦 Minimal, readable Makefile

---

## Project Structure

```
.
├── Makefile
├── src/
│   └── top.v
├── constraints/
│   └── tangnano9k.cst
├── sim/
│   └── top_tb.v      # optional
└── build/
```

---

## Requirements

Install the **OSS CAD Suite**, which includes:

- yosys
- nextpnr-himbaechel
- gowin_pack
- openFPGALoader

### Install oss-cad-suite

```bash
cd ~
wget https://github.com/YosysHQ/oss-cad-suite-build/releases/latest/download/oss-cad-suite-linux-x64.tgz
tar -xzf oss-cad-suite-linux-x64.tgz
mv oss-cad-suite-linux-x64 oss-cad-suite
```

Add to PATH:

```bash
echo 'export PATH="$HOME/oss-cad-suite/bin:$PATH"' >> ~/.bashrc
exec bash
```

Verify:

```bash
yosys -V
nextpnr-himbaechel --help
gowin_pack -h
openFPGALoader --help
```

---

## Build Flow Overview

```
Verilog → Yosys → JSON netlist
        → nextpnr → Routed JSON
        → gowin_pack → .fs bitstream
        → openFPGALoader → FPGA
```

---

## Usage

### Build Everything (Default)

```bash
make
```

Produces:

```
build/blinky.fs
```

---

### Synthesis Only

```bash
make synth
```

---

### Place & Route Only

```bash
make pnr
```

---

### Generate Bitstream Only

```bash
make pack
```

---

### Program FPGA

```bash
make program
```

---

### Run Simulation (Optional)

Requires:

```
sim/top_tb.v
```

Run:

```bash
make sim
```

---

### Clean Build Artifacts

```bash
make clean
```

---

## Customization

### Change Project Name

```makefile
PROJECT = blinky
```

### Change Top Module

```makefile
TOP_MODULE = top
```

### Change Board Constraint File

```makefile
CONSTRAINT_FILE = constraints/tangnano9k.cst
```

---

## Example: Blinky

Minimal LED blinker example:

```verilog
module top(
    input  wire clk,
    output wire led
);

    reg [23:0] cnt = 0;

    always @(posedge clk)
        cnt <= cnt + 1;

    assign led = cnt[23];

endmodule
```

---

## Toolchain Details

| Stage       | Tool               |
| ----------- | ------------------ |
| Synthesis   | yosys              |
| P&R         | nextpnr-himbaechel |
| Packing     | gowin_pack         |
| Programming | openFPGALoader     |
