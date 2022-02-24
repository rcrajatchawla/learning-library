# Using Oracle Machine Learning Services

## Introduction

 In this lab, you will get a quick tour of basic OML Services features. You will learn how to obtain an authentication token for your user account to get started with OML Services and then use OML Services to explore the APIs, get model information and score with a model. You will also get a chance to use Oracle's proprietary Cognitive Text model.

Estimated Lab Time: -- 30 minutes

### About Oracle Machine Learning Services
OML Services extends OML functionality to support model deployment and model lifecycle management for both in-database OML models and third-party Open Neural Networks Exchange (ONNX) machine learning models via REST APIs. These third-party classification or regression models can be built using tools that support the ONNX format, which includes packages like Scikit-learn and TensorFlow, among several others.

Oracle Machine Learning Services provides REST API endpoints hosted on Oracle Autonomous Database. These endpoints enable the storage of machine learning models along with its metadata, and the creation of scoring endpoints for the model.

### Objectives

*List objectives for this lab using the format below*

In this lab, you will:
* Authenticate your user account with the Autonomous Database to use OML Services.

    Obtain authentication token.
    Refresh authentication token.
    Revoke authentication token.
* Explore APIs. Specifically:

    View the API.
    Get a list of saved models.
    View a specific model by referencing the model ID.
    Get a list of models filtered by model name.
    Get a list of models filtered by version and namespace.
* Get a model scoring endpoint.
* Score data with a model.

    Perform singleton scoring and mini-batch scoring.
    Use Oracle's Cognitive Text functionality to discover keywords and get a summary for a given text string.


### Prerequisites (Optional)

*List the prerequisites for this lab using the format below. Fill in whatever knowledge, accounts, etc. is necessary to complete the lab. Do NOT list each previous lab as a prerequisite.*

This lab assumes you have:
* An Oracle account
* All previous labs successfully completed


*This is the "fold" - below items are collapsed by default*

## Task 1: Concise Step Description

(optional) Step 1 opening paragraph.

1. Sub step 1

	![Image alt text](images/sample1.png)

2. Sub step 2

  ![Image alt text](images/sample1.png)

4. Example with inline navigation icon ![Image alt text](images/sample2.png) click **Navigation**.

5. Example with bold **text**.

   If you add another paragraph, add 3 spaces before the line.

## Task 2: Concise Step Description

1. Sub step 1 - tables sample

  Use tables sparingly:

  | Column 1 | Column 2 | Column 3 |
  | --- | --- | --- |
  | 1 | Some text or a link | More text  |
  | 2 |Some text or a link | More text |
  | 3 | Some text or a link | More text |

2. You can also include bulleted lists - make sure to indent 4 spaces:

    - List item 1
    - List item 2

3. Code examples

    ```
    Adding code examples
  	Indentation is important for the code example to appear inside the step
    Multiple lines of code
  	<copy>Enclose the text you want to copy in <copy></copy>.</copy>
    ```

4. Code examples that include variables

	```
  <copy>ssh -i <ssh-key-file></copy>
  ```

## Learn More

*(optional - include links to docs, white papers, blogs, etc)*

* [URL text 1](http://docs.oracle.com)
* [URL text 2](http://docs.oracle.com)

## Acknowledgements
* **Author** - <Name, Title, Group>
* **Contributors** -  <Name, Group> -- optional
* **Last Updated By/Date** - <Name, Month Year>
