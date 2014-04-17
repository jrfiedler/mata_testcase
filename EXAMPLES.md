`testcase` examples
-------------------

First example
-------------
Suppose you've made a function that tries to return the sum of any two inputs it receives.

    function add(a, b)
    {
        return(a + b)
    }
                
Now you want to write tests for this function, either to verify that it behaves as intended, or to make sure that it will behave as intended after changes are made to the code. We'll walk through how you can make these tests using `testcase`. As a preview, the completed tests will look like   this:

    class add_test extends testcase {
        void test_errors()
        void test_output()
    }
    
    void add_test::test_errors()
    {
        pointer(function) scalar fp
        
        fp = &add()
        
        this.assert_error(3250, fp, (&0, &"a"))
        this.assert_error(3250, fp, (&"a", &0))
        this.assert_error(., fp, (&(1, 2, 3), &(1, 2)))
    }
    
    void add_test::test_output()
    {            
        this.assert_equal(add("a", "b"), "ab")
        this.assert_equal(add(0, 1), 1)
        this.assert_equal(add((0, 1), (1, 0)), (1, 1))
    }
    
    mytest = add_test()
    
    mytest.name = "mytest"
    mytest.test_names = (
        "test_errors",
        "test_output"
    )
    
    mytest.run()

In this example, all of our tests of the `add()` function will reside within a single class, which we'll call `add_test`:

    class add_test extends testcase {
        ...
    }
                        
We'll write tests to verify that `add()` raises errors when we expect it to and otherwise returns the value we expect. We'll put tests for errors within a single function called `test_errors()`, and we'll put the other tests in `test_output()`. These will be functions within our `add_test` class, so our class declaration now looks like

    class add_test extends testcase {
        void test_errors()
        void test_output()
    }

The tests will use two of the assert methods provided by testcase: `assert_error()` and `assert_equal()` (for more info, see DOC.md).

    void add_test::test_errors()
    {
        pointer(function) scalar fp
        
        fp = &add
        
        this.assert_error(3250, fp, (&0, &"a"))
        this.assert_error(3250, fp, (&"a", &0))
        this.assert_error(., fp, (&(1, 2, 3), &(1, 2)))
    }
    
    void add_test::test_output()
    {
        this.assert_equal(add("a", "b"), "ab")
        this.assert_equal(add(0, 1), 1)
        this.assert_equal(add((0, 1), (1, 0)), (1, 1))
    }

The majority of the work is finished. All that we need to do now is create an instance of this class

    mytest = add_test()
        
then record the instance name and test names (so the class knows which methods comprise the tests)

    mytest.name = "mytest"
    mytest.test_names = (
        "test_errors",
        "test_output"
    )
                        
Finally, we run the tests with

    mytest.run()
                        
and the output it produces looks like this:

    test_errors ... ok
    test_output ... ok


Failures and errors
-------------------

Let's add a couple lines to the `test_output()` function from the example above.

    void add_test::test_output()
    {
        this.assert_equal(add("a", "b"), "ab")
        this.assert_equal(add(0, 1), 1)
        this.assert_equal(add((0, 1), (1, 0)), (1, 1))
                    
        this.assert_equal(add(0, 1), "a")
        this.assert(add("a", "b"))
        add()
    }
                
The new lines are the last three lines of the function. They are

1. an assertion that fails (0 + 1 does not equal "a"),
2. an assertion with an error (assert() expects a real scalar input), and
3. an error outside of an assertion.

When we run the tests now, we get output that shows failure and errors (if continuing from the previous example, you'll have to drop `mytest` and redefine it first):

    : mytest.run()        
                       add():  3001  expected 2 arguments but received 0
     add_test::test_output():     -  function returned error
                     <istmt>:     -  function returned error

    test_errors ... ok
    test_output
      subtest 4: assert_equal()
          values are not equal
          > element types differ: real vs string
      subtest 5: assert()
          error: input was string scalar, should be real scalar
      ** unexpected error 3001 after subtest 5 **
                  
The error message immediately after `mytest.run()` corresponds to the "unexpected error" in the last line. If we want to turn off the default Mata error message, we set `noisy_capture` to zero:

    : mytest.noisy_capture = 0

    : mytest.run()

    test_errors ... ok
    test_output
      subtest 4: assert_equal()
          values are not equal
          > element types differ: real vs string
      subtest 5: assert()
          error: input was string scalar, should be real scalar
      ** unexpected error 3001 after subtest 5 **
