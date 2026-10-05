# MUL Operation

## mul1.asm

The program multiplies 25 by 10.

**Result:**

25 × 10 = 250

The 8-bit multiplication produces a 16-bit result in AX. The result is 250, so the high byte of AX is zero.

### Flags

* **CF = 0:** The upper half of the result is zero, so no significant bits were lost from the multiplication.
* **OF = 0:** The result fits within the original 8-bit operand size.
* **ZF, SF, PF and AF:** These flags are not defined by the MUL instruction, so their values should not be used to describe the result of the multiplication.

## mul2.asm

The program multiplies 3000 by 200.

**Result:**

3000 × 200 = 600000

The 32-bit result is stored in DX:AX. In this case:

* **DX = 9**
* **AX = 10176**

Together, DX:AX represents 600000.

### Flags

* **CF = 1:** The upper half of the result in DX is not zero, meaning the multiplication produced a result that does not fit within 16 bits.
* **OF = 1:** The result does not fit within the original 16-bit operand size.
* **ZF, SF, PF and AF:** These flags are not defined by the MUL instruction, so their values should not be used to describe the result.

## Conclusion

For MUL, CF and OF indicate whether the result fits within the size of the original operands. If the upper half of the result is non-zero, both CF and OF are set.
