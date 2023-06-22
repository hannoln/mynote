**what is this course about**
- a course about managing complexity
	- mastering abstraction
	- programing paradigms
	- not all about lower details 
- a introduction to python
	- full understading of language fundamentals
	- learning through implementation 
	- how computers interpret programming languges
# lecture 1

## expressions
- an expression describe a computatuon and evaluates to a value
- all expression can use function call notation 
![[Pasted image 20230430202424.png]]
## functions ,objects and interpreters

![[Pasted image 20230430203221.png]]
after the word draw is a symbol for reverse. **just look at the result**

code in lecture 1

```python
# Numeric expressions
2020
2000 + 20
-1 + 2 + 3 + 4 * ((5 // 6) + 7 * 8 * 9)

# Call expressions
max(3, 4.5)
pow(100, 2)
pow(2, 100)
max(1, -2, 3, -4)

# Importing and arithmetic with call expressions
from operator import add, mul
add(1, 2)
mul(4, 6)
mul(add(4, mul(4, 6)), add(3, 5))
add(4, mul(9, mul(add(4, mul(4, 6)), add(3, 5))))

# Objects
# Note: Download from http://composingprograms.com/shakespeare.txt
shakes = open('shakespeare.txt')
text = shakes.read().split()
len(text)
text[:25]
text.count('the')
text.count('thou')
text.count('you')
text.count('forsooth')
text.count(',')

# Sets
words = set(text)
len(words)
max(words)
max(words, key=len)

# Reversals
'draw'[::-1]
{w for w in words if w == w[::-1] and len(w)>4}
{w for w in words if w[::-1] in words and len(w) == 4}
{w for w in words if w[::-1] in words and len(w) > 6}

```

# lecture 2

## names
  how to clear screen in cmd
  ```python
  import os 
  def clear():os.system('cls') 
  clear()
```
## type of expressions
- primative expressions
	![[Pasted image 20230430205709.png]]
- call expressions 
	![[Pasted image 20230430205721.png]]

![[Pasted image 20230430210059.png]]
if there's no accident happen during my caculation ,the answer must be 1.
let's try 
![[Pasted image 20230430210307.png]]
ah…… accident happen . l try again 
![[Pasted image 20230430211517.png]]
**intersing...**
in my cmd I change the meaning of 'f' before(and I didn't restart the python program) ,maybe that is the reason that my out put is 2 rather than 3 
the reason why my output is 2 is just because i've got a typo.....
## environment diagrams
——a kind of graph to help you understand the implement of a program

## defining functions
- assignment is a simple means of abstractions:binds**names to values**
- function definition is a more powerful means of abstraction: **binds name to expression**
![[Pasted image 20230430212007.png]]
# lecture 3
## print and none

- the special value None represents nothing in Python
- a function that does not explicitly return a value will return none

**type of functions**
- pure functions :just return values
	 ![[Pasted image 20230501085300.png]]
- non-pure functions:have side effects
	![[Pasted image 20230501085347.png]]
![[Pasted image 20230501085608.png]]


## statement
![[Pasted image 20230501090701.png]]
# iteration（repeat）
- the fibonacci sequence
![[Pasted image 20230501092020.png]]

## return statements
- a return statement completes the evaluation of a call expression and provides its value

# lecture 4 higer-order functions 

