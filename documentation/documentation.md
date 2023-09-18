# Documentation - Quantified Expressions

> Authors: Maanav Garg, Michael Hammal, Tausifur Rahman

This file provides the documentation of our implementation for **Quantified Expressions**. Here we've outlined the project's description, which resources we used, how we implemented the extensions, how we tested our implementations, the challenges we faced, and what we have learned from this experience. 

<br/>


## Table of Contents

- [Documentation - Quantified Expressions](#documentation---quantified-expressions)
  - [Table of Contents](#table-of-contents)
  - [1. Project Description](#1-project-description)
    - [1.1. General Project Overview:](#11-general-project-overview)
    - [1.2. Language Extensions:](#12-language-extensions)
    - [1.3. Implementation](#13-implementation)
    - [1.3.1. Modify P0.ipynb](#131-modify-p0ipynb)
    - [1.3.2. Modify SC.ipynb](#132-modify-scipynb)
    - [1.3.3. Modify ST.ipynb](#133-modify-stipynb)
  - [2. Testing](#2-testing)
    - [2.1. Valid Test Cases](#21-valid-test-cases)
    - [2.2. Error Based Test Cases](#22-error-based-test-cases)
  - [3. Challenges](#3-challenges)
  - [4. Future Improvements](#4-future-improvements)
  - [5. Resources](#5-resources)
    - [5.1 Web Assembly:](#51-web-assembly)


## 1. Project Description
> The following outlines a general description of our problem, how the implementation was tested and measured, what insight we gained from this project.

### 1.1. General Project Overview:
The purpose of this project is to extend the P0 language to support quantified expressions including array and set comprehension as well as extend the code generator by generating a loop for each quantified expression expression, as seen in other common programming languages. 

### 1.2. Language Extensions:
- `sorted := all i ∈ 0 .. N - 1 • a[i] ≤ a[i + 1]`   or   `sorted := ∀ i ∈ 0 .. N - 1 • a[i] ≤ a[i + 1]`
- `squares := [i ∈ 0 .. N - 1 • i × i]`   or    `squares := [i × i for i ∈ 0 .. N - 1]`    where squares is an integer array
- `odds := {i ∈ 0 .. N - 1 | i mod 2 = 1 • i}`    or    `odds := {i for i ∈ 0 .. N - 1 if i mod 2 = 1}`    where odds is a set of integers
- `found := ∃ i ∈ 0 .. N - 1 • a[i] = x`    or    `found := some i ∈ 0 .. N - 1 • a[i] = x`

as well as list comprehension and set comprehension

### 1.3. Implementation

### 1.3.1. Modify P0.ipynb

In regards to P0, the following functions were extended: 
- `factor()`: This function was modified, by adding support for the following: 
    - `LBRACE`
    - `LBRAK`
    - `SORTED`
    - `EXISTS`
- `statement()`: For this function, the `if` statement code block pertaining to `BECOMES` was modified and extended. 

### 1.3.2. Modify SC.ipynb

New symbols were encoded in SC. The new symbols are:
- `DOT`: Allowed ‘•’ to be scanned
- `PIPE`: Allowed ‘|’ to be scanned
- `SORTED`: Allowed the scanner to recognize the string “SORTED” and then sort the array
- `EXISTS`: Allowed ‘∃’ to be scanned
- `RANGE`: Allowed the scanner to recognize the string “RANGE” then generates a list with a range of 0 to n. The semantics are the same as Python’s range
- `FOR`: Allowed the parser to recognize the string “FOR” 
- `IN`: Allowed the parser to recognize the string “IN” 

Sorted, exists, range, for and in were parsed in the procedure identKW(). Parsing these keywords directly in getSym() can prevent identifiers starting with ‘s’, ‘sort’ and many others.

### 1.3.3. Modify ST.ipynb

A new type was introduced (Array Elements) that holds the values of the members of the array in order to be able to generate different lists based on list comprehension expressions. Given that, the following class was added to `ST.ipynb`:

```python
class ArrayElements:
    def __init__(self, values):
        self.values = values
    def __str__(self):
        return 'ArrayElements(values = [' + ', '.join(str(i) for i in self.values) + '])'
```

## 2. Testing

To test the new functionality, the test cases are stored under [TestCases.ipynb](../src/TestCases.ipynb)

### 2.1. Valid Test Cases 
The first set of cases produced entail tests that validate and ensure that the functionality is working as designed. Here, the test cases aim to simply check if the code passes with valid syntax. The following is an example that tests `range`:

```python 
compileString("""
program p
    var y: [0..1] → integer
    var i: integer
    y := [i × 5 for i in range 5 if i > 2]
    write(y[0])
    write(y[1])
""", "example.wat")
```

Which then by running the following:
```python
!wat2wasm example.wat
runpywasm("example.wasm")
```

Returns:
```
15
20
```

### 2.2. Error Based Test Cases
Whereas, the second set of cases produced entail tests that simply check various scenarios where the functionality is meant to fail. Here, the test cases aim to check when the code fails, and what error is prompted. The following is an example that tests for `identifiers should be the same` by using the provided `compileerr()`:

```python
assert "identifiers should be the same" in compileerr("""
program p
    var x: set[0..1]
    var i: integer
    var j: integer
    x := {5 | i mod 2 = 1 • j}
""")
```

Which then returns:

```python 
line 6 pos 30 identifiers should be the same in "i mod 2 = 1 • i"
```


## 3. Challenges
After receiving feedback regarding our project proposal, we planned to start working on the project as soon as possible to get a head start. However, due to conflicts with work and other courses in school, we were unable to follow the weekly agenda we had envisioned in the project proposal. We had to take some time to lay out the scope of the project by first introducing the grammar extensions in the required format which we had failed to do for the project proposal. After that, we had a much clearer understanding of the project and the problem we are trying to solve and from there we were able to start piecing together the different components and what we needed to do to extend the P0 compiler with list and set expressions. 

Additionally, from a more niche perspective, we were also struggling with the following:

- Building a robust coding workflow. As most of our “coding” was done on Jupyter Notebook, which was hosted on Jupyter Hub, pushing our changes to GitLab became a day-to-day challenge. Due to which, we opted in sharing any files we modified via file transfer, and opted in making all the changes on one person’s Jupyter Hub. 
- Debugging and understanding the problem. As mentioned above, we really struggled to understand the actual scope of the project in the beginning. Due to which, we ended up employing the concept of “pair programming”, where we debugged and coded by sharing our screens. 
- Organizing time to work together. Since we all had other courses that were overloading our schedules, it was relatively difficult to find and set aside time for all 3 of us to chat and discuss the project. More often than not, only 2 people would be available at the same time. 


## 4. Future Improvements
In the future, we would like to modify our implementation for quantified expressions to include list comprehension with custom ranges. So for example, instead of the ranges starting at `0` and finishing at `N-1` where `N` is a provided integer, we can provide two integers `(N,M)` in the expression such that `N <= M` and the range will start at `N` and end at `M-1` to provide more flexibility in generating lists and sets.

## 5. Resources
> The following outlines the resources we used to complete for this project: 

### 5.1 Web Assembly:
- WebAssembly. (n.d.). Retrieved March 8, 2022, from <https://developer.mozilla.org/en-US/docs/WebAssembly>
- Rossberg, A. (n.d.). WebAssembly Specification. Retrieved March 8, 2022, from <https://webassembly.github.io/spec/core/>
