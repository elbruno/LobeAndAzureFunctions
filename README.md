# Vision recognition using LOBE AI and Azure Functions

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](/LICENSE)
[![Twitter: elbruno](https://img.shields.io/twitter/follow/elbruno.svg?style=social)](https://twitter.com/kartben)
![GitHub: elbruno](https://img.shields.io/github/followers/elbruno?style=social)

During the last couple of months, I’ve having fun with my new friends at home: 🐿️🐿️🐿️. These little ones, are extremelly funny, and they literally don’t care about the cold 🥶❄️☃️.

[<img src="img/squirrell-on-the-snow.png" width="250"/>](squirrell-on-the-snow.png)

So, I decided to help them and build an Automatic Feeder using Azure IoT, a Wio Terminal and maybe some more devices. You can check the Azure IoT project here [Azure IoT - Squirrel Feeder](https://aka.ms/AzureIoTSquirrelFeederGitHub).

Once the feeder was ready, I decided to add a new feature to the scenario, detecting when a squirrel 🐿️ is nearby the feeder. In this repository I'll share:

- How to create an image recognition model using [LOBE](https://www.lobe.ai/).
- How to export the model to a TensorFlow image format.
- How to run the model in an Azure Function.

[<img src="img/squirrel_detected.jpg" width="250"/>](squirrel_detected.jpg)

## LOBE AI

[LOBE](https://www.lobe.ai/) is a free, easy-to-use Microsoft desktop application that allows you to build, manage, and use custom machine learning models. With Lobe, you can create an image classification model to categorize images into labels that represent their content. 

Here's a summary of how to prepare a model in Lobe:
- Import and label images.
- Train your model.
- Evaluate training results.
- Play with your model to experiment with different scenarios.
- Export and use your model in an app.

The [Overview of image classification model by Lobe](https://docs.microsoft.com/en-us/ai-builder/lobe-overview) section contains step-by-step instructions that let you make calls to the service and get results in a short period of time. 

You can use the images in the "[LOBE/Train/](LOBE/Train/)" directory in this repository to train your model.

<img src="img/LabeledImagesInLobeProject.jpg" width="450"/>


Here is the model performing live recognition in action: 

<img src="img/LobeTestImage.gif" width="650"/>


## Exporting the model to TensorFlow

Once the project  was trained, you can export it to several formats. We will use a TensorFlow format for the Azure Function. 

<img src="img/LobeExportModel.jpg" width="450"/>

The exported model has several files. The following list shows the files that we use in our Azure Function:

- labels.txt: The labels that the model recognizes
- saved_model.pb: The model definition
- signature.json: The model signature
- example/tf_example.py.py: sample python code that uses the exported model.

You can check the exported model in the "[Lobe/ExportedModel](Lobe/ExportedModel/)" directory in this repository.

## Azure Function

Time to code! Let's create a new Azure Function Using [Visual Studio Code](https://code.visualstudio.com/) and the [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension. 


### Changes to `__ init __.py`
The following code is the final code for the `__ init __.py` file in the Azure Function.

A couple of notes:

- The function will receive a POST request with the file bytes in the body.
- In order to use the `tf_model_helper` file, we must import the `tf_model_helper.py` function from the `tf_model_helper.py` file using ".tf_model_helper"
- `ASSETS_PATH` and `TF_MODEL` are the variables that we will use to access the exported model. We will use os.path to resolve the current path to the exported model.
- The result of the function will be a JSON string with the prediction. Jsonify will convert the TF_Model() image prediction to a JSON string.


```python
import logging
import azure.functions as func

# Imports for image procesing
import io
import os
from PIL import Image
from flask import Flask, jsonify

# Imports for prediction
from .tf_model_helper import TFModel

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    results = "{}"
    try:
        # get and load image from POST
        image_bytes = req.get_body()    
        image = Image.open(io.BytesIO(image_bytes))

        # Load and intialize the model and the app context
        app = Flask(__name__)  

        # load LOBE Model using the current directory
        scriptpath = os.path.abspath(__file__)
        scriptdir  = os.path.dirname(scriptpath)
        ASSETS_PATH = os.path.join(scriptdir, "model")
        TF_MODEL = TFModel(ASSETS_PATH)

        with app.app_context():        
            # prefict image and process results in json string format
            results = TF_MODEL.predict(image)            
            jsonresult = jsonify(results)
            jsonStr = jsonresult.get_data(as_text=True)
            results = jsonStr

    except Exception as e:
        logging.info(f'exception: {e}')
        pass 

    # return results
    logging.info('Image processed. Results: ' + results)
    return func.HttpResponse(results, status_code=200)
```

### Changes to `requirements.txt`

The `requirements.txt` file will define the necessary libraries for the Azure Function. We will use the following libraries:

```text
# DO NOT include azure-functions-worker in this file
# The Python Worker is managed by Azure Functions platform
# Manually managing azure-functions-worker may cause unexpected issues

azure-functions
requests
Pillow
numpy
flask
tensorflow
opencv-python
```

### Sample Code

You can view a sample function completed code in the "[AzureFunction/LobeSquirrelDetectorFunction/](AzureFunction/LobeSquirrelDetectorFunction/)" directory in this repository.


## Testing the sample

Once our code is complete we can test the sample in local mode or in Azure Functions, after we deploy the Function. In both scenarios we can use any tool or language that can perform HTTP POST requests to the Azure Function to test our function.

### Test using Curl

Curl is a command line tool that allows you to send HTTP requests to a server. It is a very simple tool that can be used to send HTTP requests to a server. We can test the local function using curl with the following command:

```bash
❯ curl http://localhost:7071/api/LobeSquirrelDetectorFunction -Method 'Post' -InFile 01.jpg
```

<img src="img/TestUsingCurl.jpg" width="450"/>


### Test using Postman

**Postman** is a great tool to test our function. You can use it to test the function in local mode and also to test the function once it has been deployed to Azure Functions. You can download Postman [here](https://www.postman.com/downloads/).

In order to test our function we need to know the function url. In Visual Studio Code, we can get the url by clicking on the Functions section in the Azure Extension. Then we can right click on the function and select "Copy Function URL".

<img src="img/CopyAzureFunctionUrl.jpg" width="450"/>

Now we can go to Postman and create a new POST request using our function url. We can also add the image we want to test. Here is a live demo, with the function running locally, in Debug mode in Visual Studio Code:

<img src="img/AzFncLocalTestWithPostman.gif" width="700"/>

We are now ready to test our function in Azure Functions. To do so we need to deploy the function to Azure Functions. And use the new Azure Function url with the same test steps. 



## Additional Resources

You can check a session recording about this topic in English and Spanish.

- Eng - [Computer Vision using LOBE running on Azure Functions](https://www.meetup.com/Microsoft-Reactor-Toronto/events/283031778/)
- Spa - [Coming soon](https://aka.ms/ServerlesssinJan1.11)

These links will help to understand specific implementations of the sample code:

- [Microsoft Learn - Create serverless applications](https://aka.ms/CreateServerlessApps-ci)
- [AZ-204: Implement Azure Functions](https://aka.ms/AzureFunctions-ci)
- [Microsoft Docs - Overview of image classification model by Lobe (preview)](https://docs.microsoft.com/en-us/ai-builder/lobe-overview)
- [Microsoft Docs - Export your image classification model from Lobe to AI Builder (preview)](https://docs.microsoft.com/en-us/ai-builder/lobe-export)

In my personal blog "[ElBruno.com](https://elbruno.com)", I wrote about several scenarios on how to work and code with [LOBE](https://elbruno.com/tag/lobe/). 

## Author

👤 **Bruno Capuano**

* Website: https://elbruno.com
* Twitter: [@elbruno](https://twitter.com/elbruno)
* Github: [@elbruno](https://github.com/elbruno)
* LinkedIn: [@elbruno](https://linkedin.com/in/elbruno)

## 🤝 Contributing

Contributions, issues and feature requests are welcome!

Feel free to check [issues page](https://github.com/elbruno/LobeAndAzureFunctions/issues).

## Show your support

Give a ⭐️ if this project helped you!


## 📝 License

Copyright &copy; 2021 [Bruno Capuano](https://github.com/elbruno).

This project is [MIT](/LICENSE) licensed.

***