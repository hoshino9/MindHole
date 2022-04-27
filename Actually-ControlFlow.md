# Actually ControlFlow

In a language which support pattern matching and recursion, we can develop a powerful control flow "syntax":

## Flow

There is only one control flow syntax called `flow`, it can achieve both if and while.

```
flow (cond) { body }
```

`flow` is consists by two parts: `condition` and `body`

### Flow Condition

Flow Condition (or condition for short) determines whether the body is executed.

### Flow Body

Flow Body (or body for short) is executed when condition is true.

Body should have a type that matches `() -> FlowResult<R>`.

### Flow Result

Flow Result is the type which flow yields, it has three values:

- FlowAbort:    indicates that the flow doesn't execute completely (normally, it occurred when condition is false)
- FlowContine:  indicates that the flow should execute one more times.
- FlowYield(R): indicates that the flow should stop, and yields a result with type R.

### Flow Implmenetation (fake code)

Function in these code have side effect.

```haskell
data FlowResult r = FlowCont | FlowYield r

type FlowCond = () -> Bool
type FlowBody r = () -> FlowResult r

flow :: FlowCond -> FlowBody r -> FlowResult r
flow c b = case c () of
             True -> case b () of
                       FlowCont -> flow c b
                       other -> other
             False -> FlowAbort
```

And we also have these:

```haskell
yield :: r -> FlowResult r
yield = FlowYield

break :: FlowResult ()
break = yield ()

continue :: FlowResult r
continue = FlowCont
```

### If-Else Structure

We can build **If-Else Structure** by using `flow` "syntax" (which is weird in functional language...):

```haskell
type IfElseBody r = () -> r

wrapper :: IfElseBody r -> FlowBody r
wrapper f = \() -> yield (f ())

ifelse :: FlowCond -> IfElseBody r -> IfElseBody r -> r
ifelse c bt bf = case (flow c (wrapper bt)) of
                   -- 'true' case
                   FlowYield r -> r
                   -- 'false' case
                   FlowAbort -> case bf () of
                                  FlowYield r -> r
                                  _ -> undefined  -- (wrapper bf) never produce FlowCont and FlowAbort
                   _ -> undefined  -- (wrapper bt) never produce FlowCont
```

### While Loop Structure

... which is equivalent to flow, but this `while` is more powerful! You can find out that why the loop stop:

- FlowAbort: condition is false or body produce a FlowAbort
- FlowYield: condition is true but the body produce a value (a powerful point, while produce a value without syntax support)
- FlowCont: which is impossible

> Also, we can add a new value for FlowResult to find out what exactly causes the loop to stop.

```haskell
while :: FlowCond -> FlowBody r -> FlowResult r
while = flow
```
