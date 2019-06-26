# Recursion, Memoization and more...

Let's pass **fun**ctions to **fun**ctions, generate new **fun**ctions so that we can have **fun** with more **fun**ctions.

> (Mohammed) Ismail Ansari

## Nature of this session

This session has a little theory, some code snippets and plenty of live-coding.

The theory will, as usual, be informative and live-coding will be super-fun.

**Surprise:** We will use JavaScript for the session!

## Reasons for choosing JavaScript for the session

* JavaScript is still very popular
* I currently work with the front-end development team
* JavaScript is awesome!

## Kidding!

## Actual reasons for choosing JavaScript for the session

* It is clean
* It is a functional programming language in `C` clothing
* It is awesome!

## Let's start with some theory

So here comes some *boring* part: Time to get back to our books!

## Theory (1/8): Functional Programming with JavaScript

At this point, there is no doubt that JavaScript and functional programming go well together.

[Douglas Crockford](https://en.wikipedia.org/wiki/Douglas_Crockford), an american computer programmer and a JavaScript expert currently working for [PayPal](https://paypal.com) says that JavaScript is [LisP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) in [C](https://en.wikipedia.org/wiki/C_(programming_language)) clothing.

	const apply = function (func, value) {
		return func(value);
	};

or

    const apply = (func, value) => func(value);

### Notes

**Douglas Crockford** is not just a random senior programmer. He is a major contributor to what we call as JSON today.

This session will **mostly use the new (ES2015+) constructs in JavaScript**, but will often use older syntax for simplicity and comparison.

As we can see, the function `apply` accepts a function as the first parameter, just like we accept a value as the second parameter which can be in the form of a number, string, boolean, etc.

There is **no visible difference between the two parameters** until you see closely that the first is being invoked, and the second one is being passed to it as a parameter.

## Theory (2/8): Higher-order functions

*Higher-order functions* are nothing but functions that accept other functions as their input and/or return a function as the output, rather than a value in conventional functions. If a function has at least one of the two characteristics, it can be called a higher-order function.

For instance, consider this function:

	const isDivisibleBy = function (n) {
		return function (x) {
			return x % n === 0;
		};
	};

or

    const isDivisibleBy = n => x => x % n === 0;

It can be used as:

	const isDivisibleByTwo = isDivisibleBy(2);

	isDivisibleByTwo(3)    // Returns false
	isDivisibleByTwo(6)    // Returns true

### Notes

The function `isDivisibleBy` is a higher order function as when supplied with a numeric input, it provides a function that checks whether a supplied number is divisible by the number that was used while creating the function.

## Theory (3/8): Identity function

By definition, an *identity function* is a function that returns the same value as its argument. Even though it seems useless, it can be very useful at times.

Just to make it sound a little more useful (if you did not guess till now), we'll use it in this session.

Below are some representations of an identity function in a few different programming languages:

	// JavaScript (ES2015+)
	const identity = input => input;

	// C#
	Func<int, int> identity = x => x

	// LisP
	(defun identity (x)
		x)

	// Haskell
	identity x = x

	// Lambda Calculus
	λx.x

### Notes

In short: "you get what you give".

And it may be required when you have to provide a function instead of value but it **does not necessarily have a behavior**.

## Theory (4/8): Free variables

A *free variable* in a function is a variable that is neither local to itself, nor is it one of the arguments. It may be accessed from inside the function, but belonging to a scope elsewhere outside.

Consider the function below:

    const addToSomething = function (number) {
        return someValue + number;
    };

or

    const addToSomething = number => someValue + number;

For either of the function definitions to work, you need a variable called `someValue` in a scope that is accessible from inside the function.

    const someValue = 256;

### Notes

As you would have guessed, `someValue` is a free variable.

The code here may work correctly but for programming purists, this is a bad practice.

## Theory (5/8): Referential Transparency

An expression can be called *referentially transparent* if when replaced with its value, it does not alter the behavior of the program.

Consider the following function as an example

    const square = a => a * a;

and the below expression

	square(square(square(square(2))));

We can replace instances of `square(arg)` with the respective values and the expression gets more and more simplified at every step.

	square(square(square(4))); // Step 1
    
	square(square(16));        // Step 2
    
	square(256);               // Step 3
    
	65536                      // Step 4

Consider the below function which is **not referentially transparent** (referentially opaque, maybe?):

    let count = 2;

    incrementCount = step => {
        count = count + step;
        return count;
	};

It cannot be replaced by its value as doing so would alter the value of the resultant expression.

### Notes

There is one thing that is definitely preventing this function to be referentially transparent: **it creates a side effect**. Which means upon its execution, it is changing the value of a variable that it does not own and is not supposed to.

The other thing is (which you would have guessed by now), **it has a free-variable**.

**Conclusion:** For a function to be referentially transparent, it should not have any free-variables.

## Theory (6/8): Tennent Correspondence Principle

This principle states that if an expression is surrounded by a lambda (imagine a function in JavaScript) and call it immediately, the final expression returns the same result as the original expression.

For example, if we replace the expression

	1 + 2
    
and wrap it with a function and immediately invoke it

	(() => 1 + 2)()

the resultant expression returns the value.

## Theory (7/8): Inline definition

*Inline definition* of a function means that any function in a program can be replaced by its function definition.

The final program would return the same result.

As an example, if we have a definition for a function named `add` as shown below,

    const add = (a, b) => a + b;

then

	add(1, 2)

should be the same as

	((a, b) => a + b)(1, 2)

## Theory (8/8): Lambda Calculus

A very technical definition on [Wikipedia](https://en.wikipedia.org/wiki/Lambda_calculus) says

> Lambda calculus (also written as λ-calculus) is a formal system in mathematical logic for expressing computation based on function abstraction and application using variable binding and substitution. It is a universal model of computation that can be used to simulate any Turing machine. It was first introduced by mathematician Alonzo Church in the 1930s as part of his research of the foundations of mathematics.

The simpler (and maybe not so accurate) definition would be to work with anonymous functions.

### Notes

As mentioned in [Wikipedia](https://en.m.wikipedia.org/wiki/Anonymous_function):

> In computer programming, an anonymous function (function literal, lambda abstraction, or lambda expression) is a function definition that is not bound to an identifier. Anonymous functions are often arguments being passed to higher-order functions or used for constructing the result of a higher-order function that needs to return a function.[1] If the function is only used once, or a limited number of times, an anonymous function may be syntactically lighter than using a named function.

## Recursion

In computer programming, *recursion* is a technique to solve a problem by dividing the original problem into smaller sub-problems of the same or similar type. The most common example of recursion is a factorial function.

Typically, a function can be called recursive when it knows -

* How to break the problem into a smaller problem(s)
* When to stop breaking the problem further
* How to return the resultant value back to the calling function

Consider the below function that computes the factorial of a positive number.

	const factorial = num => {
		if (num < 2) {
			return 1;
		} else {
			return num * factorial(num - 1);
		}
	};

### Notes

For obvious reasons, the function `factorial` is **not designed to be clean**.

There is at least one sign that it is not clean: there are **multiple return statements**, something which is to be avoided while writing functions, unless really necessary.

In the above function, the factorial of a number can be computed assuming we know how to compute the factorial of a smaller number. The process of **delegation continues until the factorial of the current number is already known** and there is no more delegation needed.

## Recursion in Lambda Calculus

While working with lambda expressions, it is not possible to write recursive functions (at least directly).

The reason for this is that lambda calculus deals with anonymous functions.

Anonymous functions (which are unnamed obviously) have no way to call themselves without having a name to refer to, even for their own selves.

### Notes

This is so sad that we cannot use recursion everywhere. There definitely has to be a way right?

Besides, the session is not even halfway to its end. Brace yourselves!

## Combinators

Meanwhile, in another corner of the programming world, a *combinator* is *a function or a definition that has no free variables*.

We now know what free-variables are and looking at the above definition, we can safely say that a combinator is a pure lambda expression (which by definition means, "no accessing global variables or ones defined outside the scope of the function").

### Notes

There are various kinds of combinators.

Thankfully, we'll just look at one in the session.

## The Y-Combinator

Here comes some magic:

**A Y-Combinator is a combinator that can be used to implement recursion with lambda-expressions such that an anonymous function can call itself.**

**Sweet!**

### Notes

And, this brings us to the second part of the session: the Y-Combinator.

Here we will derive a Y-Combinator in JavaScript and verify it ourselves that it works and provides us what they say it is capable of.

## Let us derive Y-Combinator

**Time to write some code!**

It will be fun, trust me.

### Notes

**Caution:** We may see a few alien constructs in the upcoming slides.

If you see one that it too alien or something that you find confusing, please feel free to ask away.

## Our tool for this session: Light Table

In order to derive the Y-Combinator, let us use [LightTable](http://lighttable.com), a reactive work surface, a next-generation, data-centric IDE that will help us work with our code the way we want to for this session.

The feature that will be the most important for us is its ability to provide instant feedback by letting us evaluate code right at the point where we write it.

### Notes

LightTable is a relatively old IDE that has not received updates since the last time I used it about 5 years back. Having said that, the term *next-generation* is not quite true today but is just used to make it sound cool.

The project was launched in April 2012 and was [successfully funded on KickStarter](https://www.kickstarter.com/projects/ibdknox/light-table).

## Our helper function (1/2)

Meet our helper to help us with the activity:

    const printResults = function (f) {
        const output = [];
    
        for (var i = 0; i < 10; i++) {
            output.push(i.toString() + ': ' + f(i).toString());
        }
    
        return output.join('; ');
    };

### Notes

The activity of deriving the Y-Combinator will involve **multiple steps**.

We will **start with a simple function** that 'sort of' does what we need, we run a few tests, **find what is missing** and **improve it** in the next step.

After a few iterations, we will eventually have what we are looking for and [there will be cake](https://theportalwiki.com/wiki/cake).

For each step though, **we need a helper function** to test whether our current implementation does what it is supposed to.

One of the ways we can test whether a particular iteration of our function behaves as it is supposed to could be passing **a certain range of values** to the function as input and observing the output produced for those values.

For the purpose of this activity, **let us take numbers from 0 to 9**, which is a reasonable range of input values for to know that we have reached our goal to create the function we are looking for.

## Our helper function (2/2)

After a few washes and a couple of coats of wax, what we have is this:

    const printResults = f => {
        const outputs = (new Array(10))
                            .join('.')
                            .split('.')
                            .map(
                                (e, i) => 
                                    `${i.toString()}: ${f(i).toString()}`
                            );
    
        return outputs.join('; ');
    };

### Notes

The function basically does the same thing with a few differences:

- We replaced all function keywords with arrow functions
- There is no `for` loop anymore
- The string concatenation in every iteration has been replaced with template strings

## Factorial: As we know it

Most of us know what a factorial is and have seen an implementation or two of a factorial function in some programming language.

We also saw one in a previous slide.

We can write it in many forms, below is a reduced version.

    const factorialRecursive = n =>
        (n < 2)
            ? 1
            : (n * factorialRecursive(n - 1));

### Notes

Not very complex, is it?

However, do you have an idea for how it will be to implement the same function without having the feature of recursion in our language?

## Factorial without recursion: A rough try

Let's start with our attempt to create a factorial function without recursion.

As we know there will be many iterations, the first one will definitely fail. Hence, let's call it *iteration 0*.

    const recurse_v0 = (n, nextStep) => {
        if (n < 2) {
    	    return 1;
        } else {
    	    return n * nextStep(n - 1);
        }
    };
    
    const identity = n => n;

### Notes

The `recurse_v0` function, as it is not allowed (or capable of) calling itself, takes in a number for which the factorial has to be calculated, and a function that describes what is to be done as the next step.

The function `nextStep` is an attempt to simulate *breaking down of the problem into a smaller one (the recursive call)*.

As we do not have a clue for what the next step will look like exactly, **we'll use an identity function as the next step** for now.

Also, the next few slides will involve live-coding and we'll spend more time with code, than with slides so **let's keep all theory aside and switch to code!**

So, for each number, we (try to) calculate factorial as:

    n => recurse_v0(n, identity);

In order to print the (psuedo) factorial we can use our helper function:

    printResults(n => recurse_v0(n, identity));

For values 0 to 9, the function does what it is supposed to, only if the input is equal to or less than 3.

This simply proves something that we already know: **Identity function will not help** us in our cause. We need to fix that.

## Y-Combinator: Step 1

Let's start with turning what we have currently, into a function that creates a function to create factorial of any specified number but accepts a function that describes the next step.

	const recurse_v1 = nextStep =>
        n => {
			if (n < 2) {
				return 1;
			} else {
				return n * nextStep(n - 1);
			}
		};

### Notes

In `recurse_v1`, we accept a function to act as the next step and return a function that is supposed to act as a factorial function that takes in a number and returns the factorial of that number.

For simplicity, we still use identity function as the next step.

Running our helper function over it,

    printResults(recurse_v1(identity));

we find that the output for numbers between 0 to 9 is the same as the last iteration, just that the way we pass the next step, accept input and generate our factorial function has changed.

Having said that, the output for numbers greater than 3 is still not correct.

However, with the change we made, we can find the factorial of numbers greater than 3, with some additional code.

For example, if we need to create a function that can find the factorial of 4, we can add one level of nesting like:

    printResults(recurse_v1(recurse_v1(identity)));

This gets us the right answer for numbers from 0 to 4.

Now that we (sort of) have a solution, we can just find the largest number our function is supposed to work with and create that many numbers of nestings, cool!

Or otherwise, we can find a cleaner and more reliable way of doing it, without needing to create a function that only works for numbers within a range.

## Y-Combinator: Step 2

Before getting started with a cleaner approach, how about separating the logic to calculate factorial outside the parent function?

That way we can create a generic function to be able to implement recursion for calculating not only factorial but also other recursive functions.

The generic function can be extracted as

    const factorialStepper = nextStep =>
        n => {
			if (n < 2) {
				return 1;
			} else {
				return n * nextStep(n - 1);
			}
		};

and now as the logic for factorial is out of the way, our next iteration could be like

    const recurse_v2 = stepper =>
        n => stepper(identity)(n);

### Notes

The implementation is now **independent of the actual recursive function** that we need to create.

To see what the behavior of our function is now, let us test this iteration with our helper function.

    printResults(recurse_v2(factorialStepper));

If you see, the behavior is exactly the same.

In fact, adding further nestings like we could in the last iteration is not possible anymore and it breaks if we try to do so.

Let's continue the activity further.

## Y-Combinator: Step 3

In order to be able to add more nestings, we need to make a few changes to the last iteration.

    const recurse_v3 = stepper => {
        const function1 = 
            n => stepper(identity)(n);
        
        return n => stepper(function1)(n);
    };

### Notes

We need to replace the identity function with something that looks similar to the output function.

With the change, we achieve one more step, so now we can find factorial for numbers up to 4.

    printResults(recurse_v4(factorialStepper));

Though that is not all that we need, it is some progress.

## Y-Combinator: Step 4

In order to increase the range of our function, we can add another step:

    const recurse_v4 = stepper => {
        const function2 = 
            n => stepper(identity)(n);

        const function1 = 
            n => stepper(function2)(n);
        
        return n => stepper(function1)(n);
    };

### Notes

With that added step, we can now calculate factorial for numbers up to 5.

This brings us to a situation that we've seen before. We can decide for what the maximum number is that we want to be able to calculate factorial for and create a function that is capable of doing so.

However, as we saw the last time, we need something more capable.

In short, we need a function that keeps calling the next step with smaller values and stops when no more recursion is required. We already have the second part taken care of by our stepper function. What we still need is a mechanism to keep calling an inner function as many times as required.

## Y-Combinator: Step 5

Let us try to create a function that replaces all numbered functions like `function1`, `function2`, etc. and is capable of creating them dynamically.

    const recurse_v5 = stepper => {
        const makeStep = nextStep =>
            n => stepper(nextStep)(n);
        
        return n => stepper(makeStep)(n);
    };

### Notes

At first sight, this should work but it doesn't.

The reason is that **the structure of `makeStep` is different as compared to the function it was made to replace**. The earlier functions used to accept a number and this one accepts the next step, which is a function itself.

Not there yet, but we are very close.

## Y-Combinator: Step 6

In order to fix the problem that we faced in the last step, passing `makeStep` to `makeStep` seems to solve the problem.

We also can make a similar change inside the `makeStep` function by passing in `nextStep` to `nextStep`, which maintains symmetry.

    const recurse_v6 = stepper => {
        const makeStep = nextStep =>
            n => stepper(nextStep(nextStep))(n);
        
        return n => stepper(makeStep(makeStep))(n);
    };

### Notes

Passing the above function to our helper function seems to provide a great result: we have something that works for all inputs!

## Y-Combinator: Step 7

To simplify things, we can apply *Tennent Correspondence Principle* to our output function and re-write the function as:

    const recurse_v7 = stepper => {
        const makeStep = nextStep =>
            n => stepper(nextStep(nextStep))(n);
        
        return (func =>
            n => stepper(func(func))(n)
        )(makeStep);
    };

or

    const recurse_v7 = stepper => {
        const makeStep = nextStep =>
            n => stepper(nextStep(nextStep))(n);
        
        return (nextStep =>
            n => stepper(nextStep(nextStep))(n)
        )(makeStep);
    };

### Notes

Do you see a pattern there?

## Y-Combinator: Step 8

Matching certain patterns here, we can reduce the overall function to:

    const recurse_v8 = stepper => {
        const makeStep = nextStep =>
            n => stepper(nextStep(nextStep))(n);
        
        return makeStep(makeStep);
    };

### Notes

And this brings us to what we needed: **we almost have a Y-combinator**.

## Y-Combinator: We made it!

Removing all local variables and applying *inline-definitions*, we get:

    const yCombinator = stepper =>
        (
            nextStep => n => stepper(nextStep(nextStep))(n)
        )(
            nextStep => n => stepper(nextStep(nextStep))(n)
        );

> That this manages to work is truly remarkable. - [Sussman](https://en.wikipedia.org/wiki/Gerald_Jay_Sussman) and [Steele](https://en.wikipedia.org/wiki/Guy_L._Steele_Jr.)

### Notes

What we have now is a *magical* function that can be used to implement recursive functions like factorial in an environment where it is not possible for a function to refer itself.

## Factorial without recursion: Using Y-Combinator

Now that we have *Y-Combinator* with us, we can very easily achieve what we could not earlier.

For a recap, we had our stepper function specifically designed for factorial:

    const factorialStepper = nextStep =>
        n => (n < 2) ? 1 : (n * nextStep(n -1));

and when we apply our combinator to it,

    const factorial = yCombinator(factorialStepper);

we get a factorial function.

### Notes

We can check what we have by using our old helper function.

    printResults(factorial);

Recap:

- We started from a situation where it was not possible for a function to refer to itself and it was almost impossible to implement a recursive function
- We made a few attempts at implementing factorial without recursion
- In between the process, we extracted the implementation of factorial out of the function in question to make it more generic so that it can be used to implement any recursive function
- We now proved that the newly created *magical* function can be used to implement factorial

Now it is time to try our *magical* function to implement something other than factorial so that we prove that our function would work for anything thrown at it.

## Fibonacci: As we know it

Fibonacci is another popular term that we hear when talking about recursion.

One possible implementation of Fibonacci in a universe where recursion is possible would be:

    const fibonacciRecursive = function (n) {
        return (n < 2) 
            ? 1 
            : (fibonacciRecursive(n - 2) + fibonacciRecursive(n - 1));
    };

or

    const fibonacciRecursive = n =>
        (n < 2) ? 1 : (fibonacciRecursive(n - 2) + fibonacciRecursive(n - 1));
    

### Notes

This is another great example of a function that in order to calculate output for a particular input value, relies on the fact (or assumption) that the output for a smaller value is already known. It breaks down the problem into smaller sub-problems and keeps on doing that until a point is reached where there is no more breaking-down required.

## Fibonacci without recursion: Using Y-Combinator

Just to make our lives difficult again, imagine an environment where there is no recursion available.

Fortunately, we have our `yCombinator`.

Now we can use a `fibonacciStepper` and use our combinator to create a Fibonacci implementation even such *limiting* environments.

    const fibonacciStepper = nextStep =>
        n => (n < 2) ? 1 : (nextStep(n - 2) + nextStep(n - 1));

    const fibonacci = yCombinator(fibonacciStepper);

### Notes

To test what our function behaves like, we have our helper function:

    printResults(fibonacci);

Having seen that the combinator can also be used to implement Fibonacci without recursion, we can conclude that we now have an implementation of Y-Combinator that can be used with any stepper function.

## Memoization

In programming, memoization is an optimization technique to speed up calculations using results from previous calculations.

In a typical implementation, a cache is maintained to store the results of previous calculations with similar input so that we have the output available for reference.

    const memoize = func => {
        const cache = {};
        
        return input => {
            if (!cache[input]) {
                cache[input] = func(input);
            }
            
            return cache[input];
        };
    };

### Notes

As you can see, our `memoize` function is a higher-order function that takes in a function and returns a wrapper around it.

The returned function is memoized so that when provided with the same input multiple times, it does not have to call the original function once it knows the output to a particular input value. The output is directly fetched from the cache.

The *over-simplistic* implementation that we see here is not very practical. There are way better implementations that work with multiple input arguments.

For the sake of simplicity, the one that we have has been designed for just a single input parameter.

There are a few programming languages that have such a function built into the language itself.

For others, we can either use a library or write one yourself.

## 'Double' function: As we know it (1/2)

Consider the following function that doubles any input supplied to it:

    const double = a => a + a;

Just for the purpose to be informed every time the function is called, let's put a log statement inside it.

    const doubleLogged = a => {
        console.log(`Calculating the double of ${a}`);
        
        return a + a;
    };

## 'Double' function: As we know it (2/2)

Consider a hypothetical situation when we need to call the function `doubleLogged` multiple times, probably with repeating input values.

    [1, 2, 3, 1, 5, 1].forEach(n => console.log(`Double of ${n} is ${doubleLogged(n)}));

### Notes

As you would expect, the function `double` gets called `6` times, out of which, it is `3` three times with input being `1`.

Now, assume this function to be **very resource intensive**. How about saving time and other resources spent on every function call and returning the result for input `1` from a saved location for successive function calls?

That can be achieved using memoization.

## Memoizing 'Double' function

Using our `memoize` function over `doubleLogged`,

    const doubleLoggedMemoized = memoize(doubleLogged);

we get a memoized function that doubles its inputs efficiently.

    [1, 2, 3, 1, 5, 1].forEach(n => console.log(`Double of ${n} is ${doubleLoggedMemoized(n)}));

So, how many times is the `doubleLogged` function called this time?

### Notes

Definitely, the function is called only `4` times instead of `6` as for successive calls, our memoized function knew the output for input `1` and simply returned the output from the cache.

Memoizing a simple function like `double` was pretty easy. How about memoizing a recursive function like `factorial`?

## Memoizing  the 'factorial' function (1/2)

Getting back to our old function `factorialRecursive` with added log,

    const factorialLogged = n => {
        console.log(`Calculating factorial of ${n}`);

        return (n < 2)
            ? 1
            : (n * factorialLogged(n - 1));
    };

and in a situation when it is called with repeating values,

    [2, 6, 2].forEach(n => console.log(`Factorial of ${n} is ${factorialLogged(n)}`));

How many times do you think the function is getting called?

### Notes

The function is called not `3`, but `10` times.

That is because our function is a recursive one and it calls itself for smaller inputs.

## Memoizing the 'factorial' function (2/2)

Let us apply our `memoize` function on `factorialLogged`.

    const factorialLoggedMemoized = memoize(factorialLogged);

And try it out with the same repeating values:

    [2, 6, 2].forEach(n => console.log(`Factorial of ${n} is ${factorialLoggedMemoized(n)}`));

How many times do you think now?

### Notes

It is called `8` times.

As you can see, it was not called with input `2` the second time.

However, while calculating factorial for value `6`, it had to calculate factorials for values `5`, `4`, `3`, `2` and `1`.

Practically it should not have been called for values `2` and `1` once more, having already calculated the output for those two values before. However, that is not the case.

Can you guess why? The answer is simple, **our `memoize` function could only memoize the outer calls to `factorialLogger`**. It had no control once the outer function was called.

If the outer function was called for the same value again, it found a previously cached output. However, for any calls to itself from inside, it was not instructed to use the cache and it simply kept making more calls to itself.

## Y-Combinator saves the day! (1/5)

The problem of not being able to *properly* memoize recursive functions can be solved using our previously derived Y-Combinator.

To do that, we need a few things in place, which we'll arrange for one at a time.

## Y-Combinator saves the day! (2/5)

We need a stepper, which we'll add logs to, just to know for when (and how many times) it's being called.

    const factorialStepperLogged = nextStep =>
        n => {
            console.log(`About to run step for ${n}`);

			return (n < 2)
				? 1
				: n * nextStep(n - 1);
		};

### Notes

As usual, we need to pollute the stepper by adding logs.

## Y-Combinator saves the day! (3/5)

We need to create a `memoize` generator.

    const generateMemoizer = () => {
        const cache = {};
    
        return func =>
            input => {
                if (!cache[input]) {
                    cache[input] = func(input);
                }
            
                return cache[input];
            };
        };

### Notes

This generator just generates a memoizer that memoizes a function rather than doing it itself.

This is because we do not want the cache to be re-initialized every time the wrapper is being called.

## Y-Combinator saves the day! (4/5)

We need to modify the Y-Combinator function to accept a memoizer function.

    var yCombinatorMemoizable = (stepper, wrapper) =>
        (
            f => f(f)
        )(
            nextStep => n => wrapper(stepper(nextStep(nextStep)))(n)
        );

### Notes

As you can see, the combinator now takes an additional function to wrap actual calls to our stepper so that it is only called when the output for a particular input is not already known.

The wrapper (which is a memoizer function) returns the output from the cache for any input that has been received earlier.

## Y-Combinator saves the day! (5/5)

Finally, we create a factorial function with our modified combinator that is memoized in the right way.

    const factorialM = yCombinatorMemo(factorialStepperLogged, generateMemoizer());

### Notes

Now that we have every all the three pieces, we can combine them to create a memoized factorial function which performs recursion in an environment that does not allow a function to refer to itself.

We provide our modified combinator function with a factorial stepper and an instantly generated memoizer.

## References

* [Understanding, at last, the Y Combinator - a programmer-friendly perspective (hisham.hm)](http://hisham.hm/2011/04/04/understanding-at-last-the-y-combinator-a-programmer-friendly-perspective)
* Many more that I do not remember
