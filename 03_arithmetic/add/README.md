# ADD Operation

## add1.asm

The program adds 120 and 10.

**Result:**

120 + 10 = 130

The result is `10000010` in binary.

### Flags

* **CF = 0:** There was no carry out of the most significant bit because 130 fits within an 8-bit unsigned value.
* **PF = 1:** The binary result `10000010` contains two 1s, which is an even number of 1 bits.
* **AF = 1:** There was a carry from bit 3 to bit 4 when adding the lower four bits.
* **ZF = 0:** The result is not zero.
* **SF = 1:** The most significant bit of the 8-bit result is 1.
* **OF = 1:** The signed 8-bit operands are positive, but the result 130 cannot be represented as a positive signed 8-bit number.

## add2.asm

The program adds 32000 and 500.

**Result:**

32000 + 500 = 32500

The result fits within a 16-bit unsigned and signed positive integer.

### Flags

* **CF = 0:** There was no carry out of bit 15 because 32500 is less than 65536.
* **PF = 0:** The low byte of the result is `0xF4`, which contains five 1 bits, giving odd parity.
* **AF = 0:** There was no carry from bit 3 to bit 4.
* **ZF = 0:** The result is not zero.
* **SF = 0:** The most significant bit of the 16-bit result is 0.
* **OF = 0:** 32000 + 500 = 32500, which is within the signed 16-bit range of -32768 to 32767.

## Conclusion

The ADD instruction changes the arithmetic flags according to the result of the addition. The flags can be different between the two programs because the operations use different operand sizes and produce different results.
