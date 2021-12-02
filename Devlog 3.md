**My Own Digital History**

---
tutorial: # Maps From Google Sheets
date: November 25, 2021
tags: [[Transcription]][[Digital Humanities]][[software tools]][[HIST 5706]]

---

# what I was trying to do

_Use microsoft azure to recognize handwriting in documents and transcribe the content_

Azure: https://portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade
https://portal.azure.com/#@cmail.carleton.ca/resource/subscriptions/565e3250-6658-4004-a479-0fa48c0fb11a/resourceGroups/ResourceGroup1/providers/Microsoft.CognitiveServices/accounts/my-vision-api/cskeys

## what I did

I forgot to copy most of the error messages before I fixded them so some of the below is from memory

Detecting-handwriting tutorial
+ step 1 
	+ Jeff B shared a tutorial and some code with me: https://shawngraham.github.io/dhmuse/detecting-handwriting/
+ step 2
	+ following the tutorial, I set up an azure account and recieved a key and an endpoint
+ step 3
	+ followed the tutorial, I used the link for the code on Google Collab: https://colab.research.google.com/drive/1GnlIYzQp5IaCIfKaIFPfoY5G9btlX-Ht and made my own copy 
+ step 4
	+ I followed the tutorial instructions in collab but I had to fix some of the names of items in Step 1 to download. Referencing Microsoft's website, I found that some of the names had been changed
	+ https://docs.microsoft.com/en-us/azure/cognitive-services/Computer-vision/quickstarts-sdk/client-library?pivots=programming-language-python&tabs=visual-studio
+ step 5
	+ Step 2, I input my key and endpoint
	+ uploaded an image with text to github as the tutorial suggested 
+ step 6
	+ Step 3 loading the image is where I encountered some problems
	+ error was that the computer couldn't find: "batch_read_file"
	+ after much googling, I found that code now prefers '.read'. I made the change and got a new error
	+ error: 'ComputerVisionOcrErrorException: Operation returned an invalid status code 'Bad Request'
	+ I double checked the URL and tried different variations to make sure it was correct
	+ I double checked the read command but no luck
	+ I showed the problem to Jeff B
+ step 7
	+ Jeff tested the image URL and got a 404 error
	+ realized that my Github repository was set to private
+ step 8
	+ had to update some of the object names
+ step 9
	+ It worked! See results below


Alex L's Code
+ step 1
	+ despite failing the tutorial, it was very informative about how to use Azure so I tried to use Alex's code: https://github.com/AehLane/Handwriting-Parsing-App/blob/master/Handwriting-Parsing-App.py
	+ I pasted the code into google collab
+ step 2
	+ Accidently filled in my information in the promt text instead of running the code and waiting for it to ask questions 
	+ reset
+ step 3
	+ ran code, did not recognize the 'read' command
	+ google says to ad 'os.' before and it worked
+ step 4
	+ next error: 'invalid path'
	+ uploaded my image to collab instead of using the github link
+ step 5
	+ created an output directory path, aka a folder in google collab where the finished transcriptions would go
+ step 6
	+ errors about using CSV and TXT, I made sure they were included in the imports at the beginning
	+ still created csv and txt files in collab that were completely empty which was odd
+ step 7
	+ noticed the produced files weren't being put in the right folder, realized I had to add a '/' to the end of the output directory path
+ step 8
	+ Error: Program was denied access to Azure. Could not complete file write. An exception has occurred, use %tb to see the full traceback.
	+ Checked the keys and my URLs, was not able to solve this problem.
+ step 9
	+ Showed it to Jeff B during our meeting
	+ we adjusted the commands to try and narrow down the problem  
+ step 10
	+ Jeff B made some updates (He's more familiar with Python than I) and simplified the process too. Big thanks!
	+ Process works!


Original Test document: ![alt text](https://github.com/JonahE5/Transcription1/blob/main/NAO_Test.png?raw=true)

Transcription with Azure Results:
=====2 Processed Image - 'NAO_Test.png' =====
SIZE.
DESCRIPTION.
WHERE BORN
"ATTESTATION, &c."
FORMER SERVICE IN ANY CORPS APPLICABLE
TO FOREIGN SERVICE.
AGE
TRADE
At
Balat-
In what
"Period, dedu tiny Ser-"
Actual Service in the
Ealist-
44 Years
"County,"
For what
viCe prior to the Age of
"Eu-t or West Indies, in-"
NAMES / 18
ment.
"City, or"
Occupa-
Period of
By ubow calisted.
"Corps, ce if 18 Years, was the Time"
cluded in the preceding
"Parish,"
Place.
Date.
"alent by Duertion,"
Columus.
"tion,"
Service.
on the
Town.
Out Pension.
"Marks, &c"
Fert.
inches.
Dass.
Complexion.
"Form of Visage,"
Feat.
Hesten Jos. 54 \18 . Fair grey fair Song
Atton Sos. 3 2
Anderson Sno o
elllen Same 6 75
anderson calle J 6%


## challenges

_based on my errors, I think I'm having trouble connecting to Azure's resources. It might be that Microsoft has changed something and the code is slightly out of date?_

## thoughts on where to go next

_Now that Azure is working, it seems it is still struggling with this font of text. However, Azure was the shortcut. Perhaps the Transkribus Project will have a better time processing the documents if I spend some more time teaching it to recognize this text_



*Code* from Detecting-handwriting tutorial updated by Jonah and Jeff:


## 1. Get the packages we need, import the bits we want.

We only do this whenever we start this process. It only has to be done once per session.


# First we go get the sdk:
!pip install --upgrade azure-cognitiveservices-vision-computervision

# https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

from array import array
import os
from PIL import Image
import sys
import time

## 2. Now we connect up with Azure

We only do this during our start up, after we've done the bit above. **Notice lines 4 and 5 in the block below**, where it says `COMPUTER_VISION_SUBSCRIPTION_KEY` and `COMPUTER_VISION_ENDPOINT`. These are two variables that you need to complete with the information from the computer vision service you created in your Azure portal (a reminder of what this looks like is at [figure 7](shawngraham.github.io/dhmuse/detecting-handwriting/#a-shortcut). 

The key will be a long string of letters and numbers separated by hyphens; paste this **between** the `'` where it says `xxxx`. The endpoint will be a full url **with** the https bit. Paste that **between** the `'` where it says `yyyy`.

Once you've done that, run the three blocks of code below in order.

**PS** if you share this notebook, _delete_ the key and endpoint and replace with xxx and yyy before you share it. You don't want this info lying around.

# you have to sign up for a free trial with azure, https://portal.azure.com
# then make a resource under 'cognitive resources'
# for computer vision to get the correct api, endpoint
os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']='insert key here'
os.environ['COMPUTER_VISION_ENDPOINT']='https://my-vision-api.cognitiveservices.azure.com/'

# Add your Computer Vision subscription key to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()
# Add your Computer Vision endpoint to your environment variables.
if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
else:
    print("\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

computervision_client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(subscription_key))

## 3. Set up and load our target image.

Now the fun begins. The two blocks below do two things. 

The first creates a variable called 'remote_image_url' and gives it the information of where our image lives. 

The second loads the image up and gets it ready for processing.

Easiest solution: Put your images in a github repository. Once your image is uploaded to github, right-click it and select 'copy image url'. The resulting URL will follow this pattern: https://raw.githubusercontent.com/shawngraham/demo/master/frontenac-card.png  .  The 'raw.githubusercontent.com/your-name/your-repo/master/file-name.png' is what you want. 

Run the two code blocks in order.

remote_image_url = "https://raw.githubusercontent.com/JonahE5/Transcription1/main/NAO_Test.png"

'''
Batch Read File, recognize printed text - remote
This example will extract printed text in an image, then print results, line by line.
This API call can also recognize handwriting (not shown).
'''
print("===== Batch Read File - remote =====")
# Get an image with printed text
remote_image_printed_text_url = remote_image_url
# remote_image_printed_text_url = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/printed_text.jpg"
# Call API with URL and raw response (allows you to get the operation location)
recognize_printed_results = computervision_client.read(remote_image_printed_text_url, raw=True)

## 4. Now OCR that handwriting!

The next bit of code sends our image through the Azure computers. Just run it, and Azure will give us back the text that it has identified. Copy the text to a file somewhere, go back to step 3 and change up the remote_image_url, run those two blocks, then this one, to get your next image(s).

ta da!

# Get the operation location (URL with an ID at the end) from the response
operation_location_remote = recognize_printed_results.headers["Operation-Location"]
# Grab the ID from the URL
operation_id = operation_location_remote.split("/")[-1]

# Call the "GET" API and wait for it to retrieve the results 
while True:
    get_printed_text_results = computervision_client.get_read_result(operation_id)
    if get_printed_text_results.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Print the detected text, line by line
if get_printed_text_results.status == OperationStatusCodes.succeeded:
    for text_result in get_printed_text_results.analyze_result.read_results:
        for line in text_result.lines:
            print(line.text)
            #print(line.bounding_box)
print()

"""
# Call the "GET" API and wait for it to retrieve the results 
while True:
    read_result = computervision_client.get_read_result(operation_id)
    if read_result.status not in ['notStarted', 'running']:
        break
    time.sleep(1)

    # Print the detected text, line by line
if read_result.status == OperationStatusCodes.succeeded:
    for text_result in read_result.analyze_result.read_results:
        for line in text_result.lines:
            print(line.text)
            print(line.bounding_box)
print()
"""








*Code* from Alex Lane, updated by Jonah and Jeff:


# First we go get the sdk stuff:
# Do this once per session
!pip install --upgrade azure-cognitiveservices-vision-computervision

# https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

from array import array
import os
from PIL import Image
import sys
import time
import csv

# you have to sign up for a free trial with azure, https://portal.azure.com
# then make a resource under 'cognitive resources'
# for computer vision to get the correct api, endpoint
os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY'] = ('insert key here')
os.environ['COMPUTER_VISION_ENDPOINT'] = ('https://my-vision-api.cognitiveservices.azure.com/')

output_file_flag = "_HwRA_output"


def create_new_output_file(
    one_or_many="One",
    file_type="TXT",
    directory_path="",
    input_file_name=""
):
    if(one_or_many == "One"):
        return create_new_output_file_one(file_type, directory_path)
    else:
        return create_new_output_file_many(
            file_type, directory_path, input_file_name)


def create_new_output_file_one(file_type="TXT", directory_path=""):
    images_processed_output = ""
    output_file_name = input("Please specify a name for the output file: ")
    if(file_type == "CSV"):
        images_processed_output = open(
            directory_path + output_file_name + ".csv",
            mode='w')
        image_output_writer = csv.writer(images_processed_output).writerow
    else:
        image_output_writer = open(
            directory_path + output_file_name + ".txt",
            mode='w')
    return image_output_writer, images_processed_output


def create_new_output_file_many(
    file_type="TXT",
    directory_path="",
    input_file_name=""
):
    images_processed_output = ""

    if(file_type == "CSV"):
        images_processed_output = open(
            directory_path + input_file_name + output_file_flag + ".csv",
            mode='w')
        image_output_writer = csv.writer(images_processed_output).writerow
    else:
        image_output_writer = open(
            directory_path + input_file_name + output_file_flag + ".txt",
            mode='w')
    return image_output_writer, images_processed_output


def select_directory_path(input_dir_flag):
    while(True):
        if (input_dir_flag):
            potential_directory_path = input(
                "Please enter the directory path of the image(s) you wish to "
                "parse for handwritten text: ")
        else:
            potential_directory_path = input(
                "Please enter the output directory path: ")
        if(os.path.exists(potential_directory_path)):
            valid_directory_path_bytes = os.fsencode(
                potential_directory_path)
            valid_directory_path_string = potential_directory_path
            break
        else:
            print("Invalid directory path, does not exist.")
            continue
    return valid_directory_path_bytes, valid_directory_path_string


def select_input_file_types():
    input_filetypes = []
    while(True):
        file_type_to_add = input(
            "Please enter a image type to search for (.png, .jpg, etc.): ")
        input_filetypes.append(file_type_to_add)
        choose_another = True
        while(True):
            add_another_type = input(
                "Would you like to add another file type to search for? Y/N: ")
            if (add_another_type == "Y"):
                break
            elif (add_another_type == "N"):
                choose_another = False
                break
            else:
                print("Not a valid entry. Please enter either Y or N.")
                continue
        if(choose_another is False):
            break
    return input_filetypes


def select_num_of_output_files():
    while(True):
        one_or_many = input(
            "Would you like to output the images containing handwriting to "
            "one file or unique, one-to-one files? One/Many: ")
        if(one_or_many == "One" or one_or_many == "Many"):
            break
        else:
            print("Not a valid entry. Please enter either One or Many.")
            continue
    return one_or_many


def select_output_file_types():
    while(True):
        pick_output_file_type = input(
            "Please pick an output file type. CSV/TXT: ")
        if(pick_output_file_type == "CSV" or pick_output_file_type == "TXT"):
            break
        else:
            print(
                "Not a valid entry for output file type. Please enter either"
                "CSV or TXT.")
            continue
    return pick_output_file_type


def azure_setup():
    # Add your Computer Vision subscription key to your environment variables.
    if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
        subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
    else:
        print(
            "\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable."
            "\n**Restart your shell or IDE for changes to take effect.**")
        sys.exit()
    # Add your Computer Vision endpoint to your environment variables.
    if 'COMPUTER_VISION_ENDPOINT' in os.environ:
        endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
    else:
        print(
            "\nSet the COMPUTER_VISION_ENDPOINT environment variable."
            "\n**Restart your shell or IDE for changes to take effect.**")
        sys.exit()

    return ComputerVisionClient(
        endpoint, CognitiveServicesCredentials(subscription_key))




def send_and_receive_azure_image_data(
    writer,
    input_directory_filepath,
    image_file_types,
    output_directory_filepath,
    output_file_type,
    one_or_many,
    image_open
):
    print(input_directory_filepath, "image_file_types: ", image_file_types, output_directory_filepath,  output_file_type, one_or_many, image_open)
    for suffix in image_file_types:
        print("suffixes for images:",suffix)
    for image in os.listdir(input_directory_filepath):
        print(image)
        if(os.path.isfile(image)):              
            image_name = os.fsdecode(image)

            if any(suffix in image_name for suffix in image_file_types):
            
                '''
                Extracts handwritten text in an image, then print results,
                line by line.
                '''
                if (one_or_many == "Many"):
                    writer, image_open = create_new_output_file(
                            one_or_many,
                            output_file_type,
                        output_directory_filepath,
                        image_name)

                print("=====1 Processed Image - '" + image_name + "' =====")
    
                if(output_file_type == "CSV"):
                    writer(
                        ["=====2 Processed Image - '" + image_name + "' ====="])
                else:
                    writer.write(
                        "=====3 Processed Image - '" + image_name + "' ====="
                        + "\n")

                # Get an image with printed text
                local_image = open(input_directory_filepath + image_name, 'rb')
 
                # example code
                # https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py


                recognize_printed_results = (computervision_client.read_in_stream(local_image, raw=True))
                
                operation_location_remote = (recognize_printed_results.headers["Operation-Location"])
                # Grab the ID from the URL
                operation_id = operation_location_remote.split("/")[-1]

                # Call the "GET" API and wait for the retrieval of the results
                while True:
                    read_result = computervision_client.get_read_result(operation_id)
                    if read_result.status.lower () not in ['notstarted', 'running']:
                        break
                    print ('Waiting for result...')
                    time.sleep(10)
                # Print the detected text, line by line
                if read_result.status == OperationStatusCodes.succeeded:
                    for text_result in read_result.analyze_result.read_results:
                        for line in text_result.lines:
                            print(line.text)
                            if (output_file_type == "CSV"):
                                writer([line.text])
                            else:
                                writer.write(line.text + "\n")
                print()
                time.sleep(2.0)
            else:
                print(image_name, " does not match a file suffix.")
        else:
            print(image, " is not a file.")
    return image_open


computervision_client = azure_setup()

use_defaults = False
answer = str(input("Do you want to use defaults values in the code? Answer Y/y:")).lower()
if answer=="y":
    use_defaults = True  

if use_defaults == True:
    # This proceeds with default values 
    _, directory_of_images_filepath = os.fsencode("/content/"),"/content/"
    image_input_filetypes = [".jpg", ".png"]
    pick_one_or_many = "One"
    image_output_filetype = "CSV"
    _, output_directory = os.fsencode("/content/"),"/content/"
else:
    # This asks for input
    _, directory_of_images_filepath = select_directory_path(True)
    image_input_filetypes = select_input_file_types()
    pick_one_or_many = select_num_of_output_files()
    image_output_filetype = select_output_file_types()
    _, output_directory = select_directory_path(False)


output_writer = ""
opened_image = None
if (pick_one_or_many == "One"):
    output_writer, opened_image = create_new_output_file(
        pick_one_or_many, image_output_filetype, output_directory)

"""## Now we connect up with Azure"""

last_img_to_close = send_and_receive_azure_image_data(
    output_writer,
    directory_of_images_filepath,
    image_input_filetypes,
    output_directory,
    image_output_filetype,
    pick_one_or_many,
    opened_image)

if(image_output_filetype == "CSV"):
    last_img_to_close.close()
