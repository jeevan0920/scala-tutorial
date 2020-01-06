

## Higher Order Functions 

Functional languages treat functions as first-class *values*.

This means, a function can be **passed as a parameter** and returned as result.

**Def** : Functions that take other functions as parameters or that return functions as results are called *higher order functions*.

##### Examples

Take sum of integers between a and b

```scala
def sumInts(a :Int,b : Int) : Int =
	if (a > b) 0 else a + sumInts(a+1,b)
```

Take sum of all cubes between a and b

```scala
def cube(x: Int) : Int = x*x*x
def sumCubes(a: Int, b: Int): Int = 
	if(a > b) 0 else cube(a) + sumCubes(a+1,b)
```

Take sum of the factorials of all the integers between a and b 

```scala
def fact(n: Int): Int =
	if(n == 0 || n == 1 ) 1 else n * fact(n-1)
def sumFactorials(a: Int,b: Int): Int = 
	if(a > b) 0 else fact(a) + sumFactorials(a+1,b)
```

**Summing with Higher-Order Functions** - Idea is to factor out the common patterns

```scala
//Higher order function taking function as an argument
def sum(f: Int => Int, a: Int, b: Int): Int = 
	if(a > b) 0
	else f(a) + sum(f,a+1,b)
//sum of integers
def sumInts(a: Int, b: Int): Int = sum(id, a, b)
//sum of cubes
def sumCubes(a: Int, b: Int): Int = sum(cube, a, b)
//sum of factorials
def sumFactorials(a: Int, b: Int): Int = sum(fact, a, b)
//where
def id(x :Int): Int = x
def cube(x :Int): Int = x*x*x
def fact(x :Int): Int = if(x == 0) 1 else fact(x - 1)

```

**Anonymous** Functions helps us write functions in shorter way

```scala
def sumInts(a :Int, b: Int): Int = sum(x => x, a, b)
def sumCubes(a :Int, b: Int): Int = sum(x => x*x*x, a, b)
```

**Tail** recursive sum (Tail recursion to help us to save stack space by allowing us to not  to back prev stack frame again during evaluation )

```scala
def tailSum(f: Int => Int, a: Int, b: Int) : Int
	def loop(a: Int, acc: Int) : Int = 
		if( a > b ) acc
		else loop(a+1,f(a)+acc)
	loop(a,0)
```



---

## Currying

If you see in the below examples, functions sumInts() and sumCubes() are taking two arguments and passing them to sum(), there is a some redundancy there.

```scala
def sumInts(a :Int, b: Int): Int = sum(x => x, a, b)
def sumCubes(a :Int, b: Int): Int = sum(x => x*x*x, a, b)
```

##### Functions returning functions

```scala
def sum(f: Int => Int): (a: Int, b: Int) => Int = {
	def sumF(a: Int, b: Int): Int = 
		if(a > b) 0
		else f(a) + sumF(a+1,b)
	sumF
}

//we can now write 
def sumInts = sum(x => x)
def sumCubes = sum(x => x*x*x)
```

##### Consecutive step-wise applications

Below two functions are same

```scala
sum (cube) (1, 10) 
```

```scala
sumCubes(1,10)
```

##### Multiple Parameter Lists

we can have functions with multiple parameter list like below, they give more flexibility while using them.

```scala
//sum function with multiple parameter list
def sum(f: Int, Int) (a: Int, b: Int): Int = 
	if (a > b) 0 else f(a) + sum(f)(a+1,b)
sum(cube)(1,10)
sum(fact)(1,10)
```

##### Expansion of Multiple Parameter Lists

function with multiple parameter lists

​										def f(args_1)...(args_n) = E

where n>1, is equivalent to 

​							def f(args_1)...(arg_n-1) = { def g(args_n) = E;g }

where g is a fresh identifier. Or for short :

​							def f(args_1)...(args_n-1) = ( args_n => E ) 

​							where ( args_n => E ) is the anonymous function here

