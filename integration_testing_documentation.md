# Integration Testing

Integration testing is a software testing technique that focuses on verifying the interactions and data exchange between different components or modules of a software application. The goal of integration testing is to identify any problems or bugs that arise when different components are combined and interact with each other. Integration testing is typically performed after unit testing and before system testing. It helps to identify and resolve integration issues early in the development cycle, reducing the risk of more severe and costly problems later on.

* Integration testing can be done by picking module by module. This can be done so that there should be a proper sequence to be followed.
* And also if you don’t want to miss out on any integration scenarios then you have to follow the proper sequence. 
* Exposing the defects is the major focus of the integration testing and the time of interaction between the integrated units.

## Reason Behind Integration Testing

Although all modules of a software application are already tested in unit testing, errors still exist due to the following reasons:

1. Each module is designed by individual software developers whose programming logic may differ from developers of other modules, so integration testing becomes essential to determine the working of software modules.
2. To check the interaction of software modules with the database whether it is erroneous or not.
3. Requirements can be changed or enhanced at the time of module development. These new requirements may not be tested at the level of unit testing hence integration testing becomes mandatory.
4. Incompatibility between modules of software could create errors.
5. To test hardware's compatibility with software.
6. If exception handling is inadequate between modules, it can create bugs.

## Guidelines for Integration Testing

1. Integration testing will be done only if the modules are dependent on each other. Each integration scenario should compulsorily have ```source→data→destination```. Any scenario can be called an integration scenario only if the data gets saved in the destination.
2. We go for the integration testing only after the functional testing is completed on each module of the application.
3. We always do integration testing by picking module by module so that a proper sequence is followed, and also we don't miss out on any integration scenarios.
4. First, determine the test case strategy through which executable test cases can be prepared according to test data.
5. Examine the structure and architecture of the application and identify the crucial modules to test them first and also identify all possible scenarios.
6. Design test cases to verify each interface in detail.
7. Choose input data for test case execution. Input data plays a significant role in testing.
8. Perform positive and negative integration testing.

# Example scenario

### Lets take a simple RAG API application as an example to further understand about integration testing:
You can find the implementation of this API [here](https://github.com/abhi-pixel1/RAG-api).

This FastAPI application serves as a document processing and RAG question-answering system.  The chatbot uses MongoDB Atlas for storing and retrieving document embeddings, and integrates with Hugging Face's Mixtral-8x7B-Instruct-v0.1 model for generating responses. The API handles file uploads, performs text chunking, and processes user queries to provide contextually relevant answers. It offers the following main features:

* ```/uploadfile/``` Endpoint
    * Purpose: Handles the upload of text (.txt) files
    * Functionality:
        * Receives a text file
        * Processes the file content
        * Stores the processed information in the knowledge base

* ```/upload``` Endpoint

    * Purpose: Manages the upload of PDF files
    * Functionality:
        * Accepts PDF documents
        * Extracts and processes the text content
        * Adds the processed information to the knowledge base

* ```/uploadppt``` Endpoint

    * Purpose: Deals with PowerPoint (.ppt) file uploads
    * Functionality:
        * Takes in PowerPoint presentations
        * Extracts text content from slides
        * Processes and stores the information in the knowledge base

* ```/query/``` Endpoint
    * Purpose: Handles user queries and generates responses
    * Functionality:
        * Receives a text query from the user
        * Searches the knowledge base for relevant information
        * Considers past conversation history
        * Uses a language model to generate a response
        * Stores the interaction in the conversation history


# Integration Testing for FastAPI RAG Model API

## Overview
Here we provide a detailed explanation of the integration tests for a FastAPI API implementing a Retrieval-Augmented Generation (RAG) model.


## Pytest Fixture
```python
@pytest.fixture(scope="module")
def knowledge_base_collection():
    uri = os.getenv("MONGO_URI")
    mongo_client = MongoClient(uri, server_api=ServerApi('1'))
    db = mongo_client["RAG_Store"]
    knowledge_base_collection = db["knowledge_base"]
    yield knowledge_base_collection
    mongo_client.close()
```

Before diving into individual test cases, it is important to understand the ```knowledge_base_collection``` fixture used in the tests.

Description: 

The knowledge_base_collection fixture sets up and tears down the MongoDB connection required for the tests. It ensures a consistent database state across tests and reduces overhead by reusing the same database connection.

Steps:

* Loads the MongoDB URI from environment variables.
* Establishes a connection to the MongoDB database.
* Yields the knowledge_base collection for use in the tests.
* Closes the MongoDB connection after all tests are completed.

Integration Testing: 

This fixture is crucial for integration testing as it sets up the database environment required for the tests, ensuring that the tests interact with the actual MongoDB collection used by the application.

## Test Cases
1. ```test_upload_txt_file```
&nbsp;

```python
def test_upload_txt_file(knowledge_base_collection):    
    with open("spider.txt", "rb") as file:
        response = client.post("/uploadfile/", files={"file": file})
    
    assert response.status_code == 200
    response_data = response.json()
    file_path = {'source':list(response_data.keys())[0]}
    assert knowledge_base_collection.count_documents(file_path) == list(response_data.values())[0]
```
Description:

This test case verifies the functionality of the ```/uploadfile/``` endpoint for uploading text files. It ensures that a text file is successfully uploaded, chunked, and added to the MongoDB knowledge base collection.

Steps:

* Opens a sample text file (spider.txt).
* Sends a POST request to the /uploadfile/ endpoint with the file.
* Checks if the response status code is 200 (OK).
* Extracts the file path and document count from the response.
* Asserts that the number of documents stored in the MongoDB collection matches the count returned in the response.

Integration Testing: 

This test case verifies the integration between FastAPI endpoint, file handling, text chunking, embedding creation, and MongoDB storage. It ensures that the components work together to process and store the uploaded text file.


2. ```test_upload_pdf_file```
&nbsp;

```python
def test_upload_pdf_file(knowledge_base_collection):
    with open("atten.pdf", "rb") as file:
        response = client.post("/upload", files={"file": file})
    
    assert response.status_code == 200
    response_data = response.json()
    file_path = {'source':list(response_data.keys())[0]}
    assert knowledge_base_collection.count_documents(file_path) == list(response_data.values())[0]
```

Description: 

This test case verifies the functionality of the /upload/ endpoint for uploading PDF files. It ensures that a PDF file is successfully uploaded, chunked, and added to the MongoDB knowledge base collection.

Steps:

* Opens a sample PDF file (atten.pdf).
* Sends a POST request to the /upload/ endpoint with the file.
* Checks if the response status code is 200 (OK).
* Extracts the file path and document count from the response.
* Asserts that the number of documents stored in the MongoDB collection matches the count returned in the response.

Integration Testing: 

This test case verifies the integration between FastAPI endpoint, file handling, PDF loading, text chunking, embedding creation, and MongoDB storage. It ensures that the components work together to process and store the uploaded PDF file.

3. ```test_upload_ppt_file```
&nbsp;
```python
def test_upload_ppt_file(knowledge_base_collection):
    with open("cap.pptx", "rb") as file:
        response = client.post("/uploadppt", files={"file": file})
    
    assert response.status_code == 200
    response_data = response.json()
    file_path = {'source':list(response_data.keys())[0]}
    assert knowledge_base_collection.count_documents(file_path) == list(response_data.values())[0]
```

Description: This test case verifies the functionality of the ```/uploadppt/``` endpoint for uploading PowerPoint files. It ensures that a PowerPoint file is successfully uploaded, chunked, and added to the MongoDB knowledge base collection.

Steps:

* Opens a sample PowerPoint file (cap.pptx).
* Sends a POST request to the /uploadppt/ endpoint with the file.
* Checks if the response status code is 200 (OK).
* Extracts the file path and document count from the response.
* Asserts that the number of documents stored in the MongoDB collection matches the count returned in the response.

Integration Testing: 

This test case verifies the integration between FastAPI endpoint, file handling, PowerPoint loading, text chunking, embedding creation, and MongoDB storage. It ensures that the components work together to process and store the uploaded PowerPoint file.

4. ```test_query```
&nbsp;
```python
def test_query():
    response = client.post("/query", json={"text": "What is the content of the document?"})
    assert response.status_code == 200
```

Description: This test case verifies the functionality of the ```/query/``` endpoint. It checks if the endpoint can process a search query and return a response from the knowledge base.

Steps:

* Sends a POST request to the /query/ endpoint with a sample query (What is the content of the document?).
* Checks if the response status code is 200 (OK).

Integration Testing: 

This test case verifies the integration between FastAPI endpoint, retrieval of relevant documents from the MongoDB knowledge base, processing of the query through the LLM model, and returning a response. It ensures that the components work together to handle and respond to user queries.

# Conclusion
Each of these test cases is crucial for integration testing as they verify the interactions between various components of the application. Integration testing ensures that different parts of the system, such as the API endpoints, file handling, text processing, embedding generation, and database operations, work together seamlessly to deliver the expected functionality. These tests help identify issues that may arise when components are integrated, ensuring the robustness and reliability of the overall system.
