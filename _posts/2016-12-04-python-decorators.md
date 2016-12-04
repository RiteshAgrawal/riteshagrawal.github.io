---
layout: post
title: "Getting Started: Python Decorators"
description: "A decorator is a function that wraps another function to enhance its functionality with actually modifying source code of the function."
tags: [python]
comments: true
---


This post will help you in getting started with python decorators. I do assume some familiarity with python language. I use python2.7 for all code snippets. 

Before directly jumping into decorators, let’s take a step back and talk about python functions.  This will help in understanding the concept in a better way. 

### Functions

A function in python can be defined as follows:

~~~ python
def introduce(name):
    return 'My name is %s' % name

~~~

This function takes `name` as function argument and returns a string. 
Below is the explanation of above function `introduce`:

* *def* is the keyword used to define a function.
* *introduce* is the name of the function.
* Variable inside parenthesis (*name*) is the required argument for the function.
* Next line is the body or definition of the function.

### Function Properties

In python, functions are treated as first-class objects. This means that python treats functions as values. We can assign function to a variable, pass as an
argument to a function or return as a value from a function. 

~~~ python
def print_hello_world():
    print 'Hello World!'
~~~

We have defined a function `print_hello_world'. Now we can assign it to a
variable.

~~~ python
>>> world = print_hello_world
~~~
(Here >\>> is denoting the python  interactive shell)

Now we can call `world` just like the function `print_hello_world`.

~~~
>>>world()
Hello World!
~~~

We can also pass a function to another function as an argument.

~~~ python
def execute(func):
    print 'Before execution'
    func()
    print 'After execution'
~~~

So now when we pass `print_hello_world` function to `execute` function, the output will be as follows:

~~~ python
>>>execute(print_hello_world)
Before execution
Hello World!
After execution
~~~

Python also supports nesting of functions. It means we can define another function in body or definition of some other function. 
Example: 

~~~python
def foo(x):
    def bar(y):
        return x+y
    return bar
~~~

In the above example, we have used two concepts described before. 
1. Returning a function (bar) as a return value of the function `foo`.
2. Nesting function `bar` in the definition of the function `foo`.

Let's see this code in action.

~~~ python
>>>v1 = foo(2)
~~~

Here `v1` stores the return value of the function `foo` which is another function `bar`. Now what will happen if we call `v1` with some parameter?

~~~ python
>>>print v1(5)
7
~~~

Thinking something? It's fine about the parameter `y` but how function `bar`
got the value of `x` as the function `foo` has already executed and returned
before the call to the function `v1`. Isn't it?

When a function is handled as data (in our case, return as a value from another
        function), it implicitly carries information required to execute the function. This is called **closures** in python. We can check the closure of the function using `__closure__` attribute of the function. This will return a tuple containing all the closures of the function. If you want to see any content of the closure, you can do something like `v1.__closure__[0].cell_contents`. 

~~~ python
>>>v1.__closure__
(<cell at 0x7f4368e6c590: int object at 0xa41140>,)
>>>v1.__closure__[0].cell_contents
2
~~~

So now, we understood both function properties. Let's see how can we use these properties in real scenarios.

### Going Ahead

Suppose we want to perform some generic functionality before or/and after function execution. It can be like printing execution time of the function. 

One way to do this is by writing whatever we want to do before and after execution as initial and final statements respectively. 
Example:

~~~ python
def print_hello_world():
    print 'Before Execution'
    print 'Hello World!'
    print 'After Execution'
~~~

Is this a good way. I leave it on you. What will happen if I have several functions and need to perform the same task for all other functions too?

Another way could be to write a function that will take any other function as an argument and return the function along with performing the task before and after function execution. 
Example:

~~~ python
def print_hello_world():
    print 'Hello World'

def dec(func):
    def nest_func(*args, **kwargs):
        print 'Before Execution'
        r =  func(*args, **kwargs)
        print 'After Execution'
        return r
    return nest_func
~~~

Function `print_hello_world` just prints 'Hello World'. Function `dec` takes a
function as an argument, creates another function `nest_func` in its
definition. `nest_func` prints some statements before and after execution of
function passed as an argument in function `dec`.

Let's pass the function `print_hello_world` to `dec`.

~~~ python
>>> new_print_hello_world = dec(print_hello_world)
~~~

`new_print_function` is another function returned by the function `dec`. What
will be the output on calling `new_print_hello_world` function? Let's check it.

~~~ python
>>> new_print_hello_world()
Before Execution
Hello World
After Execution
~~~

What if we assign the new function returned by the `dec` function to
`print_hello_world` function again?

~~~ python
>>> print_hello_world = dec(print_hello_world)
~~~

Let's call `print_hello_world` function now. 

~~~ python
>>> print_hello_world()
Before Execution
Hello World!
After Execution
~~~

So we basically we have changed the functionality of the function
`print_hello_world` without changing the source code of the function itself. 

So What next? If are clear till now, then you have already learnt about
decorators. Let me explain explicitly.

### Decorators

A decorator is a function which provides us the freedom to enhance or change
the functionality of any function dynamically, without making changes in the
code of the function. 

In our case, function `dec` provides us this functionality (As it changes the
functionality of the function `print_hello_world`). So `dec` is called as
*decorator*. 
Instead of passing `print_hello_world` explicitly to function `dec`, we can use
its shorthand syntax:

~~~ python
@dec
def print_hello_world():
    print 'Hello World'
~~~

Now we are clear about decorators. Right? There may be a question wondering in
your mind. Why do I need to return a function from the `dec` function? Just
call the function in `dec` itself in which we can print statements along with
executing the function passed as argument. 
Example:

~~~ python
def dec1(func):
    print 'Before Execution'
    func()
    print 'After Execution'
~~~

I have few questions for you in answer to this question. Suppose I agree with
you and decides to do it as suggested.

~~~ python
>>>print_hello_world = dec1(print_hello_world)
~~~

1. What value does `print_hello_world` store right now? Can you call it now?
(It is storing None which is return value of the function `dec1`. So you can't
 call `print_hello_world` now)
2. What if we want to enhance function having some arguments. One suggestion
could be like

~~~ python
def dec2(func, arg1, arg2):
    print 'Before Execution'
    func(arg1, arg2)
    print 'After Execution'
~~~

But here the problem is how will we get the value of arg1 and arg2 at time of
passing any function to `dec2`. 

~~~ python
>>> print_hello_world = dec2(print_hello_world, arg1, arg2)
~~~

Here we will not be able to get the value of arg1 and arg2. 

I hope above two points have cleared your doubts about why decorators are
required to return a function.


### Decorator Examples

* It can be used to compute execution time of any function.

~~~ python
def decorator(func):
    def nest_func(*args, **kwargs):
        start = time.time()
        response = func(*args, **kwargs)
        end = time.time()
        print end-start
        return response
    return nest_func
~~~

* In web applications, it can be used to check if user is logged in or not.

~~~ python
def login_required(func):
    def nest_function(request, *args, **kwargs):
        if request.user.is_authenticated():
            return func(request, *args, **kwargs)
        else
            return redirect('/login')
    return nest_function
~~~


I hope this article provided you a basic idea of python decorators and some of
its use cases. If you have any doubt or have any feedback, you can reach out to
me at udr [dot] ritesh [at] gmail [dot] com.


