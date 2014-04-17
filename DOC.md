Documentation
=============

Contents
--------

- Creating tests
- `testcase` functions
    * Assert functions
    * Running tests and viewing results
    * Pre- and post-test functions
- `testcase` variables
    * Required
    * Optional
- Diagnostics


Creating tests
--------------

As README.md says, and as EXAMPLES.md demonstrates, you use testcase by

1. subclassing testcase,
2. creating test functions within your subclass, and
3. creating sub-tests with the inherited `assert` functions.

The test functions you create should have a declaration like

    void mytestfunction()
                
In other words, these functions should not return any values and should not take any arguments.

As with any Mata class, you must declare the test functions in the (sub)class declaration and then define them. With test functions there is an additional requirement. You must put the name of the function in the `test_names` variable (see EXAMPLES.md or **testcase variables** below).

        
`testcase` functions
--------------------

Assert functions

- _void_ **assert**(_real scalar v_)

    * `assert()` is used to test that a condition is true. In Mata "true" is encoded as 1 (or non-zero real scalar), and "false" is encoded as 0. Thus,` assert()` only accepts a real scalar as input. It records an "error" if the input is not a real scalar, a "fail" if the input is zero, or a "pass" otherwise.

- _void_ **assert_equal**(_transmorphic a_, _transmorphic b_)

    * `assert_equal()` is used to test that its two inputs are equal. It records a "pass" if the inputs are equal or a "fail" otherwise.

- _void_ **assert_unequal**(_transmorphic a_, _transmorphic b_)

    * `assert_unequal()` is used to test that its two inputs are not equal. It records a "pass" if the inputs are not equal or a "fail" otherwise.

- _void_ **assert_same_contents**(_transmorphic a_, _transmorphic b_ [, _real scalar samecount_])

    * `assert_same_contents()` is used to test that two matrices (or scalars) `a` and `b` have the same contents, regardless of how they are arranged.  For example, the matrices `(9, 10, 11)`, `(10, 11, 9)`,  and `(11 \ 10 \ 9)` all have the same contents. 
    * The optional input `samecount` determines whether the same elements must occur the same number of times. For example, with `samecount = 0`, `(9, 10, 11)`  and  `(9, 9, 10 \ 11, 11, 11)` are judged to have the same contents. The default value for `samecount` is 1, i.e., each element must occur the same number of times. 
    * `assert_same_contents()` records an "error" if `a` or `b` is (a matrix of) `struct` or `class` (an `asarray` is used to count contents, and `asarray`'s cannot have `struct` or `class` keys). It records a "fail" if `a` and `b` do not have the same contents. It records a "pass" otherwise.

- _void_ **assert_close**(_numeric a_, _numeric b_ [, _real scalar tolerance_])

    * `assert_close()` is used to test that its first two inputs `a`, `b` are nearly the same, element by element. More specifically, it checks that `mreldif(a, b) < tolerance` where `tolerance` is the third, optional input (default value is 1e-12). 
    * `assert_close()` records an "error" if either `a` or `b` is not numeric, or if the tolerance (if specified) is not a real scalar. It records a "fail" if any of the corresponding elements are not close or a and b have different dimensions. It records a "pass" otherwise.

- _void_ **assert_all**(_real a_)

    * `assert_all()` is used to test that all elements of a matrix evaluate to "true", i.e., are real and non-zero. It records an "error" if its input is not real, a "fail" if any of the input's elements is zero, or a "pass" otherwise.

- _void_ **assert_any**(_real a_)

    * `assert_any()` is used to test that any element of a matrix evaluates to "true", i.e., is real and non-zero. It records an "error" if its input is not real, a "fail" if all of the input's elements are zero, or a "pass" otherwise.

- _void_ **assert_error**(_real scalar err_, _pointer(function) scalar func_ptr_ [, _pointer matrix arg_ptrs_])

    * `assert_error()` is used to test that a function raises an error when expected to. The first input should be the intended error code (or zero for "no error") or a missing value. If the first input is a missing value, the test passes when the function raises any (non-zero) error. `assert_error()` records an "error" if any of the inputs is miss-specified, a "fail" if the function does not raise the intended error, or a "pass" otherwise.

- _void_ **assert_method_error**(_real scalar err_, _string scalar class_name_, _string scalar func_ptr_ [,_ pointer matrix arg_ptrs_])

    * `assert_method_error()` is used to test that a class member function raises an error when expected to. The first input should be the intended error code (or zero for "no error") or a missing value. If the first input is a missing value, the test passes when the function raises any (non-zero) error. It records an "error" if any of the inputs is miss-specified, a "fail" if the function does not raise the intended error, or a "pass" otherwise.


**Running tests and viewing results**

- _void_ **run**()

    * `run()` instructs the class to "run the tests". Each of the functions named in `test_names` is called, information about passed tests, failures, and errors is collected, and, if verbose is not zero (see **testcase variables** below), a summary is printed.

- _void_ **print_summary**()

    * `print_summary()` instructs the class to print a summary of the previous run.  The printed summary is the same as when using `run()` with nonzero `verbose`.


**Pre- and post-test functions**
        
- _virtual void_ **setup**()

    * `setup()` is used for any kind of preparation that is desired to occur before each test function in your `testcase` subclass is called. `setup()` is automatically called before each test function.

- _virtual void_ **setup_once**()

    * `setup_once()` is used for any kind of preparation that is desired to occur just once, before any test function in your `testcase` subclass is called. `setup_once()` is automatically called before the first test function is called (and before the first `setup()`).

- _virtual void_ **teardown**()

    * `teardown()` is used for any kind of activity that is desired to occur after each test function in your `testcase` subclass is called. `teardown()` is automatically called after each test function.

- _virtual void_ **teardown_once**()

    * `teardown_once()` is used for any kind of post-test activity that is desired to occur just once, after all of the test functions in your `testcase` subclass have been called. `teardown_once()` is automatically called after the last test function is called (and after the last `teardown()`).

- _void_ **new**()

    * `new()`, as with any Mata class, is used during class creation. Use this function for any kind of preparation or customization that you wish to occur when your `testcase` subclass is created.


`testcase` variables
--------------------

**Required**
        
- _string scalar_ **name**

    * `name` should be equal to the name of the class instance, as in the examples in EXAMPLES.md. `name` must be supplied for each class instance.

- _string vector_ **test_names**

    * `test_names` should be a vector containing the names of all of the class functions that serve as tests. You may define other class functions if desired and not include them, but the class will only know to run the tests in `test_names`. `testnames` must be supplied for each class instance.


**Optional**
        
- _real scalar_ **verbose**

    * verbose determines whether there is any output when run() is invoked.  Set `mytest.verbose = 0` to silence output. Set `mytest.verbose = 1` (or any non-zero real value) to see output. The default value is 1. To see output when verbose is zero, use `print_summary()`.

- _real scalar_ **noisy_capture**

    * `noisy_capture` determines whether the default Mata error messages are shown when there is an unexpected error in a test. (See EXAMPLES.md.) Set `mytest.noisy_capture = 0` to silence these error messages. Set `mytest.noisy_capture = 1` (or any non-zero real value) to see these error messages. The default value is 1.

- _real scalar_ **noisy_assert**

    * `noisy_assert` determines whether the default Mata error messages are shown for the `assert_error()` and `assert_method_error()` functions. This will not usually be helpful, since errors are usually expected with these functions, but may be helpful when diagnosing an unexpected result. Set `mytest.noisy_assert = 0` to silence these error messages. Set `mytest.noisy_assert = 1` (or any non-zero real value) to see these error messages. The default value is 0.

- _real scalar_ **exit_setup_error**

    * `exit_setup_error` determines whether testing should continue after an error in `setup()` or `setup_once()`. A non-zero value signals that testing should be aborted, a zero value signals that testing should continue. The default value is 1.

- _real scalar_ **exit_teardown_error**

    * `exit_teardown_error` determines whether testing should continue after an error in `teardown()` (testing is complete after `teardown_once()` regardless of errors). A non-zero value signals that testing should be aborted, a zero value signals that testing should continue. The default value is 1.


Diagnostics
-----------

If you get an error message when you invoke `run()`, like

    : mytest.run()
    type mismatch:  exp.exp:  transmorphic found where struct expected
        
make sure that you've set the name variable, and that you've set it to the name of the instance and not, for example, the name of the class.

If you implement the optional `setup()`, `setup_once()`, `teardown()`, or `teardown_once()` functions, you might have difficulty dropping your test class and/or `testcase()`. The only remedy at present is to clear Mata.

There are several variables and functions used internally that are not listed above. To prevent collision with existing functions and variables, avoid names beginning with double underscore. Alternately, if wanting to use a name that begins with double underscore, verify that the name is not in use by checking the class declaration in `testcase.mata`.
