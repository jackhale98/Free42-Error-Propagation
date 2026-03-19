# HP-42S/Free42 Uncertainty Propagation Calculator

A comprehensive uncertainty (error) propagation program for the HP-42S calculator and its modern simulators Free42 and Plus42. Perform calculations with measured values and their uncertainties, with automatic error propagation through mathematical operations.

## Features

- **17 uncertainty-aware functions** covering arithmetic, powers, logarithms, and trigonometry
- **Automatic error propagation** using standard statistical formulas
- **Complex number encoding** — values stored as `value + i×uncertainty`
- **Chainable operations** — functions behave like built-in calculator operations
- **Angle mode aware** — trig functions auto-detect DEG/RAD mode
- **Menu-driven interface** — three soft-key menus for easy access

## Quick Start

```
5 ENTER 0.2 XEQ "UEN"    → Encode 5.0 ± 0.2
3 ENTER 0.1 XEQ "UEN"    → Encode 3.0 ± 0.1  
XEQ "UMUL"               → Multiply: 15.0 ± 0.7906
XEQ "UVW"                → Display: "15+/-0.7906"
```

## Installation

### Free42 / Plus42 (Recommended)

1. Download `UNCERT.txt` from this repository
1. Open Free42 or Plus42 on your computer
1. Enter **PRGM mode** (Shift → PRGM)
1. **Copy** the entire contents of `UNCERT.txt`
1. **Paste** (Ctrl+V / Cmd+V) — the program loads automatically
1. Exit PRGM mode
1. Run `XEQ "U1"` to access the menu

### DM42 / DM42n

1. Download `UNCERT_DM42.txt` (HP-42S compatible version without FUNC)
1. Go to [Swiss Micros Online Encoder](https://technical.swissmicros.com/decoders/dm42/)
1. Paste the file contents and click “Encode »”
1. Download the `.raw` file
1. Copy to your DM42’s `/PROGRAMS/` folder
1. Import on the calculator

### Using txt2raw (Alternative)

Download Thomas Okken’s [txt2raw tool](https://thomasokken.com/free42/) and run:

```bash
txt2raw UNCERT.txt
```

This creates `UNCERT.raw` for direct import.

## Usage

### Encoding Values

Values with uncertainties are encoded as complex numbers where the real part is the value and the imaginary part is the uncertainty:

```
value ENTER uncertainty XEQ "UEN"
```

Example: To encode 25.0 ± 0.5:

```
25 ENTER 0.5 XEQ "UEN"    → Stack X: (25, 0.5)
```

### Performing Calculations

Once values are encoded, use the U-functions just like built-in operations:

```
@ Calculate (10 ± 0.5) × (2 ± 0.1)
10 ENTER 0.5 XEQ "UEN"
2 ENTER 0.1 XEQ "UEN"
XEQ "UMUL"                → Result: 20 ± 1.118
```

### Viewing Results

```
XEQ "UVW"     → Displays "value+/-uncertainty" on screen
XEQ "USPL"    → Splits complex into Y=value, X=uncertainty
```

### Chaining Operations

Functions preserve the stack like built-in operations, allowing chains:

```
@ Calculate ((a × b) + c) / d
[enter a±σa] [enter b±σb] UMUL
[enter c±σc] UADD
[enter d±σd] UDIV
UVW
```

## Function Reference

### Menu U1: Basic Operations

|Function|Description             |Formula                              |
|--------|------------------------|-------------------------------------|
|`UEN`   |Encode value±uncertainty|Y + iX                               |
|`UVW`   |View/display result     |Shows “value+/-uncertainty”          |
|`UADD`  |Add                     |σ = √(σ₁² + σ₂²)                     |
|`USUB`  |Subtract                |σ = √(σ₁² + σ₂²)                     |
|`UMUL`  |Multiply                |σ = |result| × √((σ₁/v₁)² + (σ₂/v₂)²)|
|`UDIV`  |Divide                  |σ = |result| × √((σ₁/v₁)² + (σ₂/v₂)²)|

### Menu U2: Powers & Logarithms

|Function|Description     |Formula                              |
|--------|----------------|-------------------------------------|
|`USQ`   |Square (x²)     |σ = |2v| × σᵢₙ                       |
|`USQRT` |Square root     |σ = σᵢₙ / (2√v)                      |
|`UINV`  |Inverse (1/x)   |σ = σᵢₙ / v²                         |
|`ULN`   |Natural log     |σ = σᵢₙ / v                          |
|`UEXP`  |Exponential (eˣ)|σ = eᵛ × σᵢₙ                         |
|`UPOW`  |Power (xʸ)      |σ = |xʸ| × √((y·σₓ/x)² + (ln(x)·σᵧ)²)|

### Menu U3: Trigonometry & More

|Function|Description   |Formula                       |
|--------|--------------|------------------------------|
|`USIN`  |Sine          |σ = |cos(v)| × σᵢₙ × k        |
|`UCOS`  |Cosine        |σ = |sin(v)| × σᵢₙ × k        |
|`UTAN`  |Tangent       |σ = σᵢₙ / cos²(v) × k         |
|`ULOG`  |Log base 10   |σ = σᵢₙ / (v × ln(10))        |
|`U10X`  |Power of 10   |σ = ln(10) × 10ᵛ × σᵢₙ        |
|`USPL`  |Split to reals|Returns Y=value, X=uncertainty|

*Note: k = π/180 in DEG mode, k = 1 in RAD mode (auto-detected)*

## Mathematical Background

This program implements standard uncertainty propagation based on partial derivatives. For a function f(x) with input uncertainty σₓ:

```
σ_f = |∂f/∂x| × σₓ
```

For functions of multiple variables with uncorrelated uncertainties:

```
σ_f = √( (∂f/∂x)²σₓ² + (∂f/∂y)²σᵧ² + ... )
```

### References

- JCGM 100:2008 — *Guide to the Expression of Uncertainty in Measurement (GUM)*
- Taylor, J.R. — *An Introduction to Error Analysis* (2nd ed.)
- [Wikipedia: Propagation of uncertainty](https://en.wikipedia.org/wiki/Propagation_of_uncertainty)

## Examples

### Example 1: Ohm’s Law

Calculate resistance R = V/I where V = 12.0 ± 0.1 V and I = 2.5 ± 0.05 A:

```
12 ENTER 0.1 XEQ "UEN"
2.5 ENTER 0.05 XEQ "UEN"
XEQ "UDIV"
XEQ "UVW"                 → "4.8+/-0.1058..."
```

Result: R = 4.80 ± 0.11 Ω

### Example 2: Kinetic Energy

Calculate KE = ½mv² where m = 2.0 ± 0.1 kg and v = 3.0 ± 0.2 m/s:

```
3 ENTER 0.2 XEQ "UEN"
XEQ "USQ"                 @ v² = 9 ± 1.2
2 ENTER 0.1 XEQ "UEN"
XEQ "UMUL"                @ m×v² = 18 ± 2.68
0.5 ENTER 0 XEQ "UEN"     @ 0.5 (exact, no uncertainty)
XEQ "UMUL"
XEQ "UVW"                 → "9+/-1.34..."
```

Result: KE = 9.0 ± 1.3 J

### Example 3: Trigonometry (Angle Uncertainty)

Calculate sin(30° ± 1°):

```
DEG                       @ Ensure DEG mode
30 ENTER 1 XEQ "UEN"
XEQ "USIN"
XEQ "UVW"                 → "0.5+/-0.0151..."
```

Result: sin(30°) = 0.500 ± 0.015

### Example 4: Compound Expression

Calculate √(a² + b²) where a = 3.0 ± 0.1 and b = 4.0 ± 0.1:

```
3 ENTER 0.1 XEQ "UEN"
XEQ "USQ"                 @ a² = 9 ± 0.6
4 ENTER 0.1 XEQ "UEN"
XEQ "USQ"                 @ b² = 16 ± 0.8
XEQ "UADD"                @ a² + b² = 25 ± 1.0
XEQ "USQRT"
XEQ "UVW"                 → "5+/-0.1..."
```

Result: 5.00 ± 0.10

## Technical Details

### Storage Format

Values are stored as complex numbers in rectangular form:

- Real part = measured value
- Imaginary part = absolute uncertainty (1σ standard deviation)

### Variables Used

The program uses these named variables as working storage:

|Variable  |Purpose                |
|----------|-----------------------|
|`UA`, `UB`|Input operand storage  |
|`V1`, `V2`|Extracted values       |
|`U1`, `U2`|Extracted uncertainties|
|`VR`      |Result value           |
|`T1`      |Temporary storage      |
|`TC`      |Angle conversion temp  |

### Requirements

- **Free42** or **Plus42** for full functionality (uses `FUNC` command)
- **DM42** or **HP-42S** with the `_DM42` version (no `FUNC`, stack may be affected)
- Calculator set to **rectangular** complex mode (not polar)

### Limitations

- Assumes uncorrelated (independent) uncertainties
- Uses linear approximation (first-order Taylor expansion)
- Not suitable for very large relative uncertainties (>10-20%)
- Trig functions assume uncertainty is in the same unit as the angle

## Files

|File             |Description                                     |
|-----------------|------------------------------------------------|
|`UNCERT.txt`     |Full version for Free42/Plus42 with FUNC command|
|`UNCERT_DM42.txt`|HP-42S compatible version for DM42              |
|`README.md`      |This documentation                              |

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

### Ideas for Enhancement

- [ ] Add inverse trig functions (UASIN, UACOS, UATAN)
- [ ] Add hyperbolic functions
- [ ] Add covariance support for correlated variables
- [ ] Add Monte Carlo validation mode
- [ ] Create Plus42 version with big display support

## License

This project is released under the [MIT License](LICENSE).

## Acknowledgments

- [Thomas Okken](https://thomasokken.com/free42/) for Free42 and Plus42
- [Swiss Micros](https://www.swissmicros.com/) for the DM42
- The HP calculator community for preserving RPN calculator culture

-----

*Made with ♥ for the RPN calculator community*