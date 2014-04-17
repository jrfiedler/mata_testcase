testcase - a testing framework for Mata
=======================================


Installing
----------

You can clone the Git repo with

    git clone https://github.com/jrfiedler/mata_testcase

Or you can download a zip archive by clicking on the "Download ZIP" button on the right side of this page.

Introduction
------------

`testcase` is a Mata class providing an [xUnit](http://en.wikipedia.org/wiki/XUnit) type of testing framework. To create tests, create a subclass of `testcase` and write test functions within that subclass. Users who are new to Mata class programming should probably read [m2_class](http://www.stata.com/help.cgi?m2_class).

See Syntax, below, for a simple example. See EXAMPLES.md for more complete examples.

For information about class methods and attributes, see DOC.md.


Syntax
------

Here is a simple example  `testcase`. For a test class called (for example) "mytestclass" and a test instance called "mytest", usage would look like this:

    class mytestclass extends testcase {
        void test_1()
        ... [other declarations]
    }
    
    mytestclass::test_1()
    {
        ...
    }
    
    ... [other function definitions]
    
    mytest = mytestclass()
            
    mytest.name = "mytest"
    mytest.test_names = (
        "test_1",
        ...
    )
    
    mytest.run()


Author
-----
James Fiedler (jrfiedler at gmail dot com)