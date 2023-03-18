Title: cmput361 Winter 2023 Homework #1
Date: 2023-01-15
Category: Teaching/cmput361
Subcategory: cmput361


## Instructions:

* You can write your programs in any language available in the Computing Science instructional lab machines, however you can only use standard libraries in these languages.
* If you want to use a third-party or a non-standard library, ask the instructor for permission first. You will not be allowed to use any library that implements the work that you are asked to do in the assignment. Also, even with the permission of the instructor, it is your responsibility to make sure that the libraries that you need are installed on the lab machines, or can be installed without superuser powers, so that your code can be graded by the TAs. 
* This programming assignment is meant to be completed individually or in pairs, under the **Consultation** model of collaboration as per the Computing Science [Department Course Policies](https://www.ualberta.ca/computing-science/links-and-resources/policy-information/department-course-policies).
* You must not upload *binary* files (e.g. SQLite databases) or large text files of any kind to your GitHub repository. 
* All of your code and/or data _must be on the GitHub repository created by following the instructions on eClass_. 
* That repository already has the folder structure for the assignment and the configuration file for automated testing. **DO NOT MODIFY** the folder structure.
* Remember to _submit the URL of your repository through eClass_ **before the deadline**. 


# Learning Objectives

This assignment is intended for you to become familiar with:

1. Building an inverted index to enable fast document retrieval.
+ Answering Boolean queries on zoned queries.

### Required Reading

The background material for this assignment is in Chapters 1 and 2, and in Section 6.1 of the textbook.


## Overview

Your goal is to design, implement, and document programs to build inverted indexes allowing the efficient processing of Boolean queries over a collection of JSON documents with multiple fields. Recall from Section 6.1 of the textbook that separate fields are called zones. Following the textbook notation, we will refer to JSON fields as zones below.

Your programs must work with any corpus file in JSON, formatted as follows:

```
[
  { "doc_id" : "id_1", "zone_1" : "text 1_1", ... , "zone_n" : "text 1_n" },
  ...
  { "doc_id" : "id_m", "zone_1" : "text 1_m", ... , "zone_n" : "text n_m" }
]
```

In words: the JSON file should contain an array of objects and each object corresponds to a document. 

Each document has one required field called `doc_id`, which must be an integer that uniquely identifies the document in the corpus. Your program must check that no two documents have the same id, and report an error if that is the case. **Note:** the `doc_id` field in the JSON file should not be considered a zone.

Besides the `doc_id` field, each document has at least one zone with some text associated with it. Each zone name must be a a single token (i.e., no spaces or punctuation are allowed in a zone name), while the text associated with a zone can be any text in English. Different documents may have different zones, and some zones may not be present in every document.

##### Example Corpora

Your GitHub repository has two sample corpora under the `data/` folder: `dr_seuss_lines.json`, with 5 documents, each corresponding to a line in a Dr. Seuss book, and `movie_plots.json`, with approximately 1700 summaries of movie plots. 

Here is one document from the Dr. Seuss corpus:

```json
{
	"doc_id" : "0",
	"line" : "Unless someone like you cares a whole awful lot, nothing is going to get better. It’s not.",
	"book" : "The Lorax"
}
```

The document above has two zones: `line` and `book`. Below you will see how we can use the zones in the queries.

## Tasks

**Note:** If you use Python 3 for this assignment, note that you can install libraries using [Virtual Environments](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/). If you do so, write down the step-by-step instructions in your `README.md` file. 


### Index Creation

**4 marks: 2 implementation, 1 documentation, 1 instructions**

Write a program in folder `src/` of the repository that takes two [command line arguments](https://en.wikipedia.org/wiki/Command-line_argument_parsing), in this order: the first is a path to the JSON file with the corpus, and the second must be a path to the directory where the index files will be stored. The program must create separate inverted indexes for each of the zones of an input corpus file in JSON.  **Note:** you are allowed to use a library to load JSON data (e.g., Python's `json` library) for this task.

* Your indexes should support all boolean queries.
+ Each index must be persistent and written to a TSV (tab-separated values) file with 3 columns, in this order (from left to right): `token`, `DF`, `postings`, where the `DF` is the document frequency of the token.
+ Each index file must be sorted by token, and each posting list must be sorted by document id.
+ All documents in the corpus must be indexed, unless there are errors in the corpus.
+ Your program should print error messages as necessary; if an error occurs while processing a document, your program should stop gracefully (i.e., catch and handle exceptions), and print an error message. No index files should be written if there is an error.


### Querying 

**6 marks: 4 implementation, 1 documentation, 1 instructions.**

Write a program in folder `src/` that takes two parameters, in this order: a path to a directory containing all zone indexes and a single string with a complete Boolean query. Your program should print to STDOUT the ids of the documents that satisfy the query. **Note:** your program should print one id per line and the document ids must be sorted in ascending order.

To get full marks, your program must handle arbitrary Boolean queries that use `AND`, `OR`, `NOT`, and `( )`. We will test your solution using queries with long queries involving parenthesis and all operators. Each token in the query should be prefixed by a zone name. For example `line:cares` matches the token `cares` inside the `line` zone of the document. 

Your program must print to `STDOUT` (or `STDERRR`) a clear error message if the query is not properly formed, including if a token appears in the query without a corresponding zone. 

Here are examples of queries and documents in the Dr. Seuss corpus that are retrieved by them:

| Query | ids |
|-------|-----|
|`book:Lorax` | `0`|
|`line:you` | `0, 2, 4`|
|`book:Lorax AND line:lot AND line:better` | `0`|
|`book:Lorax` OR `line:Lorax`| `0` |
|`line:you AND book:you` | `2, 4`|
|`line:you AND book:you AND NOT book:Birthday` | `4`|
|`book:Lorax OR (book:you AND line:you)` | `0, 2, 4`|


**Note:** You are allowed to use a library for parsing boolean expressions (e.g., Python's [boolean.py](https://booleanpy.readthedocs.io/en/latest/)).

## Documentation Requirements

You must include the following in the `README.md` file in the repository:

1. How to install any libraries that are needed and for which you have the approval of the instructor. Make sure **you test your own instructions on a lab machine**.
+ Clear instructions on how to use your programs, including at least one example showing all parameters.
+ A discussion of which errors your programs detect and how these errors are handled.


## Suggestions:

* Use Python 3.
* Start by figuring out how the `bash` shell (which is the one we will use for grading) handles command line arguments, and how to pass a boolean query as an argument and how to parse it into a format you can handle.
* Design and modularize your code well enough so that different team members can work on different parts of the code.
* Use version control to your advantage: commit often and with small changes instead of in big chunks.
* Design your index builder to be modular: break it down into high level functions and document your code with comments.
* Remember that different files will have different zones. We will test your code with corpora having more than just two zones.
* Test your code extensively by manually inspecting the positions in which some words appear in the corpus and always checking they are indexed correctly. 
* Start small: work on the Dr. Seuss corpus first (or on an even smaller one that you create for this), and **build all indexes by hand**, and use your answer to test your program.


## Normalization?

Decide early on what form of tokenization and normalization should be applied and justify your choice in the `README.md` file in your repository. If you are using Python, a good place to start is the [NLTK library](http://www.nltk.org/).

# Grading

Each aspect of your submission will be marked according to the following scheme. The code will be tested on both valid and invalid input from the data set provided and another used for our own testing. We will be performing all marking on **standard CS instructional lab machines** so make sure all code runs on them exactly as in the instructions you provided to avoid problems.


| Percentage | Implementation | Documentation | Instructions |
|------------|---------------| --------------| -------------|
| 100 | The code meets **all requirements** and behaves as expected on correct and incorrect input, accepting the command line arguments as specified; (b) the code uses suitable data structures and the best possible algorithms; (c) error messages are clear; (d) the program does not crash (e.g., through an uncaught exception) with unexpected or incorrect input (e.g., missing or invalid parameters). | The code is **well documented**: (a) all functions have meaningful comments; (b) all variables and functions have meaningful names; (c) the `README.md` file explains the data structures and algorithms, covers all assumptions and the error handling strategy. | The instructions for executing the code are **complete** and clear: the TA is able to compile and/or execute the code following exactly the instructions provided, on a lab machine.|
| 75 | The code correctly implements most, but not all, of the requirements using suitable data structures and the best possible algorithms. | The documentation covers only all key functions in the code. | The instructions are insufficient, but all missing steps are obvious. |
| 50 | The code correctly implements some of the features **OR** the code correctly implements the functionality using inappropriate data structures and/or algorithms. | The documentation covers only some of the key functions. | The instructions are incomplete and the TA has to guess how to compile and run the code.|
| 25 | The code correctly implements only one or two of the requirements. | Minimal documentation that does not explain the ideas behind the design decisions. | TA needs to contact the student(s) to clarify execution instructions. |
| 0 | The code does not work on any test cases; **OR** the code has syntax errors; **OR** the code requires libraries that cannot be installed in the lab machines or libraries for which you did not obtain written approval of the instructor.| Missing. |Missing. |

