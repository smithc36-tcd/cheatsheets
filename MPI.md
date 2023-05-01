# MPI (Message Passing Interface)

It is a standardised programming model for distributed-memory systems. It 
allows processes to communicated and exchange data. 

Hello world example: 

```c
#include <stdio.h>
#include <mpi.h>

int main(int argc, char* argv[]) {
    int rank, size;

    // Initialize MPI
    MPI_Init(&argc, &argv);

    // Get the rank and size
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    MPI_Comm_size(MPI_COMM_WORLD, &size);

    // Print a message from each process
    printf("Hello, World! I am process %d of %d.\n", rank, size);

    // Finalize MPI
    MPI_Finalize();

    return 0;
}
```

Compile with: 

```bash 
mpicc helloworld.c -o helloworld
```

Run with: 

```bash
mpirun -n 4 helloworld
```

# Important methods:

1. `MPI_Init`: Initializes the MPI environment and should be called before any other MPI functions.
2. `MPI_Finalize`: Cleans up and terminates the MPI environment, and should be called before exiting the program.
3. `MPI_Comm_size`: Returns the total number of processes in a communicator (usually MPI_COMM_WORLD, which includes all processes).
4. `MPI_Comm_rank`: Returns the rank (ID) of the calling process within a communicator.
5. `MPI_Send`: Sends a message from one process to another.
6. `MPI_Recv`: Receives a message sent by another process.
7. `MPI_Bcast`: Broadcasts a message from one process to all other processes in a communicator.
8. `MPI_Reduce`: Gathers and reduces data from all processes to a single process using a specified operation (e.g., sum, max, min).
9. `MPI_Barrier`: Synchronizes all processes in a communicator, ensuring that all processes have reached the same point in execution.

