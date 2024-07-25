<img src="https://numpy.org/images/logo.svg" alt="NumPy logo" height="110">

# Introduction to NumPy for Data Science <!-- omit from toc -->

## Table of Contents <!-- omit from toc -->
- [1. Overview](#1-overview)
- [2. Key Features](#2-key-features)
  - [2.1. Speed and Efficiency](#21-speed-and-efficiency)
- [3. Installation](#3-installation)
- [4. Creating Arrays](#4-creating-arrays)
  - [4.1. 1-Dimensional Arrays](#41-1-dimensional-arrays)
  - [4.2. 2-Dimensional Arrays](#42-2-dimensional-arrays)
  - [4.3. Arrays with Specific Values](#43-arrays-with-specific-values)
  - [4.4. Arrays with Evenly Spaced Values](#44-arrays-with-evenly-spaced-values)
  - [4.5. Random Arrays](#45-random-arrays)
- [5. Summary Operations](#5-summary-operations)
  - [5.1. 1-Dimensional Array](#51-1-dimensional-array)
  - [5.2. 2-Dimensional Array](#52-2-dimensional-array)
- [6. Math Operations](#6-math-operations)
  - [6.1. Single Array Operations](#61-single-array-operations)
  - [6.2. Multiple Array Operations](#62-multiple-array-operations)
  - [6.3. Element-wise Comparison](#63-element-wise-comparison)
- [7. Accessing Elements](#7-accessing-elements)
  - [7.1. 1-Dimensional Array](#71-1-dimensional-array)
  - [7.2. 2-Dimensional Array](#72-2-dimensional-array)
- [8. Reshaping Arrays](#8-reshaping-arrays)
- [9. Stacking Arrays](#9-stacking-arrays)
  - [9.1. Horizontal and Vertical Stacking](#91-horizontal-and-vertical-stacking)
- [10. Array Features](#10-array-features)
  - [10.1. Array Properties](#101-array-properties)
- [11. Example: Calculating Planetary Volumes](#11-example-calculating-planetary-volumes)
  - [11.1. Data](#111-data)
  - [11.2. Calculations](#112-calculations)
  - [11.3. Scaling Up](#113-scaling-up)


## 1. Overview

NumPy, a portmanteau of "Numerical Python," is a fundamental library for numerical and mathematical operations in Python. The core data structure in NumPy is the ndArray (n-dimensional array), and much of its functionality is implemented in the C programming language for enhanced performance. NumPy is extensively used in other popular Python packages such as Pandas, Matplotlib, and scikit-learn.

## 2. Key Features

### 2.1. Speed and Efficiency
- Written in C, providing much faster execution than pure Python.
- Uses homogenous data types, which improves memory efficiency.
- Supports parallel processing to divide and run sub-tasks concurrently.

## 3. Installation

To install NumPy, use the following command:
```python
pip install numpy
```

To import NumPy in your Python code, use:
```python
import numpy as np
```
Note: If you are using a distribution like Anaconda, NumPy comes pre-installed.

## 4. Creating Arrays

### 4.1. 1-Dimensional Arrays
```python
np.array([1, 2, 3])
# Output: array([1, 2, 3])
```

### 4.2. 2-Dimensional Arrays
```python
np.array([[1, 2, 3], [4, 5, 6]])
# Output: array([[1, 2, 3],
#                [4, 5, 6]])
```

### 4.3. Arrays with Specific Values
```python
np.zeros(5)
# Output: array([0., 0., 0., 0., 0.])

np.ones(5)
# Output: array([1., 1., 1., 1., 1.])

np.full(4, 10)
# Output: array([10, 10, 10, 10])

np.empty(2)
# Output: array([uninitialized, uninitialized])
```

### 4.4. Arrays with Evenly Spaced Values
```python
np.arange(2, 18, 4)
# Output: array([2, 6, 10, 14])

np.linspace(1, 3, 5)
# Output: array([1., 1.5, 2., 2.5, 3.])
```

### 4.5. Random Arrays
```python
np.random.random((2, 3))
# Output: array([[random values], [random values]])

np.random.randint(2, 7, 5)
# Output: array([random integers])
```

## 5. Summary Operations

### 5.1. 1-Dimensional Array
```python
my_1d_array = np.random.randint(0, 10, 7)
# Example Output: array([7, 6, 4, 2, 9, 6, 7])

my_1d_array.max()
# Output: 9

my_1d_array.min()
# Output: 2

my_1d_array.mean()
# Output: 5.857142857142857

my_1d_array.sum()
# Output: 41

my_1d_array.std()
# Output: 2.099562636671296
```

### 5.2. 2-Dimensional Array
```python
my_2d_array = np.random.randint(0, 10, (2, 3))
# Example Output: array([[6, 1, 7],
#                        [9, 0, 6]])

my_2d_array.max()
# Output: 9

my_2d_array.max(axis=0)
# Output: array([9, 1, 7])

my_2d_array.max(axis=1)
# Output: array([7, 9])
```

## 6. Math Operations

### 6.1. Single Array Operations
```python
a = np.array([1, 2, 3, 4, 5])
# Output: array([1, 2, 3, 4, 5])

a + 10
# Output: array([11, 12, 13, 14, 15])

a - 10
# Output: array([-9, -8, -7, -6, -5])

a * 10
# Output: array([10, 20, 30, 40, 50])

a / 10
# Output: array([0.1, 0.2, 0.3, 0.4, 0.5])
```

### 6.2. Multiple Array Operations
```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

np.add(a, b)
# Output: array([5, 7, 9])

np.subtract(a, b)
# Output: array([-3, -3, -3])

np.multiply(a, b)
# Output: array([4, 10, 18])

np.divide(a, b)
# Output: array([0.25, 0.4, 0.5])

np.dot(a, b)
# Output: 32
```

### 6.3. Element-wise Comparison
```python
a = np.array([1, 2, 3])
b = np.array([4, 2, 6])

a == b
# Output: array([False, True, False])

np.array_equal(a, b)
# Output: False
```

## 7. Accessing Elements

### 7.1. 1-Dimensional Array
```python
a = np.array([1, 2, 3, 4, 5])
# Output: array([1, 2, 3, 4, 5])

a[0]
# Output: 1

a[1:4]
# Output: array([2, 3, 4])

a[-1]
# Output: 5
```

### 7.2. 2-Dimensional Array
```python
a = np.array([[1, 2, 3], [4, 5, 6]])
# Output: array([[1, 2, 3],
#                [4, 5, 6]])

a[0]
# Output: array([1, 2, 3])

a[0][1]
# Output: 2
```

## 8. Reshaping Arrays
```python
a = np.array([[1, 2, 3], [4, 5, 6]])
# Output: array([[1, 2, 3],
#                [4, 5, 6]])

a.reshape(3, 2)
# Output: array([[1, 2],
#                [3, 4],
#                [5, 6]])

a.flatten()
# Output: array([1, 2, 3, 4, 5, 6])
```

## 9. Stacking Arrays

### 9.1. Horizontal and Vertical Stacking
```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

np.hstack((a, b))
# Output: array([1, 2, 3, 4, 5, 6])

np.vstack((a, b))
# Output: array([[1, 2, 3],
#                [4, 5, 6]])
```

## 10. Array Features

### 10.1. Array Properties
```python
a = np.array([[1, 2, 3], [4, 5, 6]])
# Output: array([[1, 2, 3],
#                [4, 5, 6]])

a.shape
# Output: (2, 3)

a.ndim
# Output: 2

a.size
# Output: 6
```

## 11. Example: Calculating Planetary Volumes

### 11.1. Data
```python
radii = np.array([2439.7, 6051.8, 6371, 3389.7, 69911, 58232, 25362, 24622])
# Output: array([2439.7, 6051.8, 6371, 3389.7, 69911, 58232, 25362, 24622])
```

### 11.2. Calculations
```python
volumes = 4/3 * np.pi * radii**3
# Output: array([6.08e+10, 9.28e+11, 1.08e+12, 1.63e+11, 1.43e+15, 8.27e+14, 6.83e+13, 6.25e+13])
```

### 11.3. Scaling Up
```python
radii = np.random.randint(1, 1000, 1000000)
volumes = 4/3 * np.pi * radii**3
# Output: Calculated for one million planets in a fraction of a second!
```
