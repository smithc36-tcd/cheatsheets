# OpenMP

OpenMP (Open Multi-Processing) is an API for parallel programming in C, C++, 
and Fortran, which allows you to parallelize your code using compiler 
directives, library routines, and environment variables. OpenMP is designed 
for shared-memory parallelism and is particularly useful for speeding up 
CPU-bound tasks.

## Compiler Support

To use OpenMP, make sure your compiler supports it. For GCC or Clang, 
compile your code with the `-fopenmp` flag:

```bash 
gcc -fopenmp my_openmp_program.c -o my_openmp_program
```

## Basic Syntax

OpenMP uses `#pragma` directives to specify parallel regions in your code. The 
most basic directive is `#pragma omp parallel`:

```c
#include <omp.h>
#include <stdio.h>

int main() {
    #pragma omp parallel
    {
        printf("Hello, world! I'm thread %d of %d.\n", 
                omp_get_thread_num(), omp_get_num_threads());
    }
    return 0;
}

```

## Setting the number of threads 

You can set the number of threads used in parallel regions using one of the following methods:

- Call `omp_set_num_threads(int num_threads)` before the parallel region
- Add the `num_threads` clause to the `#pragma omp parallel directive`
- Set the `OMP_NUM_THREADS` environment variable

```c
#include <omp.h>
#include <stdio.h>

int main() {
    omp_set_num_threads(4);

    #pragma omp parallel num_threads(4)
    {
        printf("Hello, world! I'm thread %d of %d.\n", omp_get_thread_num(), omp_get_num_threads());
    }

    return 0;
}
```

## Parallel Loops

OpenMP simplifies parallelizing loops with the
`#pragma omp parallel for directive`:

```c
#include <omp.h>
#include <stdio.h>

int main() {
    int n = 10;
    #pragma omp parallel for
    for (int i = 0; i < n; i++) {
        printf("Thread %d is processing iteration %d\n", omp_get_thread_num(), i);
    }
    return 0;
}
```

## Reducing variables

When using a reduction operation like summation or finding the maximum, use 
the reduction clause:

```c
#include <omp.h>
#include <stdio.h>

int main() {
    int sum = 0;
    int n = 10;

    #pragma omp parallel for reduction(+:sum)
    for (int i = 0; i < n; i++) {
        sum += i;
    }

    printf("Sum = %d\n", sum);
    return 0;
}
```

## Synchronisation 

OpenMP provides synchronization constructs like `barrier`, `critical`, and `atomic`.

### Barrier 

Use `#pragma omp barrier` to ensure that all threads reach the barrier before continuing:

```c
#pragma omp parallel
{
    // Code executed by each thread before the barrier
    #pragma omp barrier
    // Code executed by each thread after the barrier
}
```

### Critical 

Use `#pragma omp critical` to ensure that only one thread at a time can execute the enclosed block of code:

```c
#pragma omp parallel
{
    // Code executed by each thread concurrently
    #pragma omp critical
    {
        // Code executed by one thread at a time
    }
}
```

### Atomic 

Use `#pragma omp atomic` to ensure that a specific memory update is performed atomically:

``` c
int counter = 0;

#pragma omp parallel
{
    // Code executed by each thread concurrently
    #pragma omp atomic
    counter++;
}
```
## Sections 

You can divide a parallel region into sections that will be executed by 
different threads using the `#pragma omp sections` directive:

```c
#pragma omp parallel
{
    #pragma omp sections
    {
        #pragma omp section
        {
            // Code executed by one thread
        }

        #pragma omp section
        {
            // Code executed by another thread
        }
    }
}
```
