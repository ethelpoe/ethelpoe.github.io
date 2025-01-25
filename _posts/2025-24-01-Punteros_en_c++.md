---
title: Pointer in C++
author: ethelpoe
date: 2025-24-01 20:55:00 +0800
categories:
  - Blogging
  - Tutorial
tags:
  - Programing
  - c++
pin: false
---

## Creating pointers
We know that we can get the memory address of a variable by using the `&` operator:

```c++
string food = "Pizza"; // A food variable of type string

cout << food;  // Outputs the value of food (Pizza)
cout << &food; // Outputs the memory address of food (0x6dfed4)

```

A pointer however , is a variable that stores the memory address as its value.
A pointer variable points to data type (like  `int` ir `string`) of the same type, and is created with the `*` operatator. 
The address of the variable you are working with is assiggned to the pointer

```c++
string food = "Pizza";  // A food variable of type string
string* ptr = &food;    // A pointer variable, with the name ptr, that stores the address of food

// Output the value of food (Pizza)
cout << food << "\n";

// Output the memory address of food (0x6dfed4)
cout << &food << "\n";

// Output the memory address of food with the pointer (0x6dfed4)
cout << ptr << "\n";
```

here other example of how a pointer works

```c++
#include <iostream>
using namespace std;

int main() {
    int number = 10;       // A normal integer variable
    int* pointer = &number; // A pointer that stores the address of `number`

    // Output the value of the variable
    cout << "Value of number: " << number << endl;

    // Output the address of the variable
    cout << "Address of number: " << &number << endl;

    // Output the address stored in the pointer
    cout << "Address stored in pointer: " << pointer << endl;

    // Output the value at the address stored in the pointer
    cout << "Value pointed to by pointer: " << *pointer << endl;

    // Change the value of `number` using the pointer
    *pointer = 20;

    // Output the updated value of the variable
    cout << "Updated value of number: " << number << endl;

    return 0;
}
```
