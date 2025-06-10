# I. Installing the Capstone Rag Application
This brief guide is for installing the `capstone_rag.py` application. 
This process will also install the `rag_test_driver.py` application.
These applications are run from a linux command-line (or similar).

## Prerequisites for installing and running the application

A. `git`: Install git on your server if it is not already installed. 
On an AWS linux instance you might use the following:

`sudo yum install git`

B.`python 3.9 or greater`: Check that you are have Python 3.9 or higher installed and ready for use. 
This is required for the application. Use a command such as the following to determine this:

`python3 --version`

C.`aws`: The `capstone_rag` application will call Amazon Bedrock using `boto3`. 
The code needs to run in an environment where `aws boto3` is enabled.
If this is not enabled, the method of doing this will vary on your test environment.
A common approach to command line use is to install `aws tools` and run `aws configure`. 
The following url provides guidance on this process: 
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html#getting-started-install-instructions

D. The AWS account that you use has to be configured to allow `invoke` API calls to Amazon Bedrock.
Additionally, the Amazon Titan Bedrock models have to be configured for use. 
The Capstone project will use all three Amazon Titan LLMs and an Amazon Titan embeddings model.

## Installing the application code
1. Choose/create the directory where you want to run this application and other Capstone apps.
Then clone the `elvtr-solution-architecture` GitHub repository to that folder.

`git clone https://github.com/toby-fotherby/elvtr-solution-architecture.git`

`cd elvtr-solution-architecture/Class-15/Capstone-Assignment-III/`

2. Setup up a new Python virtual environment for the application.
Activate the new environment
Install the Python modules need for the application

`python3 -m venv venv`

`source venv/bin/activate`

`pip install -r requirements.txt`

3. System test the installation, by running the application on the command line. 
The first run will exercise much of the code, and if the configuration of the AWS account is not sufficient,
as noted in the pre-requisites above, it will fail. Common issues are (i) AWS account not configured, 
(ii) Amazon Bedrock invoke model not configured, (iii) Amazon Titan Bedrock models not enabled for use. 

`python capstone_rag.py`

When this works correctly, on the first run it will output some logging information.
I will create and cache a FAISS vector datastore. 
This datastore will be used in subsequent running of the code, unless the related files in `llm_faiss_index` are deleted.
Run the rag application again. The second time, it will use the cached datastore and will have less latency.

4. System test the installation of the `rag_test_driver` application. 
This is standard Python code and will call the `capstone_rag` application.
First run the application with no arguments. This will show you its expected parameters.

`python rag_test_driver.py`

Then run it with the following command line options. 
This will test the three Amazon Titan LLMs that should be configured.

`python rag_test_driver.py llm_option_one prompt_option_one "which car expenses can be tax deductable?"`
`python rag_test_driver.py llm_option_two prompt_option_two "does everyone have to pay taxes?"`
`python rag_test_driver.py llm_option_three prompt_option_three "does everyone have to pay state taxes?"`

If the above commands run without error, you have completed the Python application installation process.
Well done.


# II. Installing Promptfoo

Promptfoo is the primary test tool that we will be using for the end-to-end evaluation of the RAG solution.

## Pre-requisites
A. `node.js`: Promptfoo is a Node.js based application. 
Therefore, to install it, depending on your configuration, you may also need to install Node.js (e.g., npm, npx).

For me, running on a standard Amazon Linux instance, which did not have Node.js support, I did the following:

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash`

`source ~/.bashrc`

`nvm install --lts`

`node -e "console.log('Running Node.js ' + process.version)"`

`npm --version`

You may choose a different route. The `node.js` website provides many options: https://nodejs.org/en/download/package-manager

## Installation

1. Installing Promptfoo: Once `npm` is installed, installing `promptfoo` is easy. 
I used the following command line:

`npm install -g promptfoo`

Verify the installation with the following:

`promptfoo --version`

You can choose other options for installing Promptfoo than `npm`. 
The `promptfoo` website describes addition methods, see: https://www.promptfoo.dev/docs/installation/.
This guide assumes that you are using `npm`. Adjust accordingly, if you do otherwise.

2. Initializing Promptfoo: 
Start this short process by checking that you are in the directory where you install the `capstone_rag` application.
We will do all our testing and experimentation from here. 
To initialize `promptfoo`  use the following single line command:

`promptfoo init`

This will process will ask you some questions which will be used to create an initial `promptfooconfig.yaml`.
We will be overwriting that `promptfooconfig.yaml` in a couple of steps, so your answers will not have impact.
Note that, there are options to generate default test content for Amazon Bedrock, OpenAI, and Anthropic LLMs.
This may come in useful later.

3. Establish the initial test configuration for the `capstone_rag` application. 
To do this there are two things, the second of which is optional. 

First, copy the file `promptfooconfig_capstone_starter.yaml` to overwrite `promptfooconfig.yaml`

`cp promptfooconfig_capstone_starter.yaml promptfooconfig.yaml`

Second, and optionally, if you have an Anthropic API key, set and export that value in your environment. 
One way of doing this is:

`export ANTHROPIC_API_KEY="sk-ant-....YOUR_KEY_SPECIFICS...."`

This is used for one style of the testing that you will like want to use, called `llm-rubrik`.
You can use other LLM service providers than Anthropic for this task, including Bedrock.
Using an alternative to the one given here, is a good exercise to try, if you have time.

If you don't have a key, tests using the evaluation method `llm-rubrik` will fail with this configuration.
In which case it is recommended that you delete or modify those tests.

4. System test your installation by running the following command:

`promptfoo eval`

This is the command that is use most with Prompfoo. 
It drives the set of tests that are defined in the test configuration file.
You will see some successes and failures of the tests in the report that is produced.

The FAIL test instances should all be due to the content produced by the LLM not matching the test definition.
There should not be any configuration failures, unless 
    (a) the Anthropic Key has not been set, and,
    (b) the test that has the `llm-rubrik` that calls Anthropic, 
"I am self-employed can I expense dinners with clients?", has not been changed.

Once you have the tests running with repeated test runs using `promptfoo eval`, you have completed the system test process.

Well done!!
