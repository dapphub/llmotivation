## Why linear logic?

* Linear Logic is a **Logic**, which is one of the oldest and best studied mathematical systems.
* proof terms Curry-Howard correspond to the pi-calculus - a process calculus with atomic notion of concurrency, making it an ideal candidate for blockchain application.


## Efficiency
### Motivation

We model Linear Logic inside of the evm. The approach is that every execution of logical programs is done via proof search.
This has the advantage that correctness can be guaranteed, however, as proof verification has to be done on-chain is very expensive operation. Here we want to explore how proof verification can be optimized.

The current idea is to show that proof verification can be partially replaced with the execution of a EVM code snippet.


## How to model the EVM

The EVM has a few distinct components, such as:

*  code
*  PC
*  stack
*  memory

> In this I'd like to study how we can model the EVM in Linear Logc. For this example we won't bother with specifics of the evm and explore an arbitrary example for a simple stack machine.
> So assume the code:

    PUSH
    0x12
    INC

It should push the value `0x12` onto the stack, increment it and return the result. (`0x13`)

We can model the Stack Machine with the following predicates:

* code(X,Y) - OpCode **Y** is on position **X** in the code.
* pc(X) - Program Counter is at position **X**
* stack(X,Y) - word **Y** is on position **X** on the stack
* storage(X, Y) - word **Y** is at position **X**
* memory(X, Y) - word **Y** is at position **X**

Additionally we create a few extra predicates which will help during the execution:
* stackLength(X) - **X** is the current stack length

Additionally we need to show the following:
* The stack(and code) has to be continuous: \\(\forall X. stack(sX, \cdot) \rightarrow stack(X, \cdot)\\)

> TODO - how to do this?


### programm execution

First lets model our program with the given predicates:

    code(0, PUSH)
    code(1, 0x12)
    code(2, INC)

The correct result should be found at stack height 0. We can retrieve it with:

    stack(0, X)
    pc(3)
    -o
    result(X)

Also initially we have the following predicates given:

    pc(0)
    stackLength(0)

now lets model the **PUSH** opCode:

  $$ pc(X)\otimes code(X, PUSH) \otimes code(sX, V)\otimes stackLength(L)$$
  $$\multimap$$
  $$stack(L, V)\otimes pc(ssX)\otimes code(X, PUSH)\otimes code(sX, V)\otimes stackLength(sL)$$

This pushes the value V to the stack, increments the stack length and preserves the code.

now lets model the **INC** opCode:

Assume we have the following axiomatic rule for inc given:

`inc(X, sX)`

Thus:
$$ inc(X, Y) \Leftrightarrow X + 1 = Y$$


So the following transition rule models our INC opCode:

  $$pc(X) \otimes code(X, INC) \otimes stack(L, V) \otimes stackLength(sL) \otimes inc(V, W)$$
  $$\multimap$$
  $$pc(sX) \otimes code(X, INC) \otimes stackLength(sL) \otimes stack(L, W)$$

#### Expansion

Now we need to proof that there is a proof expansion from `inc(X,Y)` to a formula which only uses the evm primitives:



## Open questions

* How to model type encoding( unary number in this example) in such a way, that it is equivalent to evm data.
* How to model errors, such as stack overflow/underflow
* How to model compiler decisions?
