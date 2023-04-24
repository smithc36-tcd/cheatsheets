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

## OpenMP Library Funtions 

OpenMP provides library functions for controlling and querying various aspects of parallel execution

- `omp_set_num_threads(int)`: Set the number of threads for subsequent parallel regions
- `omp_get_num_threads()`: Get the number of threads in the current parallel region
- `omp_get_thread_num()`: Get the ID of the calling thread
- `omp_get_num_procs()`: Get the number of available processors
- `omp_get_wtime()`: Get the current wall-clock time

You can use these functions in your code to control and monitor the parallel execution:

```c
#include <omp.h>
#include <stdio.h>

int main() {
    int num_threads = omp_get_num_procs(); // Get the number of available processors
    omp_set_num_threads(num_threads); // Set the number of threads

    double start_time = omp_get_wtime(); // Get the start time

    #pragma omp parallel
    {
        // Parallel code
    }

    double end_time = omp_get_wtime(); // Get the end time
    printf("Execution time: %f seconds\n", end_time - start_time);

    return 0;
}
```

## OpenMP Environment variables

You can control various aspects of OpenMP execution through environment variables:

- `OMP_NUM_THREADS`: Set the default number of threads for parallel regions
- `OMP_NESTED`: Enable or disable nested parallelism
- `OMP_SCHEDULE`: Set the default loop scheduling policy and chunk size
- `OMP_MAX_ACTIVE_LEVELS`: Set the maximum number of nested active parallel regions

```bash 
export OMP_NUM_THREADS=4
./my_openmp_program
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


## Nested parallelism

OpenMP supports nested parallelism, allowing you to create parallel regions 
inside other parallel regions. To enable nested parallelism, use the 
`omp_set_nested(int)` function or set the `OMP_NESTED` environment variable:

```c
omp_set_nested(1); // Enable nested parallelism

#pragma omp parallel num_threads(2)
{
    #pragma omp parallel num_threads(2)
    {
        // Code executed by a nested parallel region
    }
}
```



## OpenMP Scheduling Policies

You can control the loop iteration scheduling policy with the schedule clause 
in the `#pragma omp parallel for` directive. OpenMP provides several scheduling 
policies:

- `static`: Divide the loop iterations into equal-sized chunks, and assign each chunk to a thread. You can specify the chunk size or let OpenMP determine it automatically.

- `dynamic`: Dynamically assign loop iterations to threads as they become available. You can specify the chunk size or let OpenMP determine it automatically.

- `guided`: Similar to dynamic scheduling, but the chunk size decreases exponentially over time. You can specify the minimum chunk size or let OpenMP determine it automatically.

- `auto`: Let the OpenMP runtime choose the scheduling policy and chunk size.

```c
#include <omp.h>
#include <stdio.h>

int main() {
    int n = 100;

    #pragma omp parallel for schedule(dynamic, 10)
    for (int i = 0; i < n; i++) {
        // Code executed by each thread concurrently
    }

    return 0;
}
```

## Task parallelism

You can also use OpenMP's task construct for more flexible parallelism. Tasks 
can be executed concurrently and in any order, allowing for parallelism in 
irregular algorithms or when it is not possible to determine the amount of 
work in advance.

```c
#pragma omp parallel
{
    #pragma omp single
    {
        #pragma omp task
        {
            // Code executed by one task
        }

        #pragma omp task
        {
            // Code executed by another task
        }
    }
}
```

### `#pragma omp taskwait`

This directive is used to introduce a synchronization point in the code, where 
the current task waits for the completion of all its child tasks before 
proceeding. This can be useful for ensuring that the necessary data is 
available before continuing execution.

```c 
#pragma omp parallel
{
    #pragma omp single
    {
        #pragma omp task
        {
            // Task 1
        }

        #pragma omp task
        {
            // Task 2
        }

        #pragma omp taskwait
        // Task 1 and Task 2 must complete before this point
    }
}
```

### `#pragma omp taskyield`

This directive is used to provide a hint to the OpenMP runtime that the current 
task can be suspended to allow other tasks to execute. This can help improve 
load balancing and resource utilization, especially in situations where a task 
is waiting for resources or data from other tasks.

```c
#pragma omp parallel
{
    #pragma omp single
    {
        #pragma omp task
        {
            // Task 1
            #pragma omp taskyield
            // Task 1 can be suspended to allow other tasks to execute
        }

        #pragma omp task
        {
            // Task 2
        }
    }
}
```

### `#pragma omp taskgroup`

This directive is used to define a task group, which is a set of tasks that 
share a common synchronization point. When a task group is encountered, it 
implicitly waits for the completion of all tasks within the group before 
proceeding. This can be useful for organizing tasks into logical groups and 
ensuring that all tasks within a group complete before continuing execution.

```c
#pragma omp parallel
{
    #pragma omp single
    {
        #pragma omp taskgroup
        {
            #pragma omp task
            {
                // Task 1
            }

            #pragma omp task
            {
                // Task 2
            }
        }
        // Task 1 and Task 2 must complete before this point
    }
}
```

