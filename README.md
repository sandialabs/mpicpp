MPICPP
======

MPICPP is a simple C++ interface for the MPI C library standard.
It brings a few tried-and-true C++ principles to bear over the plain C API:

1. True RAII: no user ever has to call an explicit destructor or finalize function.
   The `mpicpp::environment` class initializes and finalizes MPI.
   The `mpicpp::comm` class takes ownership of the communicator, destroys it in
   its destructor and duplicates it in the copy constructor.
   The `mpicpp::request` class waits on the request in its destructor.
2. Exception-based error handling. All return codes from MPI C API functions
   are put through `mpicpp::handle_error` which will throw an exception
   of type `mpicpp::exception`
   in the case of anything but `MPI_SUCCESS`.
3. Template-based MPI datatype deduction for fundamental types in C++.
   Since `MPI_Datatype` is in general a runtime-constructed object as opposed to a fully
   compile-time description, we don't attempt to implement `MPI_Datatype`
   construction for arbitrary user types. Instead we simply map fundamental C++ types
   to their built-in `MPI_Datatype`.
4. Blocking is a special case of non-blocking via RAII.
   We only wrap the non-blocking communication APIs,
   and return an `mpicpp::request` from these calls.
   Since the `mpicpp::request` object calls `MPI_Wait` in its destructor, then calling
   the non-blocking MPICPP method and simply ignoring its return value is equivalent to calling
   the blocking MPI C API.

Here is an example of usage:

```cpp
#include <iostream>
#include "mpicpp.hpp"

int main(int argc, char** argv) {
  auto mpi_env = mpicpp::environment(argc, argv);
  auto comm = mpicpp::comm::world();
  double value = 1.0;
  // MPI_Datatype is deduced because double is a fundamental type
  // ignoring the return value makes this blocking
  comm.iallreduce(&value, 1, mpicpp::op::sum());
  std::cout << "sum of ones is " << value << '\n';
  // MPI_Finalize is called from environment destructor
}
```

At Sandia, MPICPP is SCR# 2757
