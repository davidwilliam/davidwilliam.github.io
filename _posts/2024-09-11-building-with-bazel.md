---
layout: post
title: Bulding and Testing a C++ Project with Bazel
date: 2024-09-11 10:11:00
description: Bulding and testing a C++ project with Bazel
tags: bazel build tool
categories: programming build test bazel
comments: true
og_image: /assets/img/metaprogramming.jpg
---

Bazel is an opensource build and test tool. It was formelly named Blaze when used internally at Google. It is similar to [Make](https://www.gnu.org/software/make/), [Maven](https://maven.apache.org/index.html), and [Gradle](https://gradle.org/). Bazel aims to combine simplicity and power since it uses a human-readable, high-level build language while supporting projects in multiple languages and building outputs for multiple platforms.

Some of the highlights of Bazel include:

- **Scalability:** allows for building large codebases very quickly using parallel execution, remote caching, and remote execution.
- **Multi-language support:** engineering teams can standardize a single build tool for many platforms. For exemple, Bazel can be used for iOS and Android applications as well for the server code supporting those apps in whatever language.
- **Repeatable builds:** release builds can be identical to developer builds. This is particular useful for reproducing issues detected ih the releasead version of any project. It also adds extra safety to the developer workflow.
- **Extensible:** It can be adopted to new languages and custom tooling.
Bazel aims at developers struggling with builds that take too long and/or are too complex to maintain.

Bazel uses [Starlark](https://github.com/bazelbuild/starlark), a language intended for use as a configuration language which was designed for the Bazel build system, but may be also useful for other projects.

Starlark's syntax is inspired by Python3. While its semantics can differ from Python, behavioral differences are supposed to be rare.

The rules for most languages are written in Starlark so Bazel is thought of as entire ecosystem of core features and many available rule sets to serve whatever needs a developer might have.

The development of Bazel's core is driven by Google. However, there is a growing community of contributors and service providers working on Bazel's overall ecosystem.

By default, Bazel executes builds and tests in the developer's local machine. Remote execution allows Bazel to distribute the build and test actions accross multiple machines as a data center. Remote execution provides some benefits. First, faster build and test executions through parallel actions. Also, a consistent execution environment for a development team. Finally, reuse of build outputs accross a development team. So if somebody else already built artifacts required for a developer's build, that build will be significantly faster. Both execution methods can be combined through dynamic execution. Complex builds that complete earlier remotely while small incremental changes might be more efficient on the developer's local machine. This is particularly useful for developers working from home.

# How Bazel Works

Bazel is an artifact-based build system. In such a system, engineers config build files. A build file is a declarative manifest that tells the build system what to do (the artifact) and the build system (Bazel) determines how to do it.

To illustrate how Bazel works, we will create a toy C++ project. Our project consist of a program that that asks the user for a number. If the user types an integer, the program will compute the factorial of that number. If it types a double, it will compute the [Gamma function](https://en.wikipedia.org/wiki/Gamma_function) (an extesion of the factorial function to real and complex numbers).

In this example, we want to build three artifacts with Bazel:

1. An executable binary called compute_factorial
2. A library called factorial
3. And another library called gamma

We will create two directories:

- main: for our executable binary
- lib: for our libraries

In the root of our project, we will create a file called `MODULE.bazel` which identifies the directory and its contents as a **Bazel workspace** and lives at the root of the project's directory structure. It's also where you specify your external dependencies (when needed). Inside the directories main and lib, we will have a file called `BUILD`, which tells Bazel how to build different parts of the project. A directory within the workspace that contains a BUILD file is a **package**. 

Our project structure will look like the following:

```
/ (project root)
├── lib
│   ├── BUILD
│   ├── factorial.cc
│   ├── factorial.h
│   ├── gamma.cc
│   └── gamma.h
├── main
│   ├── BUILD
│   └── compute_factorial.cc
└── MODULE.bazel
```

Now, we write the code for our project:

`factorial.h`:

```c++
#ifndef FACTORIAL_H
#define FACTORIAL_H

int factorial(int n);

#endif
```

`factorial.cc`:

```c++
#include "factorial.h"
#include <iostream>

int factorial(int n) {
  if (n == 0 || n == 1) {
    return 1;
  } else {
    return n * factorial(n - 1);
  }
}
```

`gamma.h`:

```c++
#ifndef GAMMA_H
#define GAMMA_H

double gamma_function(double x);

#endif
```

`gamma.cc`:

```c++
#include "gamma.h"
#include <cmath>

double gamma_function(double x) {
    return std::tgamma(x + 1);
}
```

`compute_factorial.cc`:

```c++
#include <iostream>
#include "../lib/factorial.h"
#include "../lib/gamma.h"

int main() {
    std::cout << "Enter a number: ";
    double number;
    std::cin >> number;

    if (std::cin.fail()) {
        std::cerr << "Invalid input. Please enter a valid number.\n";
        return 1;
    }

    if (number == static_cast<int>(number)) {
        std::cout << "Factorial of " << static_cast<int>(number) << " is " << factorial(static_cast<int>(number)) << std::endl;
    } else {
        std::cout << "Gamma function of " << number << " is " << gamma_function(number) << std::endl;
    }

    return 0;
}
```
All the C++ code we need is created. Now we can focus on building our artifacts.

In the file `MODULE.bazel`, we will just identify the name of the workspace:

```bazel
module(name = "factorial_and_gamma")
```

The `BUILD` for the `lib` package will contain the following:

```bazel
cc_library(
    name = "factorial",
    srcs = ["factorial.cc"],
    hdrs = ["factorial.h"],
    visibility = ["//main:__pkg__"],
)

cc_library(
    name = "gamma",
    srcs = ["gamma.cc"],
    hdrs = ["gamma.h"],
    visibility = ["//main:__pkg__"],
)
```

The rule [cc_library](https://bazel.build/reference/be/c-cpp#cc_library) is used for for C++-compiled libraries. The result is either a `.so`, `.lo`, or `.a`, depending on what is needed. Bazel's documentation emphasizes that all header files that are used in the build must be declared in the `hdrs` or `srcs` of `cc_*` rules, which is enforced.

The attributes `name`, `srcs`, and `hdrs`, as the names suggest, are for specifying the name, the source files, and the headers for each library. The attribute `visibility` determines what has access to the library. In our case, only the package `main` has access to the libraries `factorial` and `gamma`. If `main` had subpackages, those would not have access to the libraries. Read [Bazel's documentation on visibility](https://bazel.build/concepts/visibility) for more information on other types of access.

The `BUILD` for the `main` package will contain the following:

```bazel
cc_binary(
    name = "compute_factorial",
    srcs = ["compute_factorial.cc"],
    deps = [
        "//lib:factorial",
        "//lib:gamma",
    ],
)
```

The rule [cc_binary](https://bazel.build/reference/be/c-cpp#cc_binary), as the name implies, produces an executable binary. Here we are defining the name, the source files, and the dependecies for building the executable binary.

This is all we need to build our project.

## Building

We will now use Bazel to build the `main` package with the target name`compute_factorial`:

```bash
bazel build //main:compute_factorial
```

Bazel will produce something like:

```bash
INFO: Analyzed target //main:compute_factorial (88 packages loaded, 451 targets configured).
INFO: Found 1 target...
Target //main:compute_factorial up-to-date:
  bazel-bin/main/compute_factorial
INFO: Elapsed time: 0.861s, Critical Path: 0.38s
INFO: 18 processes: 12 internal, 6 darwin-sandbox.
INFO: Build completed successfully, 18 total actions
```

Now, we run our executable binary as follows:

```bash
bazel-bin/main/compute_factorial
```

Which will present the following:

```bash
Enter a number:
```

We will type `5`, which will result in the following:

```bash
Enter a number: 5
Factorial of 5 is 120
```

If we run the program again and type `5.5`, we will see the following result:

```bash
Enter a number: 5.5
Gamma function of 5.5 is 287.885
```

To clean up after Bazel generate the build-related files, we run:

```bazel
bazel clean
```

## Dependency Graph

In our project, we have a binary target called `compute_factorial` in the `main` package that depends on two libraries: `factorial` and `gamma`, both of which are in the `lib` package:

```
                      //main:compute_factorial
                             |
            ----------------------------------------
            |                                      |
      //lib:factorial                         //lib:gamma
            |                                      |
  --------------------                ---------------------
  |                  |                |                   |
lib/factorial.cc  lib/factorial.h   lib/gamma.cc      lib/gamma.h
```

## Testing

We will add testing to our project with [Google Test](https://github.com/google/googletest). 

The first step to add Google Test is to modify our `MODULE.bazel`, which has several methods available to use. One of them is `bazel_dep` for declaring a direct dependency on another Bazel module. 

Our `MODULE.bazel` will be updated to:

```bazel
module(name = "factorial_and_gamma")

bazel_dep(name = "googletest", version = "1.15.2")
```

Next, we create a new package called `test` andd we add two test files, one for each library.

`factorial_test.cc`:

```c++
#include "gtest/gtest.h"
#include "lib/factorial.h"

TEST(FactorialTest, ComputesCorrectly) {
    EXPECT_EQ(factorial(0), 1);
    EXPECT_EQ(factorial(1), 1);
    EXPECT_EQ(factorial(5), 120);
}
```

`gamma_test.h`:

```c++
#include "gtest/gtest.h"
#include "lib/gamma.h"

TEST(GammaTest, ComputesCorrectly) {
    EXPECT_NEAR(gamma_function(5.5), 287.885, 0.001);
}
```

We also add a `BUILD` file to the test package where we define the name, source files and dependencies for our tests:

```bazel
cc_test(
    name = "factorial_test",
    srcs = ["factorial_test.cc"],
    deps = [
        "@googletest//:gtest_main",
        "//lib:factorial",
    ],
)

cc_test(
    name = "gamma_test",
    srcs = ["gamma_test.cc"],
    deps = [
        "@googletest//:gtest_main",
        "//lib:gamma",
    ],
)
```

Our new project structure looks like the following:

```
/ (root)
├── lib
│   ├── BUILD
│   ├── factorial.cc
│   ├── factorial.h
│   ├── gamma.cc
│   └── gamma.h
├── main
│   ├── BUILD
│   └── compute_factorial.cc
├── test
│   ├── BUILD
│   ├── factorial_test.cc
│   └── gamma_test.cc
└── MODULE.bazel
```

Before building the project again, we can either run:

```bash
bazel clean
```

which removes the output directories created by Bazel, or

```bash
bazel clean --expunge
```

When we add `--expunge`, in addition to remove the output directories, all all cached build data, intermediate files, action logs, and Bazel's local cache (where Bazel stores information that speeds up incremental builds) will be also removed. In other words, `bazel clean --expunge` performs a full reset of the Bazel environment.

We can now build the project again:

```bash
bazel build //main:compute_factorial
```

and run the tests:

```bash
bazel test //test:factorial_test
```

Which produces something like:

```bash
INFO: Analyzed target //test:factorial_test (3 packages loaded, 87 targets configured).
INFO: Found 1 test target...
Target //test:factorial_test up-to-date:
  bazel-bin/test/factorial_test
INFO: Elapsed time: 1.468s, Critical Path: 1.38s
INFO: 30 processes: 10 internal, 20 darwin-sandbox.
INFO: Build completed successfully, 30 total actions
//test:factorial_test                                                    PASSED in 0.2s

Executed 1 out of 1 test: 1 test passes.
```

and run

```bash
bazel test //test:gamma_test
```

which produces something like:

```bash
INFO: Analyzed target //test:gamma_test (0 packages loaded, 2 targets configured).
INFO: Found 1 test target...
Target //test:gamma_test up-to-date:
  bazel-bin/test/gamma_test
INFO: Elapsed time: 0.638s, Critical Path: 0.57s
INFO: 9 processes: 6 internal, 3 darwin-sandbox.
INFO: Build completed successfully, 9 total actions
//test:gamma_test                                                        PASSED in 0.2s

Executed 1 out of 1 test: 1 test passes.
```

To run all tests we have the command:

```bash
bazel test //...
```

where `//...` is a wildcard that tells Bazel to run all test targets in the entire workspace, regardless of where they are located.

# Code

You can get the [code for the example](https://github.com/davidwilliam/bazel_sample_project) discussed in this post on GitHub.
