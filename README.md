# Codegen

A research project looking into modern LLMs ability to generate bioinformatics specific code.

# Project Structure

The repository comprises of 4 main parts: evaluation, inference, parsing, and data. We use the data folder to store results between each step in the operations. Ensure that each step is run from the root directory, i.e. `python3 parsing/parse.py` and not `cd parsing && python3 parse.py` in order to avoid issues with relative paths.

Each section is described below.

## Parsing

The parsing section contains the code used to analyze, parse, and extract functions from the repositories. 

# Overview

## json_files

Contains the data parsed/generated by scripts

* htsjdk_functions.json - file containing all the parsed functions from the `htsjdk` repository
* pymbar_file_imports.json - file containing all the cross-file imports in the `pymbar` repository
* pymbar_functions.json - file containing all the parsed functions from the `pmybar` repository
* pymbar_functions_codex.json - file containing all the parsed functions along with CODEX outputs from the `pymbar` repository

## parsing

Contains the scripts required to parse Python repositories

* parse.py - file that is currently hardcoded to parse the `pymbar` repository
* generate_prompts.py - file that generates the prompts for `pymbar` functions that are used to pass into CODEX

## FunctionExtractor

Contains the Java code used to parse Java repositories. It is a maven project that depends on the JavaParser and JSONObject libraries

The source directory is in `src/main/java/com/lilbillbiscuit/`
* Main2.java - the entrypoint to the program. Will parse through all repositories in a given folder
* BundleStructures.java - contains the data structures used to store the parsed data
* DirExplorer.java - contains code that assists with traversing directories
* Testing.java and TestingHTSOnly.java - contains testing code, currently hardcoded for htsjdk.

## PassAtKRunner

Contains all of the code and scripts used for the testing framework

* CentralServer/ - contains the code for the AWS Lambda queue that coordinates all the test workers
* DockerServers/ - contains all the code and scripts used for setting up environments and programs for each of the test workers
* ImageBuilder/ - contains code for building test environments for each repository, also contains scripts to set up environments to build such repository metadata
* passanalysis/ - contains some basic code that runs some analysis on the results
* start_test.py - script that starts the testing framework inside the docker container

### Setting up the evaluation framework
The evaluation framework utilizes a combination of Lambda and SQS to assign tasks across multiple workers. The workers are Docker containers that are spun up on demand. The Docker containers are spun up repeatedly and query the Lambda function for tasks to perform. DynamoDB is used to store the results.

We will publish a version that runs completely locally in the future.

1. Create an AWS account
2. Create an AWS Lambda function with the following parameters:
  - Memory: 3072MB
  - Timeout: 1 minute
  - Enable Function URL
  - Python 3.9
  - Execution Role: Add the roles AmazonDynamoDBFullAccess, AmazonS3FullAccess, AmazonSQSFullAccess (although permission boundaries can be limited)
3. Copy the contents of `CentralServer/lambda_queue_handler.py` into the Lambda function
4. Click Deploy

5. Create an EC2 Launch Template with the following settings:
6. Create two SQS queues, 

Load test cases with the following command:


## RepositoryAnalysis

Contains a Jupyter notebook to analyze the list of 1740 Github repositories as found in the article

## datasets

Contains the functions that make up the various parts of the benchmark.

* `java_hidden.json`: A JSON of the 1243 Java functions that make up the Java part of the "hidden" dataset of the benchmark
* `java_public.json`: A JSON of the 50 Java functions that make up the Java part of the "public" dataset of the benchmark
* `java_simlar.json`: A JSON of the 50 Java functions that make up the Java part of the "similar" dataset of the benchmark
* `python_hidden.json`: A JSON of the 1026 Python functions that make up the Python part of the "hidden" dataset of the benchmark
* `python_public.json`: A JSON of the 50 Python functions that make up the Python part of the "public" dataset of the benchmark
* `python_simlar.json`: A JSON of the 50 Python functions that make up the Python part of the "similar" dataset of the benchmark
* `rosalind.json`: A JSON of the 253 Rosalind functions that make up the Rosalind part of the "public" dataset of the benchmark
