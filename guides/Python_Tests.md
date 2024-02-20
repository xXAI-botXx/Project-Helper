# Python Tests

[<img align="right" width=150px src="D:/Informatik/Project-Helper/res/rackete_2.png"></img>](../README.md)

- [Why?](#why?)
- [How?](#how?)
- [Test Methods](#test-methods)



> click the **rocket** for going **back**



---

### Why?

Testing your data makes sure that your programmed logic is working. You will find yourself often in situations where you have to find a mistake/bug and you have to check your whole code. Does every code work like it should? Is there a wrong planning in a functionality?<br>And if you test your data, you can make sure that it works like it should. Often it is very difficult to automatically check if the whole code works as it should and this would be also not helpful finding a bug, because the bug could still be everywhere...you only know if your code works or not.<br>It most likely makes sense to test small units of your code. Like single methods, functions, classes and other smaller parts. If the ground is correct you can easily build on top of it and can be sure that it works. And if you make changes, you maybe can find your bug easily with the tests of the smaller units.

There many different libraries to test your data, you can try to write any case or maybe only the extreme cases, you can write fail tests, correct tests and you much more. I think everybody should decide what exactly using, depending on the size and type of the project. We will not further discuss these here.<br>I only want to name TDD (Test Driven Developement). Here you first write a test for a functionionality/feature you want to implement. Then you make sure the test really fails (else the test is wrong or you already arrived at your goal). Then you create your code that you barely pass the test (means without optimization and cleaning) and last step is to optimize and clean your code. This can make sure that your code works as intended and help you to write cleaner and better code. But depending on your project it can be very annoying and too much overhead, speaking from a machine learning point.

So write only tests when needed. Think of, what you want to test, which cases should be covered, what is the real goal and if it is benefitial for your project.

As example in analyzing tasks you probably don't want to use tests, because the goal is to anaylze data or something else and often there is no code logic which can be tested well. But if you program a app or software where an machine learning works inside, you can test a lot, but not everything. <br>In the end tests should be helpful, so use them not always but then when needed.

Hint: It often happened that the code works like intended but my tests was wrong, so make sure to write your tests right :)



---

### How?

As said there are many different libraries. We will concentrate on a simple implementation, the unittest library which is already a standard library in python (preinstalled). It is simple, often used and provides most important features. Feel free to use another library.



You just have to create a new .py file and there you create a class which inherts from unittest.TestCase. Then you can run unittest.main() to run the defined tests. The test itself are the methods you write in this new class.<br>You can create many test .py files, many test classes and these also can add different methods. So you can structure it like you want. Maybe adding a new test .py file for every sub-project or maybe you have only one test py file and add a new class for every sub-project or your project is small enough to add only new methods. If you have very complex methods with many cases, you also can add a new test class for every method and so every test py file is for one normal class/python file. Else you also can write more than 1 test in a method.

To write a test, you use the asserts methods of the unittest.TestCase. Like self.assertEqual().



``````python
import unittest
from my_module import my_func

def my_important_func(value:int):
    if value > 3.14:
        raise ValueError("The value is too high.")
    else:
        return 3.14

class Test_my_func(unittest.TestCase):
    def test_my_func_1(self):
        res = my_func(6, 8, "Hey")
        self.assertEqual(res, True)
        
        res = my_func(-3, 8, "Hey")
        self.assertTrue(res)
        
    def test_my_important_func(self):
        self.assertEqual(my_important_func(1), 3.14)
        self.assertEqual(my_important_func(3), 3.14)
        self.assertEqual(my_important_func(-1), 3.14)
        self.assertEqual(my_important_func(-4), 3.14)
        with self.assertRaises(ValueError):
            my_important_func(4)
        # or simpler:
        self.assertRaises(ValueError, lambda: my_important_func(6))

        
if __name__ == "__main__":
    unittest.main()
``````



It is also possible to you simulate an object. Like it would exist, that is helpful, so that the test can't fail because of a mistake in the real class/object. But also have the negative side effect that a change of the object would lead in the same correct test result but in reality it would fail, so sometimes it can be good to use the real object or use this so called mock only partwise or you think on change also the mock, when changing the object, or using tests with mock and the real object.

``````python
import unittest
from unittest.mock import MagicMock

def my_func(obj, value):
    if obj.is_ready():
        return obj.get_value(value)
    else:
        return -1

class Test_with_Mock(unittest.TestCase):
    def test_my_important_func(self):
        obj = MagicMock()
        obj.is_ready.return_value = True
        obj.get_value.return_value = 12
        self.assertEqual(my_func(obj, 3), 12)
        obj.is_ready.return_value = False
        self.assertEqual(my_func(obj, 3), -1)
        
if __name__ == "__main__":
    unittest.main()
``````



You also can skip tests

``````python
import unittest
from my_module import my_func

def my_important_func(value:int):
    if value > 3.14:
        raise ValueError("The value is too high.")
    else:
        return 3.14

class Test_With_Skippings(unittest.TestCase):
    @unittest.skip
    def test_my_func_1(self):
        res = my_func(6, 8, "Hey")
        self.assertEqual(res, True)
        
        res = my_func(-3, 8, "Hey")
        self.assertTrue(res)
        
    @unittest.skip("reason for skipping")
    def test_my_important_func(self):
        self.assertEqual(my_important_func(1), 3.14)
        self.assertEqual(my_important_func(3), 3.14)
        self.assertEqual(my_important_func(-1), 3.14)
        self.assertEqual(my_important_func(-4), 3.14)
        with self.assertRaises(ValueError):
            my_important_func(4)
        # or simpler:
        self.assertRaises(ValueError, lambda: my_important_func(6))

        
if __name__ == "__main__":
    unittest.main()
``````



The mathods **setUp** and **tearDown** can be used to prepare something for the test and remove it then. That can be many things. Maybe you want to create a temporary file or directory and use it to test if your code is working.

``````python
import unittest
import tempfile
import os

class Test_With_Preparation(unittest.TestCase):
    
    def setUp(self):
        self.test_dir = tempfile.TemporaryDirectory()
        self.test_file_path = os.path.join(self.test_dir.name, 'test.txt')
    
    def tearDown(self):
        self.test_dir.cleanup()
    
    def test_file_creation(self):
        with open(self.test_file_path, 'w') as f:
            f.write('test content')
        self.assertTrue(os.path.exists(self.test_file_path))
    
    def test_file_deletion(self):
        with open(self.test_file, 'w') as f:
            f.write('test content')
        os.remove(self.test_file)
        self.assertFalse(os.path.exists(self.test_file))

if __name__ == '__main__':
    unittest.main()
``````



---

### Test Methods

Following I list you different methods for testing with unittests. These are methods from the unittest.TestCase class.

- `assertEqual(first, second, msg=None)`: Checks if two values are equal.
- `assertNotEqual(first, second, msg=None)`: Checks if two values are not equal.
- `assertTrue(expr, msg=None)`: Checks if the expression is True.
- `assertFalse(expr, msg=None)`: Checks if the expression is False.
- `assertIs(first, second, msg=None)`: Checks if two objects refer to the same object.
- `assertIsNot(first, second, msg=None)`: Checks if two objects do not refer to the same object.
- `assertIsNone(expr, msg=None)`: Checks if the expression is None.
- `assertIsNotNone(expr, msg=None)`: Checks if the expression is not None.
- `assertIn(first, second, msg=None)`: Checks if the first value is in the second value.
- `assertNotIn(first, second, msg=None)`: Checks if the first value is not in the second value.
- `assertIsInstance(obj, cls, msg=None)`: Checks if an object is an instance of a class.
- `assertNotIsInstance(obj, cls, msg=None)`: Checks if an object is not an instance of a class.
- `assertRaises(excClass, callableObj=None, *args, **kwargs)`: Checks if an exception is raised when calling a function.
- `assertRaisesRegex(excClass, regex, callableObj=None, *args, **kwargs)`: Checks if an exception is raised and its message matches a regex pattern.
- `assertWarns(warn, callableObj=None, *args, **kwargs)`: Checks if a warning is issued when calling a function.
- `assertWarnsRegex(warn, regex, callableObj=None, *args, **kwargs)`: Checks if a warning is issued and its message matches a regex pattern.
- `assertAlmostEqual(first, second, places=7, msg=None, delta=None)`: Checks if two floating-point numbers are approximately equal.
- `assertNotAlmostEqual(first, second, places=7, msg=None, delta=None)`: Checks if two floating-point numbers are not approximately equal.
- `assertGreater(a, b, msg=None)`: Checks if `a` is greater than `b`.
- `assertGreaterEqual(a, b, msg=None)`: Checks if `a` is greater than or equal to `b`.
- `assertLess(a, b, msg=None)`: Checks if `a` is less than `b`.
- `assertLessEqual(a, b, msg=None)`: Checks if `a` is less than or equal to `b`.
- `assertRegex(text, expected_regex, msg=None)`: Checks if a regex matches a given text.
- `assertNotRegex(text, unexpected_regex, msg=None)`: Checks if a regex does not match a given text.

These methods are commonly used to write test cases and assertions in Python unittests.







