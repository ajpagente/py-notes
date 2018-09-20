# Simple REST API with Flask

In my company, we have a server providing REST APIs to share a list of tasks, download files for a specific task, and to upload report on the task performed. Several organizations are using the services and each organization has to setup interfaces to the services. We provided a Python framework to quickly setup the services and we had to find a way to allow each organization to test their setup before integrating with the production server.

As we have used Python for the framework, we decided to use Flask to develop a local test server. The server does not implement any checks and is meant to be customized if rigorous tests are required. 

The following sections describes the server code and particularly focuses on three types of HTTP request: GET returning a JSON encoded information, GET with a file download, and POST with file upload.

## Environment
* Windows 7
* Python 3.7
* Python virtual environment (recommended)

## Python Setup
To be able to use Flask with HTTPS install the following:
* pip install flask
* pip install pyopenssl

## Code
Here is the complete code and the relevant sections are explained.

```python
from flask import Flask, request, jsonify, send_from_directory

app = Flask(__name__)

tasks = { "tasks": [{"id": 1, "name": "vuln scan"}, 
                      {"id": 2, "name": "disinfect"}]}

upload_response = { "id" : 1,
                    "name" : "vuln scan",
                    "status" : "PASSED",
                  }

@app.route('/xyz/api/cleaner/tasks', methods=['GET'])
def get_tasks():
    return jsonify(tasks)

@app.route('/xyz/api/cleaner/tasks/download', methods=['GET'])
def download_tools():
    return send_from_directory('./','1797242037.zip')

@app.route('/xyz/api/cleaner/tasks/report', methods=['POST'])
def upload_report():
    return jsonify(upload_response)

if __name__ == '__main__':
    app.run(ssl_context='adhoc', debug=True)

```

## Code Explained

### GET with JSON response
```python
@app.route('/xyz/api/cleaner/tasks', methods=['GET'])
def get_tasks():
    return jsonify(tasks)
```

The code responds to this GET request:
```
'https://localhost:5000/xyz/api/cleaner/tasks'
```

The requesting client receives the string representation of the tasks.

### GET with file download
```python
@app.route('/xyz/api/cleaner/tasks/download', methods=['GET'])
def download_tools():
    return send_from_directory('./','1797242037.zip')
```

You will notice that there is no decision made based on request parameters. This is a design decision as the above code is meant to provide a simple test. The user is expected to customize the code to their needs.

send_from_directory('./','1797242037.zip') returns the zip file named 1797242037.zip that is located in the same directory as this application.

The code responds to the GET request below. The id value is ignored in this code:
```
'https://localhost:5000/xyz/api/cleaner/tasks/download?id=1'
```
The client receives a file which it needs to save.

### POST with file upload
```python
@app.route('/xyz/api/cleaner/tasks/report', methods=['POST'])
def upload_report():
    return jsonify(upload_response)
```

The uploaded file is not saved by the server. A fixed response is sent back to the client.

### SSL
```python
app.run(ssl_context='adhoc', debug=True)
```

To run with https, ssl_context='adhoc' is passed to run(). You need pyopenssl to be able to use adhoc certificates. pyopenssl generates a self-signed cert everytime the run() method is called. On the client side, special handling is required to ignore any errors due to self-signed certificates. (WARNING: USE CA GENERATED CERTIFICATES IN PRODUCTION)