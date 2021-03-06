# Intro to Testing Scientific Software

## Sciware Session #2

[https://github.com/flatironinstitute/learn-sciware-dev](https://github.com/flatironinstitute/learn-sciware-dev/tree/master/02_Testing)


# Goals for Today 

- Introduce testing
- Describe how testing is important for scientists 
- Provide examples of various kinds of tests
- Motivate and help folks implement some from of testing into their regular workflow


## Rules of Engagement

### Goal: 

Participants all working to actively foster an environment which encourages participation across experience levels, coding language fluency, technology choices, and scientific disciplines.


## Rules of Engagement

- Avoid discussions between a few people on a narrow topic
- Provide time for people who haven't spoken to speak/ask questions


## Rules of Engagement
### Engage Novices

- Find ways to be sure everybody is following and that folks aren't lost
- Anybody can ask a question, request for clarification, for yourself or the benefit of others


## Rules of Engagement
### Engage Experts

- Find ways to be sure all contributors have a chance to contribute
- Anybody can contribute more on a topic or request more ideas from the group.

(These will always be a work in progress and will be updated, clarified, or expanded as needed.)



## We already do some scientific validation
- print statements
- plots or output files midway through

Implementing "test" formalizes this process


## Example: Crossmatch two lists of fast moving astro objects with sky locations from different decades
- Read in coordinates columns from List #1
- Forecast coordinates of List #1 to date of List #2
- Choose objects from from List #2 with closest to coords to forecasted List #1
- Output matched List with all columns from both lists


# Types of Tests 
- **Integration** - Does the code run as expected from beginning to end?
- **Acceptance/Scientific** - Is the output logical and/or physically realistic?
- **Unit** - Do all the little bits work?
- **Edge** - What unusual cases work?
- **Known Failures** - What does the code NOT do?


## Software Integration Tests
Does the code run as expected from beginning to end?
- Output file should exist
- Output file should be a CSV


## Acceptance/Scientific Integration Tests
Is the output logical and/or physically realistic?
- Number of lines in output matched list should be the same as in List #1
- Characteristics from List #1 and #2 should be similar for matched objects (e.g., faint matched with faint)
- Sample characteristics of List #1 and output matched list should be similar


## Unit Tests
Do all the little bits work?
- Test coordinate forecast method
- Test finding closest coords method


## Edge Tests
What unusual cases work?
- Make a test List #1 with "edge" cases.
## Edges
- objects which didn't match the first time
- objects where closest match isn't correct one
- objects with null or missing data 


## Known Failures
What does the code NOT do?
- objects with insufficient data to test if closest match is the best one
- objects with large uncertainties on location


## Activity: Think of one of each type of test relevant to your current work
- **Software Integration** - Does the code run as expected?
- **Acceptance/Scientific Integration** - Is the output logical and/or physically realistic?
- **Unit** - Do all the little bits work?
- **Edge** - What unusual cases work?
- **Known Failures** - What does the code NOT do?



## What is a "test"?

* Something you can run (a program, script, function)
* distinct from your "real" code (unnecessary)
* that runs relatively quickly (while you wait)
* that executes some portion of your "real" code
* and checks that it is doing what you expect
* and returns success if things are good, or some kind of error otherwise.


## Debugging assertions

Many languages provide some kind of `assert`:

```python
def assert(expr):
    if __debug__:
        if not expr: raise AssertionError
```

```c
#ifndef NDEBUG
#define assert(expr) if (!expr) abort()
#endif
```


```python
x = load_file("input")

y = process(x)

write_file("output", y)
```

A very simple kind of "test" inserts checks into an existing code flow


```python
x = load_file("input")
assert(len(x) == 345)
y = process(x)
assert(len(y) < len(x) and y[0] > 0)
write_file("output", y)
```

This validates some assumptions, but isn't very flexible (hard-coded input)


Instead, write a separate test with hard-coded data:

```python
x = [...]
y = process(x)
assert(len(y) < len(x) and y[0] > 0)
```


## Motivation for testing
##### (from Rosetta tutorials)

Why are tests important? 
* Lets you know your code is working properly
* Lets others know when they have broken your code
* Formal validation

Protect against:
* Bugs
* Crazy user edge cases
* Constantly changing code base



## Strategies for writing tests

Real-world code is more complicated

```
G0 = 100
G1 = 200
global_data = load_file("globals")
z = 0

for i in 1:120
    x = load_file(str(i)+"/input")
    z += f(x)/G0
    y = g(G1, x, z)
    write_file(str(i)+"/output", y)
end
```


### Modularize your code (functions)

* Identify testable components to turn into functions
   * Chunk of calculations 
   * Body of for loop
   * Pass all inputs as arguments, return outputs
   * Look for similar (repetitive) parts of code (mode argument)
* Anything you're not sure about, tricky
   * That you might try separately, interactively, in a debugger or REPL
* Call these functions from both tests and "real" code


### Think about automation

* Write many tests
* Write one script to call all the tests (more later...)

### Validate assumptions

* If some function works for a range of values, choose one at random each time
* Compose any pair of inverse operations (*g* ◦ *f*, `load` ◦ `save`)
* Validate properties that should hold
* Use a less efficient, equivalent calculation to check
* Identify and write down (test) your assumptions



## Boring Terminology


### Unit tests

* Tests of some small "unit" of code, usually a function
* Often augments documentation

#### "Doc" test

* A unit test embedded in documentation


### Smoke tests

* A very simple test to make sure things are basically working
* Often run before other tests to determine whether to proceed


### Integration, System, Acceptance tests

* Tests of the interaction between components or the entire system in some real-world-like scenario


### Regression tests

* *regression*: when something stops working due to a (theoretically unrelated) change
* Often added when fixing bugs, to avoid re-introducing them later

### Performance tests

* Automatically timing some code to make sure it still runs fast enough


### Test-driven development

* The practice of writing tests before code
* For example, a unit test for a function defines the API
* May be written as a "TODO" for things to be implemented

* Coding with the end in mind
* Determine requirements for "working" code
* Write tests that assert these requirements based on an interface
   * Requirement: `f(2)` always returns 4
   ```
   assert f(2) == 4
   ```
* Implement functions
* Check function passes the test


### Continuous Integration (CI)

* Automatically running tests for each change/commit



# Group Activity
- Discuss your particular use cases and questions with your team 
- Discuss aspects of your code which you don't know how to test
- Come up with more test ideas



# Digital Lab Techniques
## More on Intergration Tests
Nick Carriero (SCC)



## Example tests

Trivial smoke test
```sh
#!/bin/sh
true
```


Another less trivial smoke test 
```
import mypackage

return True
```


Autocorrelation of iid random data should be 0
```python
data = np.random.randn(1000)
maxlag = 5
# calculate autocovariance:
C = cnmf.pre_processing.axcov(data, maxlag)

numpy.testing.assert_allclose(C, numpy.concatenate(
    (numpy.zeros(maxlag), numpy.array([1]), numpy.zeros(maxlag))))
```
[source](https://github.com/flatironinstitute/CaImAn/blob/master/caiman/tests/test_pre_processing.py)


Compare fast and slow search for test membership
```c++
std::vector<int> ints = {{ 1,3,6,7,9,10,12,14 }};

for(int i = 0; i <= 30; ++i)
    {
    auto res = detail::binaryFind(ints,i);
    //Do linear search for i to check
    bool found = false;
    for(const auto& el : ints)
        if(el == i)
            {
            found = true;
            break;
            }
    if(found)
        {
        CHECK(res);
        CHECK(i == *res);
        }
    else CHECK(!res);
    }
```
[source](https://github.com/ITensor/ITensor/blob/v2/unittest/algorithm_test.cc#L31)

This could also generate random input


Check some expected property of a function for various inputs
```python
"""
Tests that 0.25 <= xi_max(Q) <= 0.5, whatever Q
"""
Q_range = np.arange(1, 21, dtype=int)
for Q in Q_range:
    xi_max = compute_xi_max(Q)
    assert xi_max <= 0.5
    assert xi_max >= 0.25
```
[source](https://github.com/kymatio/kymatio/blob/master/kymatio/scattering1d/tests/test_filters.py#L195)


Compare min and max value fast for different structures (with random input)
```c++
netket::Binning<double> binning(16);
std::vector<double> vals;
for (int i = 0; i < N; i++) {
    double x = GaussianWalk(x, gen, 2);
    binning << x;
    vals.push_back(x);
}

double mean = binning.Mean();
double minval = *std::min_element(vals.begin(), vals.end());
double maxval = *std::max_element(vals.begin(), vals.end());
REQUIRE(mean >= minval);
REQUIRE(mean <= maxval);
```
[source](https://github.com/netket/netket/blob/master/Test/Stats/unit-stats.cc#L59)


Check known output for expected possible DNA mutations on fixed input
```python
observed = in_silico_mutagenesis_sequences("ATCCG")
expected = [
    (0, 'C'), (0, 'G'), (0, 'T'),
    (1, 'A'), (1, 'C'), (1, 'G'),
    (2, 'A'), (2, 'G'), (2, 'T'),
    (3, 'A'), (3, 'G'), (3, 'T'),
    (4, 'A'), (4, 'C'), (4, 'T')]

self.assertListEqual(observed, expected)
```
[source](https://github.com/FunctionLab/selene/blob/master/selene_sdk/predict/tests/test_model_predict.py#L12)


Ensure out-of-bounds indexing generates an error
```c++
array<long, 2> A(2, 3);

EXPECT_THROW(A(0, 3), key_error);
EXPECT_THROW(A(range(0, 4), 2), key_error);
EXPECT_THROW(A(range(10, 14), 2), key_error);
EXPECT_THROW(A(range(), 5), key_error);
EXPECT_THROW(A(0, 3), key_error);
```
[source](https://github.com/TRIQS/triqs/blob/2.1.x/test/triqs/arrays/bound_check.cpp)


Check basic math on tensors
```python
m0 = matrix([[1,0],[0,2]])
m1 = matrix([[0,1],[2,0]])
A = BlockMatrix(['up','dn'],[m0,m1])

A2 = A + A
assert (A2["up"] == 2*m0).all() and (A2["dn"] == 2*m1).all(), "Addition failed"
A2 += A
assert (A2["up"] == 3*m0).all() and (A2["dn"] == 3*m1).all(), "In-place addition failed"
A2 = A - A
assert (A2["up"] == 0*m0).all() and (A2["dn"] == 0*m1).all(), "Subtraction failed"
```
[source](https://github.com/TRIQS/triqs/blob/2.1.x/test/pytriqs/base/block_matrix.py#L23)


Check marshalling (sending/receiving) preserves data (generated random inputs)
```haskell
Test.quickCheckResult $ \s -> Test.ioProperty $ do
  [(Just s')] <- [pgSQL|SELECT ${s}::varchar|]
  return (s' Test.=== s)
```
[source](https://github.com/dylex/postgresql-typed/blob/master/test/Main.hs#L104)


# Group Activity
- Discuss your particular use cases and questions with your team 
- Discuss aspects of your code which you don't know how to test
- Come up with more test ideas



## How do you implement tests?

### Testing libraries

* functions, utilities for writing tests
* checks for (in)equality
* log messages, produce errors
* random input generation, looping


### Testing frameworks ("harness", "runner")

* organize, describe tests
* way to run tests (or some subset thereof)
* capture output, aggregate results

Many tools include both, and languages can include builtin features or standard libraries



## Python

### [unittest](https://docs.python.org/3/library/unittest.html)

* Standard library

```python
class TestStringMethods(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')

    def test_isupper(self):
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):
            s.split(2)

if __name__ == '__main__':
    unittest.main()
```


#### Test discovery

1. Look for all `test_*.py` and `*_test.py` files recursively
1. Run all functions named `test*`

### [nose2](https://docs.nose2.io/en/latest/)

Alternative way to invoke unittest tests with more features.

### [Pytest](https://docs.pytest.org/en/latest/index.html)

```python
def test_foo():
    assert 0.1 + 0.2 == pytest.approx(0.3)
```


### [doctest](https://docs.python.org/3/library/doctest.html)

* Standard library

```python
"""
The example module supplies one function, factorial().  For example,

>>> factorial(5)
120
"""

def factorial(n):
    """Return the factorial of n, an exact integer >= 0.

    >>> [factorial(n) for n in range(6)]
    [1, 1, 2, 6, 24, 120]
    >>> factorial(30)
    265252859812191058636308480000000
    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n must be >= 0
    """

    import math
    if not n >= 0:
        raise ValueError("n must be >= 0")
    result = 1
    factor = 2
    while factor <= n:
        result *= factor
        factor += 1
    return result
```



## C++

### [CppUnit](http://cppunit.sourceforge.net/doc/cvs/cppunit_cookbook.html)

```c++
class ComplexNumberTest : public CppUnit::TestCase { 
public: 
  ComplexNumberTest( std::string name ) : CppUnit::TestCase( name ) {}
  
  void runTest() {
    CPPUNIT_ASSERT( Complex (10, 1) == Complex (10, 1) );
    CPPUNIT_ASSERT( !(Complex (1, 1) == Complex (2, 2)) );
  }
};
```


### [CxxTest](http://cxxtest.com/guide.html)

```c++
class CalculatorTest : public CxxTest::TestSuite {

public: 
   setUp() {
       c_ = CalculatorOP( new Calculator() );       
   }

   tearDown() {
       c_ = 0;
   }

   void test_add() {
       TS_ASSERT( c_->add( 1, 2 ) == 3 );
       TS_ASSERT_EQUALS( c_->add( 0, 0 ), 0 ); 
       TS_ASSERT_DELTA( c_->add( 1, 2 ), 3, 0.01 ); 
   }

private:
   CalculatorOP c_;
};
```



## [Julia](https://docs.julialang.org/en/latest/stdlib/Test/)

* Standard library

```julia
@testset "My simple tests" begin
    @test true
    @test [1, 2] + [2, 1] == [3, 3]
    @test π ≈ 3.14 atol=0.01

    θ = 20*rand()
    @test sin(-θ) ≈ -sin(θ)
end;
```



## [R](http://r-pkgs.had.co.nz/tests.html)

### [testthat](https://www.rdocumentation.org/packages/testthat)

```R
test_that("str_length is number of characters", {
  expect_equal(str_length("a"), 1)
  expect_equal(str_length("ab"), 2)
  expect_equal(str_length("abc"), 3)
})
```



## CI: Test Automation

Integration with github and other repositories to automatically run tests

### [Travis-CI](https://travis-ci.org/)

`.travis.yml`:
```yaml
language: python
```

### [Jenkins](https://jenkins.flatironinstitute.org/)



## #sciware

Please post questions, useful resources, and other topics of discussion to the #sciware Slack channel

[https://github.com/flatironinstitute/learn-sciware-dev](https://github.com/flatironinstitute/learn-sciware-dev)


## Coming Attractions 

### June 20 - Testing for packages?

### July 18 or 25 - TBD. Requests?

