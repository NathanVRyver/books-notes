# Compiler Optimization Techniques

## Overview

Compiler optimizations are transformations applied to code during compilation to improve performance, reduce code size, or decrease memory usage. While many language implementations (especially for dynamic languages) focus on runtime optimizations, these compile-time techniques can significantly improve performance.

As noted by Robert Nystrom in "Crafting Interpreters":

> "Many language implementations generate relatively unoptimized code, and focus most of their performance effort on the runtime."

The following techniques represent common compiler optimizations that transform code at compile time rather than during execution.

## Key Optimization Techniques

### Constant Propagation

**Definition:** Replaces variables that have constant values with their actual values throughout the code.

**Benefits:**
- Eliminates unnecessary memory accesses
- Enables further optimizations like constant folding
- Reduces variable tracking overhead

**Example:**
```
x = 5
y = x + 2
z = y * 3
```

Optimized to:
```
x = 5
y = 7       // x replaced with 5, calculation performed
z = 21      // y replaced with 7, calculation performed
```

### Common Subexpression Elimination (CSE)

**Definition:** Identifies repeated calculations and computes them only once, storing the result in a temporary variable.

**Benefits:**
- Reduces redundant computations
- Decreases code size
- Improves execution speed

**Example:**
```
a = b * c + d
e = b * c + f
```

Optimized to:
```
temp = b * c
a = temp + d
e = temp + f
```

### Loop Invariant Code Motion

**Definition:** Moves calculations that produce the same result in every loop iteration outside the loop.

**Benefits:**
- Reduces redundant calculations
- Decreases instruction count in critical loop paths

**Example:**
```
for(i = 0; i < n; i++) {
    x = y * z;
    a[i] = x + i;
}
```

Optimized to:
```
x = y * z;  // Moved outside the loop
for(i = 0; i < n; i++) {
    a[i] = x + i;
}
```

### Global Value Numbering

**Definition:** Assigns unique identifiers to computed values to detect equivalences and eliminate redundant computations across different code paths.

**Benefits:**
- Identifies non-obvious duplicate calculations
- Works across basic blocks and control flow
- Enables more thorough redundancy elimination

**Example:**
```
if(cond) {
    a = x * y;
} else {
    b = 5;
    a = x * y;
}
c = a + z;
```

The optimizer recognizes that `x * y` computes the same value in both branches.

### Strength Reduction

**Definition:** Replaces expensive operations with equivalent but cheaper ones.

**Benefits:**
- Reduces computational complexity
- Particularly effective for loop optimizations

**Example:**
```
for(i = 0; i < n; i++) {
    x = i * 4;
}
```

Optimized to:
```
x = 0;
for(i = 0; i < n; i++) {
    x = x + 4;  // Multiplication replaced with addition
}
```

### Scalar Replacement of Aggregates

**Definition:** Breaks down arrays or structures into individual scalar variables when possible.

**Benefits:**
- Enables better register allocation
- Removes memory indirection
- Allows for better tracking of variable dependencies

**Example:**
```
struct Point { int x, y; }
Point p;
p.x = 5;
p.y = 10;
int z = p.x + p.y;
```

Optimized to:
```
int p_x = 5;
int p_y = 10;
int z = p_x + p_y;
```

### Dead Code Elimination

**Definition:** Removes code that cannot affect the program's output.

**Benefits:**
- Reduces code size
- Eliminates unnecessary computations
- Simplifies control flow

**Example:**
```
if(false) {
    doSomething();  // This will never execute
}
x = 5;
y = x + 1;
// x is never used again
```

Optimized to:
```
y = 6;  // Everything else is eliminated
```

### Loop Unrolling

**Definition:** Duplicates the loop body multiple times to reduce iteration count and loop overhead.

**Benefits:**
- Reduces branch prediction misses
- Decreases loop control overhead
- Creates more opportunities for parallelism and instruction-level optimizations

**Example:**
```
for(i = 0; i < 100; i++) {
    a[i] = i;
}
```

Optimized to:
```
for(i = 0; i < 100; i += 4) {
    a[i] = i;
    a[i+1] = i+1;
    a[i+2] = i+2;
    a[i+3] = i+3;
}
```

## Implementation Considerations

These optimizations often work together in a compiler's optimization pipeline:

1. **Dependency**: Some optimizations enable others (e.g., constant propagation enables dead code elimination)
2. **Order matters**: The sequence of optimizations affects the final result
3. **Diminishing returns**: Each additional optimization pass typically yields smaller improvements
4. **Trade-offs**: Compile time vs. run time performance must be balanced
5. **Correctness**: Optimizations must preserve the program's original semantics

## Practical Context

While these optimizations are powerful, many language implementations (especially for dynamic languages) focus on runtime optimizations because:

- Static analysis is more difficult with dynamic typing
- Just-in-time compilation can leverage runtime information
- Some optimizations are better applied at runtime with actual usage patterns

Understanding these techniques remains valuable for:

- Developing compilers or language implementations
- Writing performant code that compilers can better optimize
- Analyzing performance bottlenecks
- Understanding differences between language implementations
