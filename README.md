# Compiler<br/>

**Basic compiler theory**.<br/>
G = (V, T, P, S) where G = grammer, v = variable, T = terminal, P = production, and S = start symbol<br/>
<br/>
Ex: E => E+E | E\*E | id ..Lets derive id+id+id   (assume input as 2+3+4)<br/>
LISP representation 1:	ADD((2) (ADD ((3) (4))))<br/>
LISP representation 2:	ADD((ADD ((2)(3))) (4))<br/>
<br/>
Above grammer is ambigous as it has 2 AST for same expression. The problem with grammer that it is not aware of ASSOCIATIVITY RULES of '+'. + is left associative because it needs left most expression to evaluate first.<br/>
From above, representation 2 is correct.<br/>
<br/>
Let try id\*id+id. (assume input 2\*3+4)<br/>
LISP representation 1:	MUL((id) (ADD ((3) (4))))<br/>
LISP representation 2:  ADD((MUL ((2) (3))) (4))<br/>
Above grammer is ambigous as it has 2 AST for same expression. The problem with the grammer is that is not aware of PRECEDENCE RULES OF OPERATOR. <br/>
As we know that '\*' has higher precedence then '+', '\*' needs to be evaluated first. Hence representation 2 is right.<br/>
<br/>
Another example of ambigous grammer:
Ex1	S => aS|Sa|a  input = aa, since 2 parse trees, hence grammer is ambigous
	S -> (aS) -> (a(a)) 1st parse tree
	S -> (Sa) -> ((a)a) 2nd parse tree
	
Ex2	S -> aSbS | bSaS | e   input ab, Grammer is not ambigous for input ab
	S -> aSbS -> aebS -> aebe
	
	input = abab, so 2 parse tree and grammer is ambigous
	S -> aSbS -> aebS -> aeb(aSbS) -> aeb(aebS) -> aebaebe = abab
	S -> a(bSaS)bS -> a(beaS)bS -> a(beae)bS -> abeaebe = abab
	
**Few definitions:**

What is Left associativity or Right associativity?
Dictates how the expression should be evaluated i.e whether left side should be evaluated first (Left recursive grammer = A=Aa) or right side should be evaluated first (right recursive grammer A=aA).<br/>
For ex: a+b+c can be (a+b)+c or a+(b+c). But right way is left associative as it should be evaluated ((a+b)+c). Similarly (((a+b)+c)+d)
Same for a\*b\*c, a-b-c.<br/>
<br/>
But how about 2^3^4, This expression has to be evaluated with right associativity, as 3^4 should be evaluated first and then (2^(3^4)).<br/>
NOTE: Since aS|Sa means that it can be left/right associative at same level, that makes it ambigous grammer.<br/>
<br/>
What is Precedence of operation?<br/>
For example: a+b\*c (From https://en.cppreference.com/w/cpp/language/operator_precedence, and https://introcs.cs.princeton.edu/java/11precedence/) '\*' has higher precedence than '+'). <br/>
The right evaluation tree is a+(b\*c).<br/>
<br/>
So how we write grammer that can help us work with **ASSOCIATIVITY RULES and PRECEDENCE RULES OF OPERATOR**.<br/>
**RULES OF THUMB:**<br/>
	1) Use of left/right recursion decides the associativity as it helps to determine wheather LHS is processed first or RHS is processed first.<br/>
	2) Writing production in a manner that they are further away from Root production controls the precedence. Furthest from root would be first to get evaluated.<br/>
<br/>
How to convert ambigous grammers into non ambigous:<br/>
E = E\*E/E+E/id. <br/>
*What we infer from the grammer just by looking at it:*<br/>
E+E is ambigous and '+' has no associativity preference. It can be treated as left or right associative.<br/>
E\*E is ambigous and '\*' has no associativity preference. It can be treated as left or right associative.<br/>
'\*' and '+' have same precedence as they both are at same level. <br/>
<br/>
Lets try to convert it into unambigous:<br/>
<br/>
Since '+', '-', '\*', '/' are left associative, the production needs to be of form E=E+T<br/>
Since '\*', '/' are of higher precedencer then '+', '-',  '\*', '/'  should be far from root as compared to '+', '-'.<br/>
<br/>
See below how ambigous grammer (E => E+E | E\*E | id) has been converted into unambigous grammer.<br/>
EADD	E=E+F | F<br/>
EMIN	F=F-G | G<br/>
EMUL	G=G\*H | H<br/>
EDIV	H=H/J | J<br/>
EINT	J=id<br/>
<br/>
5\*6/3+4-5<br/>
The parser tree of this grammer will always be: EADD ((EMUL ((EINT 5) (EDIV ((EINT 6) (EINT 3))))) (EMIN (EINT 4) (EINT 5)))<br/>
Above productions E+T and T\*F are left recursive as E=E+F, and F=F-G.<br/>
