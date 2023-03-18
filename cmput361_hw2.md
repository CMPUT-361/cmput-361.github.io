Title: cmput361 Winter 2023 Homework #2
Date: 2023-02-24
Category: Teaching/cmput361
Subcategory: cmput361


## Instructions:

* You can write your programs in any language available in the Computing Science instructional lab machines, however you can only use standard libraries in these languages.
* If you want to use a third-party or a non-standard library, you must ask the instructor for permission first. You will not be allowed to use any library that implements the work that you are asked to do in the assignment. Also, even with the permission of the instructor, it is your responsibility to make sure that the libraries that you need are installed on the lab machines, or can be installed without superuser powers, so that your code can be graded by the TAs. 
* This programming assignment is meant to be completed individually or in pairs, under the **Consultation** model of collaboration as per the Computing Science [Department Course Policies](https://www.ualberta.ca/computing-science/links-and-resources/policy-information/department-course-policies).
* You must not upload *binary* files (e.g. SQLite databases) or large text files of any kind to your GitHub repository. 
* All of your code and/or data _must be on the GitHub repository created by following the instructions on eClass_. 
* That repository already has the folder structure for the assignment and the configuration file for automated testing. **DO NOT MODIFY** the folder structure.
* Remember to _submit the URL of your repository through eClass_ **before the deadline**. 


# Learning Objectives

This assignment is intended for you to become familiar with:

1. Building an inverted index to enable fast document retrieval.
+ Answering keyword and phrase queries.


## Overview

Your goal is to design, implement, and document programs to build inverted indexes allowing the efficient processing of keyword and phrase queries over a collection of documents given in a JSON file formatted exactly as in the [previous assignment](https://sites.ualberta.ca/~denilson/cmput361-winter-2023-homework-1.html).

Unlike with the previous assignment, zones should be ignored for the purposes of this assignment. To do that, your program must consider the content of all zones as the document content. That is, your program should concatenate the content of all zones in the order in which they appear, adding a blank space between the content of the zones.

Here is what the content of the document with id `0` in the Dr. Seuss corpus would look like:

```
    Unless someone like you cares a whole awful lot, nothing is going to get better. It’s not. The Lorax
```

## Tasks

**Note:** If you use Python 3 for this assignment, note that you can install libraries using [Virtual Environments](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/). If you do so, write down the step-by-step instructions in your `README.md` file. 


### Index Creation

**4 marks: 2 implementation, 1 documentation, 1 instructions**

Write a program in folder `src/` of the repository that takes two [command line arguments](https://en.wikipedia.org/wiki/Command-line_argument_parsing), in this order: the first is a path to the JSON file with the corpus, and the second must be a path to the directory where the index files (again, ignoring zones) will be stored. **Note:** you are allowed to use a library to load JSON data (e.g., Python's `json` library) for this task.

* Your index should support ranked retrieval using keyword and phrase queries.
+ Each index must be persistent and written to a TSV (tab-separated values) file in which the first column is the `token`, and the other columns should contain all the information necessary for answering the queries.
+ The inverted index file must be sorted by token, and each posting list must be sorted by document id.
+ The document index file must be sorted by document id and have all information necessary to compute the score as efficiently as possible.
+ All documents in the corpus must be indexed, unless there are errors in the corpus.
+ Your program should print error messages as necessary; if an error occurs while processing a document, your program should stop, and print an error message. No index files should be written if there is an error.


### Answering Keyword and Phrase Queries

**6 marks: 4 implementation, 1 documentation, 1 instructions.**

Write a program in the `src/` folder that takes three parameters as [command line arguments](https://en.wikipedia.org/wiki/Command-line_argument_parsing), in this order: a path to a directory containing the index, the number `k` of ranked documents to be returned, and a single string with a complete query. 

Your program must print the following to STDOUT, in this order:

1. One line with the number of documents that were considered for the query (see Step 1 below).
+ One line with the number of documents with non-zero scores among those that were considered for the query.
+ Up to `k` lines each with a document id and a non-zero similarity score (one besides the other, with the id first and a tab in between), sorted by decreasing score. 

**Note:** the program can only print less than `k` lines if there are less than `k` documents with non-zero scores with respect to the given query. 

####Kinds of Queries####

**Keyword Queries** contain only query terms separated by spaces (e.g. `Lorax`, `cares better`, etc.). **Phrase Queries** contain phrases delimited by colons (e.g., `:someone like you cares:`). 

To get full marks, your program must handle: (1) keyword queries, (2) phrase queries, and (3) mixed queries with any number of keywords and phrases at the same time.

Examples:

* Keyword query: `lot like you someone`
* Phrase query: `:The Lorax:`
* Mixed query: `:get better: going :like you: lot`

####Scoring####

Queries containing phrases must be done in two steps. 

**Step 1: create a pool of documents.** In the first we collect *all documents containing at least one of the phrases in the query* into a pool. A document that does not contain any of the phrases should not be added to the pool. Of course, documents containing multiple phrases should be added.

For queries without phrases (i.e., only keywords) the pool should be *all documents* in the system.

**Step 2: score documents in the pool.** In the second step we score all the documents in the pool, if any. The scoring should be according to Algorithm 7.1 in the textbook and follow the `apc.btn` "scheme" (see textbook for details). The query vector should contain all terms that appear in the query either as a keyword or inside a phrase. 

It is important that you explain and document well how you implement the pool of documents in your solution. 


## Documentation Requirements

You must include the following in the `README.md` file in the repository:

1. How to install any libraries that are needed and for which you have the approval of the instructor. Make sure **you test your own instructions on a lab machine**.
+ Clear instructions on how to use your programs, including at least one example showing all parameters.
+ A discussion of which errors your programs detect and how these errors are handled.


## Suggestions:

* Use Python 3.
* Start by figuring out how the `bash` shell (which is the one we will use for grading) handles command line arguments, and how to pass a query as an argument and how to parse it into a format you can handle.
* Design and modularize your code well enough so that different team members can work on different parts of the code.
* Use version control to your advantage: commit often and with small changes instead of in big chunks.
* Design your index builder to be modular: break it down into high level functions and document your code with comments.
* Test your code extensively by manually inspecting the positions in which some words appear in the corpus and always checking they are indexed correctly. 
* Start small: work on the Dr. Seuss corpus first (or on an even smaller one that you create for this), and **build all indexes by hand**, and use your answer to test your program.


# Grading

Each aspect of your submission will be marked according to the following scheme. The code will be tested on both valid and invalid input from the data set provided and another used for our own testing. We will be performing all marking on **standard CS instructional lab machines** so make sure all code runs on them exactly as in the instructions you provided to avoid problems.


| Percentage | Correctness (60%) | Documentation (20%) | Instructions (20%)|
|------------|---------------| --------------| -------------|
| 100 | The code meets **all requirements** and behaves as expected on correct and incorrect input, accepting the command line arguments as specified; (b) the code uses suitable data structures and the best possible algorithms; (c) error messages are clear; (d) the program does not crash (e.g., through an uncaught exception) with unexpected or incorrect input (e.g., missing or invalid parameters). | The code is **well documented**: (a) all functions have meaningful comments; (b) all variables and functions have meaningful names; (c) the `README.md` file explains the data structures and algorithms, covers all assumptions and the error handling strategy. | The instructions for executing the code are **complete** and clear: the TA is able to compile and/or execute the code following exactly the instructions provided, on a lab machine.|
| 75 | The code correctly implements most, but not all, of the requirements using suitable data structures and the best possible algorithms. | The documentation covers only all key functions in the code. | The instructions are insufficient, but all missing steps are obvious. |
| 50 | The code correctly implements some of the features **OR** the code correctly implements the functionality using inappropriate data structures and/or algorithms. | The documentation covers only some of the key functions **OR** a video explaining the code is missing. | The instructions are incomplete and the TA has to guess how to compile and run the code.|
| 25 | The code correctly implements only one or two of the requirements. | Minimal documentation that does not explain the ideas behind the design decisions. | TA needs to contact the student(s) to clarify execution instructions. |
| 0 | The code does not work on any test cases; **OR** the code has syntax errors; **OR** the code requires libraries that cannot be installed in the lab machines or libraries for which you did not obtain written approval of the instructor.| Missing. |Missing. |

