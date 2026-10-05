# SUB Operation

## sub1.asm

The program subtracts 80 from 50.

**Result:**

50 - 80 = -30

Since the operation uses 8-bit registers, the result is stored as the two's complement value `11100010`, which is 226 when interpreted as unsigned.

### Flags

* **CF = 1:** A borrow was required because 80 is greater than 50 in an unsigned subtraction.
* **PF = 1:** The binary result `11100010` contains four 1 bits, giving even parity.
* **AF = 0:** There was no borrow from bit 4.
* **ZF = 0:** The result is not zero.
* **SF = 1:** The most significant bit of the 8-bit result is 1, indicating a negative signed result.
* **OF = 0:** 50 - 80 = -30, which is within the signed 8-bit range of -128 to 127.

## sub2.asm

The program subtracts 2000 from 1000.

**Result:**

1000 - 2000 = -1000

The 16-bit result is stored as the two's complement value `0xFC18`, which is 64536 when interpreted as unsigned.

### Flags

* **CF = 1:** A borrow was required because 2000 is greater than 1000.
* **PF = 1:** The low byte of the result is `0x18`, which contains two 1 bits, giving even parity.
* **AF = 0:** There was no borrow from bit 4.
* **ZF = 0:** The result is not zero.
* **SF = 1:** The most significant bit of the 16-bit result is 1, indicating a negative signed result.
* **OF = 0:** 1000 - 2000 = -1000, which is within the signed 16-bit range of -32768 to 32767.

## Conclusion

The SUB instruction sets the flags according to the result of the subtraction. In both programs, a borrow occurs because the second operand is larger than the first, resulting in CF being set.
