# Starter Web App (Flask w/ Google Sheets)

An example web application, built in Python with the Flask package and a Google Sheets datastore, for educational purposes.

View live in production at: https://web-app-starter-flask-sheets.herokuapp.com/products.

## Installation

Clone or download from [GitHub source](https://github.com/prof-rossetti/web-app-starter-flask-sheets). And navigate there from the command-line:

```sh
cd starter-web-app-flask-sheets
```

Create and activate an Anaconda virtual environment:

```sh
conda create -n web-starter-sheets-env # first time only
conda activate web-starter-sheets-env
```

> NOTE: Subsequent commands assume you're running them from within the virtual environment, in the root directory of the repository.

Install package dependencies (first time only):

```sh
pip install -r requirements.txt
```

## Setup

### Google Sheets

> adapted from, see also: [this excellent blog post](https://www.twilio.com/blog/2017/02/an-easy-way-to-read-and-write-to-a-google-spreadsheet-in-python.html)

#### Downloading API Credentials

Visit the [Google Developer Console](https://console.developers.google.com/cloud-resource-manager). Create a new project, or select an existing one. Click on your project, then from the project page, search for the "Google Sheets API" and enable it. Also search for the "Google Drive API" and enable it.

From either API page, or from the [API Credentials](https://console.developers.google.com/apis/credentials) page, follow a process to create and download credentials to use the APIs. Fill in the form to find out what kind of credentials:

  + API: "Google Sheets API"
  + Calling From: "Web Server"
  + Accessing: "Application Data"
  + Using Engines: "No"

The suggested credentials will be for a service account. Follow the prompt to create a new service account with a role of: "Project" > "Editor", and create credentials for that service account. Download the resulting .json file and store it in this repo as "auth/google_api_credentials.json".

There is a way for us to configure the spreadsheet service to read the contents of that file, but to make the development environment compatible with the way the application needs to operate on the the Heroku server, we'll store the credentials information in an environment variable instead:

```sh
export GOOGLE_API_CREDENTIALS="$(< auth/google_api_credentials.json)"
echo $GOOGLE_API_CREDENTIALS #> { "type": "service_account" ... }
```

#### Configuring a Google Sheet Datastore

Create your own Google Sheet document, or use this [example public](https://docs.google.com/spreadsheets/d/1_hisQ9kNjmc-cafIasMue6IQG-ql_6TcqFGpVNOkUSE/edit#gid=0) document.

If you create your own, make sure it contains a sheet called "Products" with column headers `id`, `name`, `department`, `price`, and `availability_date`. And modify the document's sharing settings to grant "edit" privileges to the "client email" address located in the credentials file.

Note the document's unique identifier from its URL, and store the identifier in an environment variable called `GOOGLE_SHEET_ID`.

## Usage

Read and write example data to and from your Google Sheets document:

```sh
python app/spreadsheet_service.py
```

Run a local web server, then view your app in a browser at http://localhost:5000/:

```sh
FLASK_APP=web_app flask run
```

> NOTE: you can quit the server by pressing ctrl+c at any time. If you change a file, you'll likely need to restart the server for the changes to take effect.


## Deploying

First, [install the Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install), and make sure you can login and list your applications.

```sh
heroku login

heroku apps:list
```

Create an application server (e.g. "web-app-starter-flask-sheets"):

```sh
heroku apps:create web-app-starter-flask-sheets # or do this from the online console
```

Find the application's "heroku git url" from the application's settings tab in the heroku online dashboard, then associate this repository with that remote address:

```sh
git remote add heroku-sheets REMOTE_ADDRESS # like https://git.heroku.com/web-app-starter-flask-sheets.git
```

Set the entire contents of the credentials.json file into an environment variable (approach allows service to function on Heroku server without uploading the .json file there):

```sh
heroku config:set GOOGLE_API_CREDENTIALS="$(< auth/google_api_credentials.json)" -a web-app-starter-flask
-sheets
```

Deploy to the "heroku-sheets" remote address:

```sh
git push heroku-sheets sheets:master
```

## [Licence](/LICENSE.md)
