# Pytest Documentation

## Introduction

Brief introduction about pytest.

## Pytest

The pytest framework makes it easy to write small, readable tests, and can scale to support complex functional testing for applications. In the larger Python ecosystem, there are a lot of testing tools. Pytest stands out among them due to its ease of use and its ability to handle increasingly complex testing needs.

## To Install pytest

```bash
pip install pytest
```

## Expectation of pytest

Pytest expects our tests to be located in files whose names begin with `test_` or end with `_test.py`. 

### Run multiple tests

Pytest will run all files of the form `test_*.py` or `*_test.py` in the current directory and its subdirectories.

### Assert statements

Pytest allows you to use the standard Python `assert` for verifying expectations and values in Python tests.

### Example

```python
# test_sample.py
def f():
    return 3

def test_function():
    assert f() == 4
```

Running the test:

```bash
$ pytest
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
rootdir: /home/sweet/project
collected 1 item

test_sample.py F                                                     [100%]

================================= FAILURES =================================
_______________________________ test_answer ________________________________

    def test_answer():
>       assert func(3) == 5
E       assert 4 == 5
E        +  where 4 = func(3)

test_sample.py:6: AssertionError
========================= short test summary info ==========================
FAILED test_sample.py::test_answer - assert 4 == 5
============================ 1 failed in 0.12s =============================
```

The `[100%]` refers to the overall progress of running all test cases. After it finishes, pytest then shows a failure report because `f(3)` does not return `4`.

### Use the raises helper to assert that some code raises an exception

```python
# test_sysexit.py
import pytest

def f():
    raise SystemExit(1)

def test_mytest():
    with pytest.raises(SystemExit):
        f()
```

### Group Multiple tests in a Class

Pytest makes it easy to create a class containing more than one test.

Example:

```python
# test_class.py
class TestClass:
    def test_one(self):
        x = "this"
        assert "h" in x

    def test_two(self):
        x = "hello"
        assert hasattr(x, "check")
```

Pytest discovers all tests following its Conventions for Python test discovery, so it finds both `test_` prefixed functions. There is no need to subclass anything, but make sure to prefix your class with `Test` otherwise the class will be skipped. We can simply run the module by passing its filename.

Grouping tests in classes can be beneficial for the following reasons:

- **Test organization**
- **Sharing fixtures for tests only in that particular class**
- **Applying marks at the class level and having them implicitly apply to all tests**

Reference: [Getting Started with pytest](https://docs.pytest.org/en/stable/getting-started.html#get-started)