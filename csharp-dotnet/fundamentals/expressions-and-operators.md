# Operator table

|Category|Operator symbol|Operator name|Example
|--|--|--|--
|Primary|.|Member access|x.y
||?. and ?[]|Null-conditional|x?.y or x?[0] 
||! (postfix)|Null-forgiving|x!.y or x![0]
||-> (unsafe)|Pointer to struct|x->y 
||()|Function call|x() 
||[]|Array/index|a[x]
||++|Post-increment|x++
||−−|Post-decrement|x−− 
||new|Create instance|new Foo() 
||stackalloc|Stack allocation|stackalloc(10) 
||typeof|Get type from identifier|typeof(int) 
||nameof|Get name of identifier|nameof(x) 
||checked|Integral overflow check on|checked(x) 
||unchecked|Integral overflow check off|unchecked(x) 
||default|Default value|default(char) 
|Unary|await|Await|await myTask
||sizeof|Get size of struct|sizeof(int) 
||+|Positive value of|+x 
||−|Negative value of|−x 
||!|Not|!x 
||~|Bitwise complement|~x 
||++|Pre-increment|++x 
||−−|Pre-decrement|−−x 
||()|Cast|(int)x 
||^|Index from end|array[^1]
||* (unsafe)|Value at address|*x 
||& (unsafe)|Address of value|&x 
|Range|..|Range of indices|x..y
||..^||x..^y
Switch & with|switch|Switch expression|num switch {<br>  1 => true,<br>  _ => false<br>}
||with|With expression|rec with { X = 123 }
|Multiplicative|*|Multiply|x * y 
||/|Divide|x / y 
||%|Remainder|x % y 
|Additive|+|Add|x + y 
||−|Subtract|x − y 
|Shift|<<|Shift left|x << 1 
||>>|Shift right|x >> 1 
|Relational|<|Less than|x < y 
||>|Greater than|x > y 
||<=|Less than or equal to|x <= y 
||>= Greater than or equal to|x >= y 
||is|Type is or is subclass of|x is y 
||as|Type conversion|x as y 
|Equality|==|Equals|x == y 
||!=|Not equals|x != y 
|Logical And|&|And|x & y 
|Logical Xor|^|Exclusive Or|x ^ y 
|Logical Or|\||Or|x \|y 
|Conditional And|&&|Conditional And|x && y
|Conditional Or|\|\||Conditional Or|x \|\|y 
|Null coalescing|??|Null coalescing|x ?? y
|Conditional|?:|Conditional|isTrue ? thenThis : elseThis
|Assignment and lambda|=|Assign|x = y
||*=|Multiply self by|x *= 2
||/=|Divide self by|x /= 2
||%=|Remainder & assign to self|x %= 2 
||+=|Add to self|x += 2
||−=|Subtract from self|x −= 2
||<<=|Shift self left by|x <<= 2
||>>=|Shift self right by|x >>= 2
||&=|And self by|x &= 2
||^=|Exclusive-Or self by|x ^= 2
||\|=|Or self by|x \|= 2
||??=|Null-coalescing assignment|x ??= 0 
||=>|Lambda|x => x + 1 