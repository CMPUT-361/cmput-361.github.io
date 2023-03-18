Title: cmput361 Winter 2021 Homework #3
Date: 2021-03-26
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

This assignment is intended for you to implement, evaluate, and compare text classifiers based on the "bag of words" and the vector models. The background material for this assignment is in Chapters 13 and 14 of the textbook.


# Overview

Tasks:
* Implement the following classifiers: Naive Bayes, Rocchio, and *k*NN (20% each).
* Experiment with different values of *k*, to find the best value overall (20%).
* Compare Naive Bayes, Rocchio, and the best performing *k*NN using micro and macro F1.

You are not allowed to use libraries that already implement any part of the tasks above.

All of your programs **must** accept all required parameters via [command line arguments](https://en.wikipedia.org/wiki/Command-line_argument_parsing).

### Train and test sets

In the `data/` folder of your repository there are two files: `train.json`, which should be used for building the model used by your classifier, and `test.json`, which should be used for evaluating the classifiers. You are **strongly** encouraged to create smaller train and test files, by hand, to debug your program. Hopefully you have seen the usefulness of the "Dr Seuss" corpus in your previous assignments.

Each entry in those JSON files has two parts as in the example below:

```JSON
 {
 	"category": "class_name",
 	"text": "text in one news story"
 }
```

The TAs will also use a "held-out" set that will not be given to students for their own independent evaluation. Ideally, the F1 on the held-out set should be very close to that on the test set given to you. If your model is trained correctly, this is very likely to happen.

Here are the number of documents in each class:

| class | train | test | held|
|------|------|----|----|
|business | 460 | 26 | 24 |
|entertainment | 348 | 20 | 18 |
|politics | 376 | 21 | 20 |
|sport | 460 | 26 | 25 |
|tech | 361 | 21 | 19 |

## Tasks

### Naive Bayes Classification (20 marks)

**Task 1 (10 marks)**. Write a program <span style="font-family: monospace; color:blue">nbc/nbc_train.py</span> that takes **two command line arguments** in this order: (1) path to a JSON file with training data; and (2) path to a file where the model will be written. Here is one example of how to call your program (assuming you use Python 3):

```
  % python3 ./nbc/nbc_train.py ./data/train.json ./full_bbc_model.tsv
```

If the path provided for writing the model refers to an existing file, we strongly suggest that your program asks the user for confirmation before overwriting the file. 

**Output.** The model must be written to a TSV (tab-separated values) file with two kinds of lines for representing priors and likelihoods (conditional probabilities). The first column in each line should be a single token: either `prior` or `likelihood`. If the line is for a prior, the next columns should be the class name and its prior. If the line is for a likelihood, the next three columns should be the class, the term, and the probability.

All probabilities must be estimated using add-one smoothing.

**Task 2 (10 marks)**. Write a program <span style="font-family: monospace; color:blue">nbc/nbc_prediction.py</span> that should take **two command line arguments** in this order: (1) a path to a model created by your previous program; and (2) a path to a JSON test file like the one provided. Here is an example of how to call your program (assuming you use Python 3):

```
  % python3 ./nbc/nbc_prediction.py ./full_bbc_model.tsv ./data/test.json 
```

**Output.** See below.

Your program must print to STDOUT:

* One line for each class in the test file with the true positive, false positive, false negative and true negative counts, followed by the precision, recall and F1 score for that class.
* One line with the micro-averaged F1 for the entire test.
* One line with the macro-averaged F1 for the entire test.


### Rocchio Classification (20 marks)

### kNN Classification (20 marks)

**Task 5 (2 marks)**. Write a program <span style="font-family: monospace; color:blue">knn/knn_create_model.py</span> that should take **two command line arguments** in this order: (1) path to a JSON file with training data; and (2) path to a file where the document vectors in the training data will be written. Here is one example of how to call your program (assuming you use Python 3):

```
  % python3 ./knn/knn_create_model.py ./data/train.json ./bbc_model.tsv
```

If the path provided for writing the model refers to an existing file, we strongly suggest that your program asks the user for confirmation before overwriting the file. Also, your program should print error messages if called with a wrong number of arguments or if the path to the training data is invalid.

All documents in the train and test sets should be represented by vectors whose the weights are calculated using the `ltn` weighting scheme. To calculate IDF, assume the corpus to be the training set only.

The output file should be a TSV (tab-separated values) file with the IDF scores of terms in the training corpus (which are needed to compute the vectors of the test document) and the vectors of the training documents. There should be two kinds of lines in this file: `idf` and `vector`, and the first column should indicate that (i.e., should be one of those values).

In an `idf` line the next two columns should be the term and the actual IDF of that term computed from the training corpus using the `t` weighting scheme. In a `vector` line, the second column should be the class of the training document, and the third column should be a single string representing the vector of the document (which is a training example of class). You can (and should) omit zero-weight entries in your vectors. Your output file should have as many `vector` lines as there are documents in the train set.


**Task 6 (2 marks)**. Write a program <span style="font-family: monospace; color:blue">knn/knn_prediction.py</span> that should take **three command line arguments** in this order: (1) path to a TSV file with vectors computed as in the previous step; (2) a value `k`; and (3) a path to a JSON test file like the one provided. Here is one example of how to call your program (assuming you use Python 3):

```
  % python3 ./knn/knn_prediction.py ./bbc_doc_vectors.tsv 11 ./data/test.json
```
Your kNN classifier must implement the algorithm explained in the textbook and using the **Euclidean distance**.


**Classifier Output:** All `_prediction` programs (NBC, Rocchio, and *kNN) must produce the same output as follows:

Your program must print to SDOUT:

* One line for each class in the test file with the true positive, false positive, false negative and true negative counts, followed by the precision, recall and F1 score for that class.
* One line with the micro-averaged F1 for the entire test.
* One line with the macro-averaged F1 for the entire test.



**Task 7 (1 mark)**. Use your programs above to study the effect of the parameter `k`. Using 1, 5, 11 and 23 as values of `k`, compute the per-class and the aggregate (micro and macro) F1 scores using the vectors of all documents in the train set and all documents in the provided test set.

Write these values in the `README.md` file in your repository.

### WARNING about computation time

To complete this task you will have to perform about **228 thousand distance calculations for each of the 4 values of `k`, for a total of close to 1 million calculations**. 

You should not underestimate how long this will take, and you should write clean and efficient code. You are strongly encouraged to use a heap data structure to keep the `k` vectors closest to the test document.

For reference, if your program takes more than 5 minutes to run on the provided dataset with a single value of `k`, you should be contacting a TA to discuss how to improve your code. **See the notes on the Grading scheme about execution time.**


# Grading

| Percentage | Correctness (60%) | Documentation (20%) | Instructions (20%)|
|------------|---------------| --------------| -------------|
| 100 | The code meets **all requirements** and behaves as expected on correct and incorrect input, accepting the command line arguments as specified; (b) the code uses suitable data structures and the best possible algorithms; (c) error messages are clear; (d) the program does not crash (e.g., through an uncaught exception) with unexpected or incorrect input (e.g., missing or invalid parameters). | The code is **well documented**: (a) all functions have meaningful comments; (b) all variables and functions have meaningful names; (c) the `README.md` file explains the data structures and algorithms, covers all assumptions and the error handling strategy. | The instructions for executing the code are **complete** and clear: the TA is able to compile and/or execute the code following exactly the instructions provided, on a lab machine.|
| 75 | The code correctly implements most, but not all, of the requirements using suitable data structures and the best possible algorithms. | The documentation covers only all key functions in the code. | The instructions are insufficient, but all missing steps are obvious. |
| 50 | The code correctly implements some of the features **OR** the code correctly implements the functionality using inappropriate data structures and/or algorithms. | The documentation covers only some of the key functions **OR** a video explaining the code is missing. | The instructions are incomplete and the TA has to guess how to compile and run the code.|
| 25 | The code correctly implements only one or two of the requirements. | Minimal documentation that does not explain the ideas behind the design decisions. | TA needs to contact the student(s) to clarify execution instructions. |
| 0 | The program does not accept command line arguments as specified, **OR** the program does not produce the expected answer with the provided inputs after 5 minutes of execution on a lab machine, **OR** the code does not work on any test cases; **OR** the code has syntax errors; **OR** the code requires libraries that cannot be installed in the lab machines or libraries for which you did not obtain written approval of the instructor.| Missing. |Missing. |

