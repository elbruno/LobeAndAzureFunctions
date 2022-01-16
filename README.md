# Vision recognition using LOBE AI and Azure Functions

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](/LICENSE)
[![Twitter: elbruno](https://img.shields.io/twitter/follow/elbruno.svg?style=social)](https://twitter.com/kartben)
![GitHub: elbruno](https://img.shields.io/github/followers/elbruno?style=social)

During the last couple of months, I’ve having fun with my new friends at home: 🐿️🐿️🐿️. These little ones, are extremelly funny, and they literally don’t care about the cold 🥶❄️☃️.

[<img src="img/squirrell-on-the-snow.png" width="250"/>](squirrell-on-the-snow.png)

So, I decided to help them and build an Automatic Feeder using Azure IoT, a Wio Terminal and maybe some more devices. You can check the Azure IoT project here [Azure IoT - Squirrel Feeder](https://aka.ms/AzureIoTSquirrelFeederGitHub).

Once the feeder was ready, I decided to add a new feature to the scenario, detecting when a squirrel 🐿️ is nearby the feeder. In this repository I'll share:

- How to create an object recognition model using [LOBE](https://www.lobe.ai/).
- How to export the model to a Tensor Flow image format.
- How to run the model in an Azure Function.

[<img src="img/squirrel_detected.jpg" width="250"/>](squirrel_detected.jpg)

## LOBE AI

[LOBE](https://www.lobe.ai/) is an image recognition service that lets you build, deploy, and improve your own image identifier models. 

## Exporting the model to TensorFlow

Once the project  was trained, you can export it to several formats. We will use a TensorFlow format for the Azure Function. 

## Azure Function

Time to code! Let's create a new Azure Function Using [Visual Studio Code](https://code.visualstudio.com/) and the [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension. 


### Changes to `__ init __.py`
The following code is the final code for the `__ init __.py` file in the Azure Function.

A couple of notes:

- The function will receive a POST request with the file bytes in the body.
- In order to use the predict file, we must import the `predict` function from the `predict.py` file using ".predict"


```python
```

### Changes to `requirements.txt`

The `requirements.txt` file will define the necessary libraries for the Azure Function. We will use the following libraries:

```text
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

### Test using Postman

**Postman** is a great tool to test our function. You can use it to test the function in local mode and also to test the function once it has been deployed to Azure Functions. You can download Postman [here](https://www.postman.com/downloads/).

In order to test our function we need to know the function url. In Visual Studio Code, we can get the url by clicking on the Functions section in the Azure Extension. Then we can right click on the function and select "Copy Function URL".

Now we can go to Postman and create a new POST request using our function url. We can also add the image we want to test. Here is a live demo, with the function running locally, in Debug mode in Visual Studio Code:

We are now ready to test our function in Azure Functions. To do so we need to deploy the function to Azure Functions. And use the new Azure Function url with the same test steps. 

## Additional Resources

You can check a session recording about this topic in English and Spanish.

- Eng - [Computer Vision using LOBE running on Azure Functions](https://www.meetup.com/Microsoft-Reactor-Toronto/events/283031778/)
- Spa - [Coming soon](https://aka.ms/ServerlesssinJan1.11)

These links will help to understand specific implementations of the sample code:

- [Microsoft Learn - Create serverless applications](https://aka.ms/CreateServerlessApps-ci)
- [AZ-204: Implement Azure Functions](https://aka.ms/AzureFunctions-ci)

In my personal blog "[ElBruno.com](https://elbruno.com)", I wrote about several scenarios on how to work and code with [LOBE](https://elbruno.com/tag/lobe/). 

## Author

👤 **Bruno Capuano**

* Website: https://elbruno.com
* Twitter: [@elbruno](https://twitter.com/elbruno)
* Github: [@elbruno](https://github.com/elbruno)
* LinkedIn: [@elbruno](https://linkedin.com/in/elbruno)

## 🤝 Contributing

Contributions, issues and feature requests are welcome!

Feel free to check [issues page](https://github.com/elbruno/CustomVisionAndAzureFunctions/issues).

## Show your support

Give a ⭐️ if this project helped you!


## 📝 License

Copyright &copy; 2021 [Bruno Capuano](https://github.com/elbruno).

This project is [MIT](/LICENSE) licensed.

***