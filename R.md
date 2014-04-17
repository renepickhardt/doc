# Digging into R

Motivation: I was advised to use R for statistical analysis of time series data.

### Installation

Under Ubuntu Linux it was very simple to install R using `apt-get`
cf. [blog](http://www.personal.psu.edu/mar36/blogs/the_ubuntu_r_blog/installing-r.html).
Soon I decided to install the GUI [RStudio](http://www.rstudio.com/),
whose installation was also unproblematic.

### First impression

I am used to programming in python and java so I started with reading
[1]. The first thing to notice is that, the most important feature of
any IDE, tab-completion, is suported by the R shell. However, the
function oriented language design

	a <- c(1,2,3)    # creates a `vector` 
	length(a)        # returns 3
	
makes interactive exploration of libraries very-hard. How do I know
which of the hundreds of available function is applicable to a?

On the plus side, I love that variable assignments have proper syntax.
As a mathematician using `=` as an assigment operator has always felt
utterly wrong. This sentiment is shared e.g. by
[D. Knuth](http://en.wikipedia.org/wiki/Donald_Knuth), who also uses
`<-` in his books.

A thing I found quiet amusing about R is that there seem to be equaly
many articles describing flaws in the R language [2,3], than
introductions and guides to the language. Of course I started reading
flaws first.

One of the mayor differences of R to other programming languages is
the absence of scalar data types. __Everything is a vector__.

	typeof(1)        # > [1] "double", i.e. double vector
	typeof(c(1))     # > [1] "double", i.e. double vector
	1 == c(1)        # > TRUE

Another curiosity is the (ab)-use of the vector generation operator `:`

	1:5             # > [1] 1 2 3 4 5, a list with 5 elements

So lists start with `1` and the end is inclusive. Seems good at first,
but then the basic lentgh relation becomes a bit wired:

	length(a:b) == b - a + 1

So in order to produce an empty list one has to use `a:a-1`, right? No:

	1:1             # > [1] 1
	1:0             # > [1] 1 0
	1:-1            # > [1] 1 0 -1

When the sencond index is less than the first one, then the iteration
goes backwards. So the length relation fails in edge cases. Lets make
a test, and calculate:

	length(0:a) for a = -5 .. 5

How do you do that using R? My first idea is to generate a list of
lists, which contains `0:a` for varying `a`. Is this possible in R?
Can we have vectors of vectors?

	c(c(1,2,3), c(4,5,6))  # > [1] 1 2 3 4 5 6

No. Nested vectors are flattend out! Ok, next try, lets use a function:

	lengthOfArray <- function(a) { length(0:a) }
	lengthOfArray(0)
	# > [0] 1

Good. Now lets map it to a vector:

	sapply(-2:2, lenthOfArray)   # > [1] 3 2 1 2 3

Ok. This is not as bad as I would had thought. At least the length
function has a simple regularity:

	length(a:b) == abs(b - a) + 1

One problem with the approach is that `sapply` stands for simplified
apply, and performes some simplifications which might depend on the
input data, and can lead to subtle bugs.

### Under the hood

We are following [6, Chap. 13].

R is an interpreted language using a __read__, __parse__, __evaluate__
loop to evaluate expressions read from stdin.  In R an expression is
parsed into an __object__, which is then passed to the `eval()`
function. The eval function returns the result of the evaluation as an
object with is then printed to the output stream.

Evaluation and parsing can be separated using the `quote` and `eval`
functions:

	a <- 0
	X =	quote(a <- 3)        # R object representation of expression
	a                        # > [1] 0
	eval(X)                  # Evaluate object
	a                        # > [1] 3
	
So everyhing in R comes down to evalating __objects__.  But what are
objects?

Ideally I would like to have some lower level, detail about
implementations of objects. Howver, lets look at some examples first:

* Costants

  Constant Objects := "Objects that evaluate to themselves."
	
  This means a statement xxx represents an constant object iff

		eval(quote(xxx)) == quote(xxx)

  Examples:

    	eval(quote(3)) == quote(3)          # TRUE => constant
	    eval(quote("hi")) == quote("hi")    # TRUE => constant
    	eval(quote(a)) == quote(a)          # FALSE
    	eval(quote(-1)) == quote(-1)        # FALSE -> function call

  The statement `-1` represents the function call object `-(1)`.

Question: Can we turn the rule for constants into a function?

	isConst <- function(x) { eval(quote(x)) == x }
	isConst(3) # > [1] FALSE

So the naive approach does not work. How can we debug this?


### References
1. [R programming for programmers](http://www.johndcook.com/R_language_for_programmers.html)
2. [Design flaws in R](http://radfordneal.wordpress.com/2008/08/06/design-flaws-in-r-1-reversing-sequences/)
3. Patrick Burns - The R Inferno
4. [List of Ressources](http://www.ats.ucla.edu/stat/r/)
5. [Online course for beginners.](http://tryr.codeschool.com/)
6. John Chambers - Software for Data Analysis
