---
date: 2016-04-09T16:50:16+02:00
title: How-to for Flow as a Service
weight: 20
---

This page will guide you through the set of steps needed to make your flogo application run as 'Flow as a Service'.

## AWS Lambda function

Follow these steps to create an AWS Lambda function example

### Requirements

- Install flogo cli details [here](https://github.com/TIBCOSoftware/flogo-cli#installation)
- Install docker [here](https://docs.docker.com/engine/installation/)
- make command available

### Create the application

Create a flogo.json file with the following content

```json
{
  "name": "lambda",
  "type": "flogo:app",
  "version": "0.0.1",
  "description": "My flogo application description",
  "triggers": [
    {
      "id": "my_lambda_trigger",
      "ref": "github.com/TIBCOSoftware/flogo-contrib/trigger/lambda",
      "settings": {
      },
      "handlers": [
        {
          "actionId": "my_simple_flow",
          "settings": {
          }
        }
      ]
    }
  ],
  "actions": [
    {
      "id": "my_simple_flow",
      "name": "my simple flow",
      "ref": "github.com/TIBCOSoftware/flogo-contrib/action/flow",
      "data": {
        "flow": {
          "name": "my simple flow",
          "attributes": [],
          "rootTask": {
            "id": 1,
            "type": 1,
            "tasks": [
              {
                "id": 2,
                "type": 1,
                "activityRef": "github.com/TIBCOSoftware/flogo-contrib/activity/log",
                "name": "log",
                "attributes": [
                  {
                    "name": "message",
                    "value": "Simple Log",
                    "type": "string"
                  }
                ]
              }
            ],
            "links": [
            ]
          }
        }
      }
    }
  ]
}
```

Create the app using the copied flogo.json

Note: this example command assumes that you are executing it in the same directory where flogo.json lives

```bash
flogo create -f flogo.json lambda

cd lambda
```

After that, your application is created


### Build the application

We will be building an embedded application [-e] option and with target shim [-shim] option using the trigger id as shim

```bash
flogo build -e -shim my_lambda_trigger
```

This command will pull the docker image 'eawsy/aws-lambda-go-shim:latest' locally and build the zip file needed to run in AWS lambda.

Once this command finishes successfully the zip file (handler.zip) will be located in your app directory (for example /path/to/app/lambda/src/lambda/handler.zip).


### Deploy the application

Deploy to AWS providing the following configuration
- Runtime: python2.7
- Handler: handler.Handle

<p align="center">
   <img src="https://github.com/eawsy/aws-lambda-go-shim/blob/master/asset/aws_config-preview.png" align="center">
</p>

### Final notes

We are using the library https://github.com/eawsy/aws-lambda-go-shim behind the scenes, so you can have a look at it to understand a bit more the internals.