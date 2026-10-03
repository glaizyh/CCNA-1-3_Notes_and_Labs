# Module 5: Number Systems

## 1. Binary Number System

### Binary and IPv4 Addresses
- **Binary** numbering system: only 1s and 0s, called **bits**.
- **Decimal** numbering system: digits 0 through 9.
- Hosts, servers, and network equipment use binary addressing to identify each other.
- An IPv4 address is a string of **32 bits**, divided into four sections called **octets**.
- Each octet has **8 bits (1 byte)**, separated by a dot.
- For people, the dotted binary is converted to **dotted decimal**.

### Binary Positional Notation
**Positional notation** means a digit represents different values depending on the position it occupies in the number.

**Decimal positional value table (radix 10)**

| Radix | 10 | 10 | 10 | 10 |
|-------|----|----|----|----|
| Position in number | 3 | 2 | 1 | 0 |
| Calculate | 10³ | 10² | 10¹ | 10⁰ |
| Position value | 1000 | 100 | 10 | 1 |
| Name | Thousands | Hundreds | Tens | Ones |

**Binary positional value table (radix 2)**

| Radix | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
|-------|---|---|---|---|---|---|---|---|
| Position in number | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
| Calculate | 2⁷ | 2⁶ | 2⁵ | 2⁴ | 2³ | 2² | 2¹ | 2⁰ |
| Positional value | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |

### Convert Binary to Decimal
Multiply each bit by its positional value, then add the results.

Example: `11000000.10101000.00001011.00001010`

| Octet | Calculation | Decimal |
|-------|-------------|---------|
| 11000000 | 128 + 64 | 192 |
| 10101000 | 128 + 32 + 8 | 168 |
| 00001011 | 8 + 2 + 1 | 11 |
| 00001010 | 8 + 2 | 10 |

**Result: 192.168.11.10**

### Convert Decimal to Binary
1. Start at the 128 position (the most significant bit).
2. Is the octet's decimal number **equal to or greater than** 128?
   - **No:** write a binary **0** in the 128 position and move to the 64 position.
   - **Yes:** write a binary **1** in the 128 position, **subtract 128** from the decimal number, and move to the 64 position.
3. Repeat through the 1 position.

**Example: convert 168 to binary**

| Position | Question | Answer | Bit | Remaining |
|----------|----------|--------|-----|-----------|
| 128 | Is 168 ≥ 128? | Yes | 1 | 168 - 128 = 40 |
| 64 | Is 40 ≥ 64? | No | 0 | 40 |
| 32 | Is 40 ≥ 32? | Yes | 1 | 40 - 32 = 8 |
| 16 | Is 8 ≥ 16? | No | 0 | 8 |
| 8 | Is 8 ≥ 8? | Equal (yes) | 1 | 8 - 8 = 0 |
| 4, 2, 1 | No values left | - | 0, 0, 0 | 0 |

**Result: 10101000**

## 2. Hexadecimal Number System

### Hexadecimal and IPv6 Addresses
- **Hexadecimal** is a base-16 system using digits 0-9 and letters A-F.
- It is easier to write a value as one hex digit than as four binary bits.
- Used for **IPv6 addresses** and **MAC addresses**.
- An IPv6 address is **128 bits**. Every 4 bits is one hex digit, so there are **32 hex digits**.
- **Hextet:** each group of four hexadecimal characters in an IPv6 address.

### Decimal, Binary, and Hexadecimal Chart

| Decimal | Binary | Hex |
|---------|--------|-----|
| 0 | 0000 | 0 |
| 1 | 0001 | 1 |
| 2 | 0010 | 2 |
| 3 | 0011 | 3 |
| 4 | 0100 | 4 |
| 5 | 0101 | 5 |
| 6 | 0110 | 6 |
| 7 | 0111 | 7 |
| 8 | 1000 | 8 |
| 9 | 1001 | 9 |
| 10 | 1010 | A |
| 11 | 1011 | B |
| 12 | 1100 | C |
| 13 | 1101 | D |
| 14 | 1110 | E |
| 15 | 1111 | F |

### Decimal to Hexadecimal
1. Convert the decimal number to an 8-bit binary string.
2. Split the binary string into groups of four, starting from the right.
3. Convert each group of four bits to its hex digit.

**Example: 168 to hex**
- 168 in binary is `10101000`
- Split: `1010` and `1000`
- `1010` = A, `1000` = 8
- **Result: A8**

### Hexadecimal to Decimal
1. Convert each hex digit to a 4-bit binary string.
2. Join them into an 8-bit binary grouping.
3. Convert the 8-bit binary to decimal.

**Example: D2 to decimal**
- D = `1101`, 2 = `0010`
- Joined: `11010010`
- 128 + 64 + 16 + 2 = 210
- **Result: 210**

## Key Terms

| Term | Meaning |
|------|---------|
| Dotted decimal notation | How IPv4 addresses are written: octets in decimal, separated by dots |
| Positional notation | A system where a digit's value depends on its position |
| Base 10 (decimal) | Uses 10 digits (0-9) |
| Base 2 (binary) | Uses 2 digits (0 and 1) |
| Base 16 (hexadecimal) | Uses 16 symbols (0-9 and A-F) |
| Radix | The base of a number system (10 decimal, 2 binary, 16 hex) |
| Octet | A section of 8 bits (1 byte) in an IPv4 address |
| Hextet | A 16-bit segment of four hex digits in an IPv6 address |
