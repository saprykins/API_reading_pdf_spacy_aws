# API TO GET INFORMATION FROM PDF


# OVERVIEW
API that receives and saves pdf file, extracts and saves its text, meta-data and named entities in local database.  
API allows to retrieve text and meta-data of a pdf-file previously uploaded via its id.  
It is required to have AWS credentials information in the following path: 
~/.aws/credentials  


# MORE DETAILS

## What it can 
It can receive and analyse 'PDF' and 'pdf' files. Other files are rejected.  
In case user starts to request empty database, the user receives an error message.  
In case user requests unsupported id like character or a digit which is not in database range, the user receives an error message. 


## What it can not

It can recieve only one file at a time.  


## Application structure  

```
API_reading_pdf/  
├── flaskr  
│   ├── init.py  
│   ├── controller.py  
│   └── model.py  
├── tests  
│   └── test_**.py   (files contain code to test the app)  
├── uploads          (pdf-files storage, created automatically after app launch)  
├── Dockerfile       (instruction to create Docker container)  
├── venv             (virtual environment folder)  
├── pdf.db           (database file, created automatically after app launch)  
├── readme.md  
├── requirements.txt (list of required libs and packages)  
├── sample.pdf       (pdf file for tests)  
└── wsgi.py          (application entry point)  
```


# INSTALLATION (UBUNTU OS)

Unzip the archive from the (link)[https://github.com/saprykins/API_reading_pdf_spacy_aws.git]   

The application requires Python installed.  

 * For more details about Python installation, check the following link:  
 https://www.python.org/downloads/  

In folder "API_reading_pdf" create vitual environment  

* You can create it from command line:  
  ```
  virtualenv <my_env_name>
  ```
  where <my_env_name> is the name of the virtual environment you would like to create.  

  As an example, to create a virtual environment 'venv' you should type in terminal: 
  ```
  virtualenv venv
  ```
* If you do not have virtual environment tool installed, you can install it from command line:    
  ```
  sudo apt install python3-virtualenv
  ```

Activate the virtual environment you have just created:  
In Ubuntu:
```
source <my_env_name>/bin/activate
```
* In case you created virtual environment "venv" you activate it as follows:  
  ```
  source ./venv/bin/activate
  ```

* For more details about virtual environment, check the following link:  
  https://docs.python.org/3/tutorial/venv.html  

Install the packages that application requires by typing in command line:  
```
pip install -r requirements.txt
```
Type in command line the command below to install additional package:  
```
python -m spacy download en_core_web_sm
```
Configure your AWS Command Line Interface (AWS CLI) to use AWS resources from command line  
Install AWS CLI by typing in command line  
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"  
```
```
unzip awscliv2.zip
```
```
sudo ./aws/install
```
Provide "Access key ID" and "Secret access key" by typing in command line  
```
aws configure
```
In response you will have an invite to input your "Access key ID", then "Secret access key", then region.  
* The instruction below will help you to create admin user with appropriate policies and programmatic access. To find out where to get this information, check the information below.  

  You should open your AWS web console  

  In search-bar type "IAM"  

  In left verticla menu choose "users"

  Click on button "add users"  

  Type user name, for example "admin"  

  Check on "Access key - Programmatic access"  

  From 3 rectangles choose the one that states "Attach existing policies directly"  

  Choose "AdministratorAccess"  

  Click on "next: tags" in the right bottom corner  

  Click on "next: review" in the right bottom corner  

  Click on "create user" button

  Copy "Access key ID" and past it in command line where aws configure was typed  

  Copy "Secret access key" and past it in the same command line window  

  When region is requested in command line, type "eu-central-1"


# TUTORIAL

## Run the application  

To run the application after installation, stay in folder "flask_reading_pdf" and type the following in command line:  
```
python wsgi.py
```
The application runs while the command line window is open.  

* You can check in your web-browser that the application works by typing in address line of your browser:  

  http://localhost:5000/  

  You should see text "Index page" in your browser

## Upload a pdf-file  

Open a new terminal window, to let your application run in previous terminal window  

To upload a pdf-file "sample.pdf" using command line, you can use curl-command  

* In case curl tool is not installed you can do this in command line:  
  ```
  sudo apt install curl   
  ```

In terminal, go to the folder where the file you want to send is located.  

You can find a sample.pdf file in "flask_reading_pdf" folder.  

To send sample.pdf file to application, type:  
```
curl -sF file=@"sample.pdf" http://localhost:5000/documents
```
* Standard response returns in the following format:  
  ```
  {
      "id": 1
  }
  ```
  where 1 is id number of the record in database.  

* At this moment the pdf-file is saved in 'API_reading_pdf-develop/uploads' on your local machine, text, metadata and named entities are saved in local sqlite database.  

* You should use this id to retrieve the information about the file.  


## Get metadata  

To retrive metadata about a file, you need its id (which is document_id) that you got from on the previous step.  
```
curl -s http://localhost:5000/documents/<document_id>
```
* where document_id should be replaced by a number.  

* In case you sent at least one file, you can retrieve metadata related to the record 1 in database by typing:  
  ```
  curl -s http://localhost:5000/documents/1
  ```
  
The standard response is in the following format:
```
{
  "author": "GPL Ghostscript SVN PRE-RELEASE 8.62",
  "creation_date": "D:20080201104827-05'00'",
  "creator": "dvips(k) 5.86 Copyright 1999 Radical Eye Software",
  "file_id": "tpvtajdvrlrqdecv",
  "link_to_content": "http://localhost:5000/text/1.txt",
  "modification_date": "D:20080201104827-05'00'",
  "named_entities_aws":"Stallman, Sussman, Gaschnig, Ginsberg, ","named_entities_spacy":"Ginsberg, Dynamic Backtracking, Bruynooghe, David McAllester, Selman et.al, McAllester, Mark Fox, Don Geddis, Will Harvey, Vipin Kumar, Scott Roy, Narinder Singh, Ari Jonsson, Dynamic Backtracking\n\nProof, "}
}
```
We can find names of authors and people in the lines that starts with "named_entities_**"  

The line that starts with "named_entities_aws" contains named entities found by AWS service "Comprehend".  

The line that starts with "named_entities_spacy" contains named entities found by SpaCy Python library.  

Instead of curl, you can also retrieve metadata via web-browser  

Type in address line http://localhost:5000/documents/<document_id>  

* where document_id should be replaced by a number.  

* In case you sent at least one file, you can retrieve metadata related to the record 1 in database by typing:  

  http://localhost:5000/documents/1  


We can find names of authors and people in the lines that starts with "named_entities_**"  

The line that starts with "named_entities_aws" contains named entities found by AWS service "Comprehend".  

The line that starts with "named_entities_spacy" contains named entities found by SpaCy Python library.  


## Stop the application

To stop the application, type "ctr + C" in terminal window where it was launched or close the terminal window.  

