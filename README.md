
# Policy QA (RAG Pipelines)
---------------
### Steps to set up the application
#### Configure keys.env file
**step 1.** Obtain the openAI API keys and initialize it to OPENAI_API_KEY variable.
**step 2.** Obtain the llama cloud API keys from, [https://cloud.llamaindex.ai/api-key] after logging in. Click on Generate New Key, get your api keys and initialise it to LLAMA_CLOUD_API_KEY variable. Used to parse documents.
**step 3.** Obtain the elastic cloud api keys from, [https://cloud.elastic.co/registration?plcmt=nav&pg=nav&tech=rpt&cta=eswt-b]. First login and then obtain the keys. Initialize it to ES_API_KEY
**step 4.** Obtain the cloud ids from [https://cloud.elastic.co/deployments/e5121bb20f2648b8a95d514ad5ec870a], initialize it to ES_CLOUD_ID
---------------
## Use these steps if you need to reproduce the results for other files.
### Steps to create a conda environment
Run the following commands in the terminal
```bash
conda create -n pdf_qa python=3.10
conda activate pdf_qa
pip install -r requirements.txt
```

### Steps to parse documents and create index.
**step 1.** Parse the documents store in pdfs folder
```bash
python parse_files.py
```
**step 2.**  create Index. Use create_index.py to create index in the elasticvector store.
```bash
python create_index.py
```
This will create 3 indices by the following names
text_doc_index, text_summary_index, text_doc_without_headings_index

---------------

### Steps for RAG evaluation
#### create golden samples for RAG evaluation and save it.
This will create synthetic samples for RAG evaluation, further all the methods/ RAG approches' results will be computed on the golden samples and saved.
```bash
python create_eval_dataset.py
```

### Run FastAPI
#### either you can run fast api directly from the terminal or you can first deploy the model in a container and then run it
```bash
uvicorn api:app --reload
```
You can then open the browser link displayed and add /docs in the link


## Build Docker Image
If docker is not installed, install docker in the system using the link[https://docs.docker.com/engine/install/]
```bash
cd pdf_qa
docker build -t policy_qa_image . # build docker image
docker run -d -p 8000:8000 --name qa_container policy_qa_image # build docker container
```
If a same container is running in your system first stop the container and remove it
```bash
docker stop qa_container
docker remove qa_container
```
To see the use the fast API copy and paste the following link on your browser. 
[http://localhost:8000/docs#/]
### 

## File Structure
The following is an overview of the project's file structure. Each directory and file is explained to clarify its role in the project.

pdf_qa/
│
├──eval_data/       # the golden samples created by the llm are saved here 
│      
│__eval_methods/    # results for the golden samples for each method are stored here
│
├──json_files/     # the output obtained from llamaparse, along with the document structure is stored here consisting of jsons
│
│──pdfs/          # pdf file corresponding to the documents being passed into the llama parse for chunking
│
├── summary_json/   # consists of all summaries for each document in json format.
├──api.py           # consists of code of fast api for each method and obtaining evaluation metrics
│
├──create_eval_dataset.py     # consists of code for evaluation data preparation, golden samples and the results of methods
│
├──create_index.py      # code for indexing of the documents into elastic store
│
├──default_app.py       # code for RAG using llama_index, hybrid retrieval with default embeddings
│
├──Dockerfile       # dockerfile to containerize the app
│
├──eval.py        # codes for generating the evaluation/ metrics on the golden samples with the 
│
├──keys.env       # all the api keys are stored here
│
├──parse_files.py     # code to parse pdf files, structurise them into json
│
├──rag_main.py      # code to define custom rag pipelines which can handle various kinds of queries
│
├──pdf_qa_exp.ipynb   # experimentation
├── README.md           # Documentation of the project
├──req_install.txt      # pip install for libraries
├──requirements.txt    # Python dependencies for the project
└── utils.py    # contains code to define custom elastic search class and query,search , create vectore index 

