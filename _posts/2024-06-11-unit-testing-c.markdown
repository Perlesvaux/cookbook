---
layout: post
title:  "Unit Testing (C)"
date:   2024-06-11 16:22:19 -0600
categories: unit testing
tags: unit testing c framework criterion linux
---
* Table of contents
{:toc}

## Installation
Install criterion
```bash
sudo apt install libcriterion-dev
```

Compile source-code and test. Link them to Criterion library
```bash
gcc fun.c test.c -o testing -l criterion
```

 Run test!
```bash
./testing
```

## Unit testing sample

Function example fun.c
```bash
char * greet() {
    return "Hello World";
}
```
Test example test.c 
```c
#include <criterion/criterion.h>
#include <string.h>

char * greet();

Test(greet_tests, returns_hello_world){
  char * result = greet();
  cr_assert_str_eq(result, "Hello World", "Expected 'Hello World' but got '%s'", result);
}

Test(greet_tests, returns_eleven){
  char * result = greet();
  size_t len = strlen(result);
  cr_assert_eq(len, 11, "Expected length of 11 but got %zu", len);
}
```

## Assumed project structure
```
myProject/
├── fun.c
├── test.c
└── testing
```
