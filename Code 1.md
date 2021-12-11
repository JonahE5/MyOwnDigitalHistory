_Code_ from Detecting-handwriting tutorial updated by Jonah and Jeff:

## [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#1-get-the-packages-we-need-import-the-bits-we-want)1. Get the packages we need, import the bits we want.

We only do this whenever we start this process. It only has to be done once per session.

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#first-we-go-get-the-sdk)First we go get the sdk:

!pip install --upgrade azure-cognitiveservices-vision-computervision

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#httpsdocsmicrosoftcomen-usazurecognitive-servicescomputer-visionquickstarts-sdkpython-sdk)[https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk)

from azure.cognitiveservices.vision.computervision import ComputerVisionClient from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes from msrest.authentication import CognitiveServicesCredentials

from array import array import os from PIL import Image import sys import time

## [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#2-now-we-connect-up-with-azure)2. Now we connect up with Azure

We only do this during our start up, after we've done the bit above. **Notice lines 4 and 5 in the block below**, where it says `COMPUTER_VISION_SUBSCRIPTION_KEY` and `COMPUTER_VISION_ENDPOINT`. These are two variables that you need to complete with the information from the computer vision service you created in your Azure portal (a reminder of what this looks like is at [figure 7](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/shawngraham.github.io/dhmuse/detecting-handwriting/#a-shortcut).

The key will be a long string of letters and numbers separated by hyphens; paste this **between** the `'` where it says `xxxx`. The endpoint will be a full url **with** the https bit. Paste that **between** the `'` where it says `yyyy`.

Once you've done that, run the three blocks of code below in order.

**PS** if you share this notebook, _delete_ the key and endpoint and replace with xxx and yyy before you share it. You don't want this info lying around.

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#you-have-to-sign-up-for-a-free-trial-with-azure-httpsportalazurecom)you have to sign up for a free trial with azure, [https://portal.azure.com](https://portal.azure.com/)

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#then-make-a-resource-under-cognitive-resources)then make a resource under 'cognitive resources'

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#for-computer-vision-to-get-the-correct-api-endpoint)for computer vision to get the correct api, endpoint

os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']='insert key here' os.environ['COMPUTER_VISION_ENDPOINT']='[https://my-vision-api.cognitiveservices.azure.com/](https://my-vision-api.cognitiveservices.azure.com/)'

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#add-your-computer-vision-subscription-key-to-your-environment-variables)Add your Computer Vision subscription key to your environment variables.

if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ: subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY'] else: print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**") sys.exit()

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#add-your-computer-vision-endpoint-to-your-environment-variables)Add your Computer Vision endpoint to your environment variables.

if 'COMPUTER_VISION_ENDPOINT' in os.environ: endpoint = os.environ['COMPUTER_VISION_ENDPOINT'] else: print("\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n**Restart your shell or IDE for changes to take effect.**") sys.exit()

computervision_client = ComputerVisionClient(endpoint, CognitiveServicesCredentials(subscription_key))

## [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#3-set-up-and-load-our-target-image)3. Set up and load our target image.

Now the fun begins. The two blocks below do two things.

The first creates a variable called 'remote_image_url' and gives it the information of where our image lives.

The second loads the image up and gets it ready for processing.

Easiest solution: Put your images in a github repository. Once your image is uploaded to github, right-click it and select 'copy image url'. The resulting URL will follow this pattern: [https://raw.githubusercontent.com/shawngraham/demo/master/frontenac-card.png](https://raw.githubusercontent.com/shawngraham/demo/master/frontenac-card.png) . The 'raw.githubusercontent.com/your-name/your-repo/master/file-name.png' is what you want.

Run the two code blocks in order.

remote_image_url = "[https://raw.githubusercontent.com/JonahE5/Transcription1/main/NAO_Test.png](https://raw.githubusercontent.com/JonahE5/Transcription1/main/NAO_Test.png)"

''' Batch Read File, recognize printed text - remote This example will extract printed text in an image, then print results, line by line. This API call can also recognize handwriting (not shown). ''' print("===== Batch Read File - remote =====")

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#get-an-image-with-printed-text)Get an image with printed text

remote_image_printed_text_url = remote_image_url

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#remote_image_printed_text_url--httpsrawgithubusercontentcomazure-samplescognitive-services-sample-data-filesmastercomputervisionimagesprinted_textjpg)remote_image_printed_text_url = "[https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/printed_text.jpg](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/printed_text.jpg)"

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#call-api-with-url-and-raw-response-allows-you-to-get-the-operation-location)Call API with URL and raw response (allows you to get the operation location)

recognize_printed_results = computervision_client.read(remote_image_printed_text_url, raw=True)

## [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#4-now-ocr-that-handwriting)4. Now OCR that handwriting!

The next bit of code sends our image through the Azure computers. Just run it, and Azure will give us back the text that it has identified. Copy the text to a file somewhere, go back to step 3 and change up the remote_image_url, run those two blocks, then this one, to get your next image(s).

ta da!

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#get-the-operation-location-url-with-an-id-at-the-end-from-the-response)Get the operation location (URL with an ID at the end) from the response

operation_location_remote = recognize_printed_results.headers["Operation-Location"]

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#grab-the-id-from-the-url)Grab the ID from the URL

operation_id = operation_location_remote.split("/")[-1]

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#call-the-get-api-and-wait-for-it-to-retrieve-the-results)Call the "GET" API and wait for it to retrieve the results

while True: get_printed_text_results = computervision_client.get_read_result(operation_id) if get_printed_text_results.status not in ['NotStarted', 'Running']: break time.sleep(1)

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#print-the-detected-text-line-by-line)Print the detected text, line by line

if get_printed_text_results.status == OperationStatusCodes.succeeded: for text_result in get_printed_text_results.analyze_result.read_results: for line in text_result.lines: print(line.text) #print(line.bounding_box) print()

"""

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#call-the-get-api-and-wait-for-it-to-retrieve-the-results-1)Call the "GET" API and wait for it to retrieve the results

while True: read_result = computervision_client.get_read_result(operation_id) if read_result.status not in ['notStarted', 'running']: break time.sleep(1)

```
# Print the detected text, line by line
```

if read_result.status == OperationStatusCodes.succeeded: for text_result in read_result.analyze_result.read_results: for line in text_result.lines: print(line.text) print(line.bounding_box) print() """