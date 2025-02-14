# Feedback Meeting

![](https://image.ibb.co/gV7Oeo/Capture_d_e_cran_2018_06_21_a_10_47_49.png)

## Introduction

### What is it ?

Feedback Meeting is a service which allow to retrieve user's feedbacks, posted after a call, from the Cisco endpoints based in the first floor of the Cisco France building.

### Why ?

This service was built in order to showcases the API integrated in the Cisco endpoints.

### How it works ?

This service is composed of two parts :

- the first part is an In-Room Control interface, appearing following a call, which allow the user to post a feedback thanks to the TextInput, available in the API
- the second part is a web interface which display the feedbacks and furthers informations on the call.

### Why a fork?
I just want to translate it to Italian language :)

## Build Setup

### Clone project and install dependencies

``` bash
# clone the project
git clone https://github.com/tloyau/feedback-meeting

# go the project
cd feedback-meeting

# install dependencies
npm install

# go to client directory
cd client

# install dependencies
npm install
```

### Database

Feedback Meeting uses MongoDB database. Follow these steps in order to run the project properly :

 1. Install MongoDB on your machine (https://www.mongodb.com/download-center#community)
 2. Through MongoDB console, create a database named `Feedback Meeting`
 3. Create two collections : `codecs` and `calls`
 4. Go to `codecs` collection
 5.  Add a new codec followed this schema : 
``` json
{
    "systemName": "XXX",
    "macAddress": "XX:XX:XX:XX:XX:XX",
    "ipAddress": "XX.XXX.XXX.XXX",
    "productType": "XXX",
    "username": "XXX",
    "password": "XXX",
    "firmware": "XXX",
    "uri": "XXX"
}
```
You can add as many codecs as you want.

### Environment variables
 Create now a `.env` file at the root of the project and fill the appropriate fields. I attached an example in the repository : `.env.example`

### Logs
 Before run the project, create a folder named `log`. The logs will be added to this folder in the future.

### Run the project
 You can now run the project. The Node.js server will register HttpFeedbacks for you.
``` bash
# build for production with minification
npm run build
```

### Run the project as a service
You might want to run it as a service (I do).

Just install node-windows by typing:
```
npm install -g node-windows
```
Then, in project root, run:
```
npm link node-windows
```

Then create a script like this and run it with node:
```
var Service = require('node-windows').Service;

// Create a new service object
var svc = new Service({
  name:'Feedback Meeting Portal',
  description: 'Web server that collects feedbacks from video endpoints',
  script: 'C:\\feedback-meeting\\server.js',
  nodeOptions: [
    '--harmony',
    '--max_old_space_size=4096'
  ]
  //, workingDirectory: '...'
  //, allowServiceLogon: true
});

// Listen for the "install" event, which indicates the
// process is available as a service.
svc.on('install',function(){
  svc.start();
});

svc.install();
```
