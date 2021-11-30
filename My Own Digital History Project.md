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

