# DIV Operation

## div1.asm

The program divides 100 by 7.

**Result:**

100 ÷ 7 = 14 remainder 2

For 8-bit division, the quotient is stored in AL and the remainder is stored in AH.

* **AL = 14**
* **AH = 2**

### Flags

The arithmetic flags are **undefined** after the DIV instruction. Therefore, the values displayed by GDB for CF, PF, AF, ZF, SF and OF cannot be used to determine the result of the division.

* **CF, PF, AF, ZF, SF and OF:** Undefined after DIV.
* **IF:** This flag is set, but it is not a result of the DIV operation. It controls hardware interrupt handling and should not be used to explain the division result.

The quotient 14 fits within the 8-bit AL register, so the division completes successfully.

## div2.asm

The program divides 50000 by 300.

**Result:**

50000 ÷ 300 = 166 remainder 200

For 16-bit division, the dividend is stored in DX:AX. The quotient is stored in AX and the remainder is stored in DX.

* **AX = 166**
* **DX = 200**

### Flags

The arithmetic flags are **undefined** after the DIV instruction. Therefore, the values displayed by GDB for CF, PF, AF, ZF, SF and OF cannot be used to determine the result of the division.

* **CF, PF, AF, ZF, SF and OF:** Undefined after DIV.
* **IF:** This flag is set, but it is not a result of the DIV operation. It controls hardware interrupt handling and should not be used to explain the division result.

The quotient 166 fits within the 16-bit AX register, so the division completes successfully.

## Conclusion

Unlike ADD and SUB, the DIV instruction does not define the arithmetic status flags. Their values after division are therefore not reliable indicators of the division result. The important outputs of DIV are the quotient and remainder stored in the appropriate registers.
