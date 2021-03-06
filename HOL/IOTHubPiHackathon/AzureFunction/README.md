## Azure Functions Lab

In this part of the lab, you will create an Azure function that will be used to programmatically send data back to the Raspberry Pi. The Azure function that you will create will be triggered by events that arrive at the IoT Hub. If the status of the Raspberry Pi is normal and the newly reported temperature goes above the set threshold, the status of Sense HAT will be set to high. If the status is at high and the temperature goes below the threshold, the status will be reset to normal. These state changes will be sent back to the Raspberry Pi through a Cloud to Device (C2D) message and the status message will be displayed on the Sense HAT LED display. 
Note that if your are using the Sense HAT emulator, the text that shows up across the emulated LED display may be faint and may show up quickly. Keep an eye on the LED display so you can see the message appear. 

### Create a Function

In the next part of this lab, you will be creating a C# Azure Function that will get triggered whenever the IoT hub service receives a new event. 
For ease of getting through the lab, we have provided the code that you will need to write the function. When triggered, the code in the function will compare the new event value to the set threshold. The threshold value is the tag parameter setting that you previously set to a value of 40. If the value is above or below, the function will send a cloud to device (C2D) message to the RaspberryPi. Note: To be technically correct, the function actually gets triggered when the event hub compatible endpoint within the IoT Hub service receives an event. IoT Hub service is built with the event hub service running under the covers.
1. Navigate to the Azure portal: https://portal.azure.com 
2. Click the ‘+’ sign and type in “function app” 
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/CreateFunction1.jpg" width="50%" height="50%" />
    </p> 
 3.	Click the “Create” button <br> 
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/CreateFunction2.jpg" width="50%" height="50%" />
    </p> 

4.	Fill out the required values to create a function <br>
  - Provide the function app a name (eg. functionC2DHoL)
  - Select your Azure subscription
  - Select your existing subscription that you are using for the hands on lab
  - Select "Windows" for the OS
  - For hosting plan, select “consumption plan”
  - For location, choose the closest data centre (eg. East US)
  - For Runtime stack, choose ".NET"
  - For storage, select “create new” and provide a name for the storage
  - You can leave Application Insights turned off
  - Click “Create” <br>
      <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/CreateFunction3.jpg" width="30%" height="30%" />
    </p> 
5. Once the Function app is created, click the function (the function icon is the one in the shape of a lightning bolt)
6. Click the ‘+’ sign beside the “Functions” node in the hierarchy tree
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/CreateFunction4.jpg" />
    </p> 
7. Click on “In-Portal” then Continue.
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/FunctionsV2GetStarted.PNG" width="50%" height="50%" />
    </p> 
8. Choose "More templates..." then click on "Finish and view templates".
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/FunctionsV2MoreTemplates.PNG" width="50%" height="50%" />
    </p> 
9. Scroll down and choose the “Azure Event Hub trigger”. 
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/eventHubTrigger.jpg" width="50%" height="50%" />
    </p> 
10. Input all the values to create your new IoT Hub based function.
  - Enter a name for your new function in the “Name:” field. eg. MessageTriggerFunction
  - click "new"
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/newFunction1.jpg" width="50%" height="50%" />
    </p>   
  - In the _Connection_ pop-up, click the "IoT Hub" button and select the IoT Hub that you provisioned. The _Endpoint_ should be "Events (built-in endpoint)". Click "Select"
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/functionConnection.jpg" width="50%" height="50%" />
    </p> 
  - You should be brought back to the *New Function* blade and the *Event Hub connection* and *Event hub consumer group* (leave it at *$Default*) fields should be auto-filled. 
    You will need to type in the *Event Hub name*. The name is a substring of the connection string (see highlighted substring in the screenshot). Paste this value into the *Event Hub name* field and click the "Create" button. 
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/createNewFunction.jpg" width="50%" height="50%" />
    </p>     
11.	The template for your new Event Hub trigger is now created! 
12.	You will now configure the required libraries that will be needed for the new function created. 
  - Expand the “Logs” view at the bottom of the page
  - Click on “View Files”	

![Expand Function views](/HOL/IOTHubPiHackathon/images/FunctionsV2ViewFiles.PNG)

  - Click on “+ Add” under the "View files" tab. 
  - Enter “function.proj” <br />
![Add project file](/HOL/IOTHubPiHackathon/images/FunctionsV2AddFunctionFile.PNG)

  - Copy the text from [function.proj](/HOL/IOTHubPiHackathon/AzureFunction/function.proj) file in the github repo to the new json file you created.
  - Click "Save". 
    <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/FunctionsV2SaveProj.PNG" width="50%" height="50%" />
    </p>    
13.	Now add the main source code that will used within the function
  - Copy the text from [Function.txt](/HOL/IOTHubPiHackathon/AzureFunction/AzureFunction.txt) in the github repo to the "run.csx" file. 
  - In the run.csx file, find the CONNECTION_STRING variable and set the value to the IoT Hub Primary Key Connection String obtained in an earlier lab.
  - Click “Save and run” to run the function

   <p align="center">
    <img src="/HOL/IOTHubPiHackathon/images/FunctionsV2RunFunction.PNG" />
    </p>

## Trying it out

You will now attempt to trigger the function and have the function send a message back to the Sense HAT. 
If you recall in lab 2, you created a tag parameter called tags.HighTemperatureLimit and set it to 40. This is the threshold that will determine when the status of the Sense HAT will change to Hot (if temperature is above the limit) or to Normal (when the temperature drops below the limit). When the status changes, the state (hot or normal) will appear on the Sense HAT display LEDs. 

1. If your python script is no longer running on the Raspberry Pi, start it back up using the command ```python SenseHat_IoTHub_Http_Lab_Key.py```
2. Try to get the temperature of the Sense HAT above the threshold value (if set to the instructed value, you should be trying to get the temperature above 40C)

If you are having difficulties getting the temperature on your physical Sense HAT over the threshold, you can do one of the following:
- Lower the threshold in the device twin for the RaspberryPi device. You can do this in the preconfigured solution portal (see steps in lab 2 to determine how to change the HighTemperatureLimit) 
- Update your Python script to use the Sense Hat emulator instead of the physical board. Using the Sense HAT emulator will allow you to virtually control the temperature (and other properties)

[Back to Main HOL Instructions](../README.md)
