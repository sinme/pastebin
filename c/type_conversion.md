整理
https://www.securecoding.cert.org/confluence/display/seccode/INT02-C.+Understand+integer+conversion+rules

整型提升

比int小的整数类型在运算之前被转化为int.

如果原先类型的值可以完全用int表示, 那么就会被转化为int, 否则被转化为unsigned int.

这么做的目的是为了防止中间运算结果溢出:

signed char cresult, c1, c2, c3;
c1 = 100;
c2 = 3;
c3 = 4;
cresult = c1 * c2 / c3;

In this example, the value of c1 is multiplied by c2. The product of these values is then divided by the value of c3 (according to operator precedence rules). Assuming that signed char is represented as an 8-bit value, the product of c1 and c2 (300) cannot be represented. Because of integer promotions, however, c1, c2, and c3 are each converted to int, and the overall expression is successfully evaluated. The resulting value is truncated and stored in cresult. Because the final result (75) is in the range of the signed char type, the conversion from int back to signed char does not result in lost data.


算法转换的基本规则:
-   如果两个操作数是两个相同的类型, 不需要进一步转换.
-   如果连个操作数都是signed 或者 unsigned,
    类型rank比较底的被转化为rank高的
-   如果unsigned操作数的rank大于等于signed的操作数的rank, signed 操作数被转化为unsigned的.
-   如果signed操作数，能表示所有的unsigned操作数范围, unsigned 操作数被转化为signed.
-   否则,两个操作数都被转化为对应类型的unsigned

简单来说, 

如果类型一样，不用转.
如果符号一样，转化为rank大的.
如果符号不一样, signed 包含unsigned的范围, 转化为signed.
否则, 转为unsigned.

for example:
sizeof 'A' , 结果是4

int si = -1;
unsigned int ui = 1;
si < ui 为false, 因为si被转化为了unsigned.





Integer Conversion Rank

Every integer type has an integer conversion rank that determines how conversions are performed. The ranking is based on the concept that each integer type contains at least as many bits as the types ranked below it. The following rules for determining integer conversion rank are defined in the C Standard, Section 6.3.1.1 [ISO/IEC 9899:2011]:

    No two signed integer types shall have the same rank, even if they have the same representation.
    The rank of a signed integer type shall be greater than the rank of any signed integer type with less precision.
    The rank of long long int shall be greater than the rank of long int, which shall be greater than the rank of int, which shall be greater than the rank of short int, which shall be greater than the rank of signed char.
    The rank of any unsigned integer type shall equal the rank of the corresponding signed integer type, if any.
    The rank of any standard integer type shall be greater than the rank of any extended integer type with the same width.
    The rank of char shall equal the rank of signed char and unsigned char.
    The rank of _Bool shall be less than the rank of all other standard integer types.
    The rank of any enumerated type shall equal the rank of the compatible integer type.
    The rank of any extended signed integer type relative to another extended signed integer type with the same precision is implementation-defined but still subject to the other rules for determining the integer conversion rank.
    For all integer types T1, T2, and T3, if T1 has greater rank than T2 and T2 has greater rank than T3, then T1 has greater rank than T3.

The integer conversion rank is used in the usual arithmetic conversions to determine what conversions need to take place to support an operation on mixed integer types.
Usual Arithmetic Conversions

The usual arithmetic conversions are rules that provide a mechanism to yield a common type when both operands of a binary operator are balanced to a common type or the second and third operands of the conditional operator ( ? : ) are balanced to a common type. Conversions involve two operands of different types, and one or both operands may be converted. Many operators that accept arithmetic operands perform conversions using the usual arithmetic conversions. After integer promotions are performed on both operands, the following rules are applied to the promoted operands:

    If both operands have the same type, no further conversion is needed.
    If both operands are of the same integer type (signed or unsigned), the operand with the type of lesser integer conversion rank is converted to the type of the operand with greater rank.
    If the operand that has unsigned integer type has rank greater than or equal to the rank of the type of the other operand, the operand with signed integer type is converted to the type of the operand with unsigned integer type.
    If the type of the operand with signed integer type can represent all of the values of the type of the operand with unsigned integer type, the operand with unsigned integer type is converted to the type of the operand with signed integer type.
    Otherwise, both operands are converted to the unsigned integer type corresponding to the type of the operand with signed integer type. Specific operations can add to or modify the semantics of the usual arithmetic operations.

