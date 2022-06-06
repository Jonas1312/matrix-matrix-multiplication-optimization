# Matrix Matrix Multiplication Optimization

```c++
#include <cassert>
#include <chrono>
#include <iostream>
#include <vector>

#define N 1024

using matrix = std::vector<std::vector<double>>;

void reset_matrix(matrix &C) {
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j < N; ++j) {
            C[i][j] = 0.0;
        }
    }
}

bool are_equal(const matrix &X, const matrix &Y) {
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j < N; ++j) {
            if (X[i][j] != Y[i][j]) {
                return false;
            }
        }
    }
    return true;
}

auto multiply_ijk(const matrix &A, const matrix &B, matrix &C) {
    auto start = std::chrono::high_resolution_clock::now();

    for (int i = 0; i < N; ++i) {
        for (int j = 0; j < N; ++j) {
            for (int k = 0; k < N; k++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }

    auto end = std::chrono::high_resolution_clock::now();

    return std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
}

auto multiply_ikj(const matrix &A, const matrix &B, matrix &C) {
    auto start = std::chrono::high_resolution_clock::now();

    for (int i = 0; i < N; ++i) {
        for (int k = 0; k < N; k++) {
            for (int j = 0; j < N; ++j) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }

    auto end = std::chrono::high_resolution_clock::now();

    return std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();
}

int main(int argc, char **argv) {
    matrix A, B, C_ijk, C_ikj;

    // Initialize sizes
    A.resize(N);
    B.resize(N);
    C_ijk.resize(N);
    C_ikj.resize(N);
    for (int i = 0; i < N; ++i) {
        A[i].resize(N);
        B[i].resize(N);
        C_ijk[i].resize(N);
        C_ikj[i].resize(N);
    }

    // Initialize A and B values
    for (int i = 0; i < N; ++i) {
        for (int j = 0; j < N; ++j) {
            A[i][j] = static_cast<double>(i * N + j);
            B[i][j] = static_cast<double>(i * N + j);
        }
    }

    // BENCHMARK
    for (int s = 0; s < 5; ++s) {
        std::cout << std::endl << "Step: " << s << std::endl;

        reset_matrix(C_ijk);
        std::cout << "IJK: " << multiply_ijk(A, B, C_ijk) << " ms" << std::endl;

        reset_matrix(C_ikj);
        std::cout << "IKJ: " << multiply_ikj(A, B, C_ikj) << " ms" << std::endl;

        assert(are_equal(C_ijk, C_ikj));
    }

    return 0;
}

```

```console
Step: 0
IJK: 30311 ms
IKJ: 14430 ms

Step: 1
IJK: 26369 ms
IKJ: 14486 ms

Step: 2
IJK: 26373 ms
IKJ: 14897 ms

Step: 3
IJK: 26413 ms
IKJ: 14403 ms

Step: 4
IJK: 26300 ms
IKJ: 15300 ms
```
