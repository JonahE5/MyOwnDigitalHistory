_Code_ from Alex Lane, updated by Jonah and Jeff:

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#first-we-go-get-the-sdk-stuff)First we go get the sdk stuff:

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#do-this-once-per-session)Do this once per session

!pip install --upgrade azure-cognitiveservices-vision-computervision

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#httpsdocsmicrosoftcomen-usazurecognitive-servicescomputer-visionquickstarts-sdkpython-sdk-1)[https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk](https://docs.microsoft.com/en-us/azure/cognitive-services/computer-vision/quickstarts-sdk/python-sdk)

from azure.cognitiveservices.vision.computervision import ComputerVisionClient from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes from msrest.authentication import CognitiveServicesCredentials

from array import array import os from PIL import Image import sys import time import csv

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#you-have-to-sign-up-for-a-free-trial-with-azure-httpsportalazurecom-1)you have to sign up for a free trial with azure, [https://portal.azure.com](https://portal.azure.com/)

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#then-make-a-resource-under-cognitive-resources-1)then make a resource under 'cognitive resources'

# [](https://github.com/JonahE5/MyOwnDigitalHistory/blob/main/Devlog%203.md#for-computer-vision-to-get-the-correct-api-endpoint-1)for computer vision to get the correct api, endpoint

os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY'] = ('insert key here') os.environ['COMPUTER_VISION_ENDPOINT'] = ('[https://my-vision-api.cognitiveservices.azure.com/](https://my-vision-api.cognitiveservices.azure.com/)')

output_file_flag = "_HwRA_output"

def create_new_output_file( one_or_many="One", file_type="TXT", directory_path="", input_file_name="" ): if(one_or_many == "One"): return create_new_output_file_one(file_type, directory_path) else: return create_new_output_file_many( file_type, directory_path, input_file_name)

def create_new_output_file_one(file_type="TXT", directory_path=""): images_processed_output = "" output_file_name = input("Please specify a name for the output file: ") if(file_type == "CSV"): images_processed_output = open( directory_path + output_file_name + ".csv", mode='w') image_output_writer = csv.writer(images_processed_output).writerow else: image_output_writer = open( directory_path + output_file_name + ".txt", mode='w') return image_output_writer, images_processed_output

def create_new_output_file_many( file_type="TXT", directory_path="", input_file_name="" ): images_processed_output = ""

```
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
```

def select_directory_path(input_dir_flag): while(True): if (input_dir_flag): potential_directory_path = input( "Please enter the directory path of the image(s) you wish to " "parse for handwritten text: ") else: potential_directory_path = input( "Please enter the output directory path: ") if(os.path.exists(potential_directory_path)): valid_directory_path_bytes = os.fsencode( potential_directory_path) valid_directory_path_string = potential_directory_path break else: print("Invalid directory path, does not exist.") continue return valid_directory_path_bytes, valid_directory_path_string

def select_input_file_types(): input_filetypes = [] while(True): file_type_to_add = input( "Please enter a image type to search for (.png, .jpg, etc.): ") input_filetypes.append(file_type_to_add) choose_another = True while(True): add_another_type = input( "Would you like to add another file type to search for? Y/N: ") if (add_another_type == "Y"): break elif (add_another_type == "N"): choose_another = False break else: print("Not a valid entry. Please enter either Y or N.") continue if(choose_another is False): break return input_filetypes

def select_num_of_output_files(): while(True): one_or_many = input( "Would you like to output the images containing handwriting to " "one file or unique, one-to-one files? One/Many: ") if(one_or_many == "One" or one_or_many == "Many"): break else: print("Not a valid entry. Please enter either One or Many.") continue return one_or_many

def select_output_file_types(): while(True): pick_output_file_type = input( "Please pick an output file type. CSV/TXT: ") if(pick_output_file_type == "CSV" or pick_output_file_type == "TXT"): break else: print( "Not a valid entry for output file type. Please enter either" "CSV or TXT.") continue return pick_output_file_type

def azure_setup(): # Add your Computer Vision subscription key to your environment variables. if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ: subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY'] else: print( "\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable." "\n**Restart your shell or IDE for changes to take effect.**") sys.exit() # Add your Computer Vision endpoint to your environment variables. if 'COMPUTER_VISION_ENDPOINT' in os.environ: endpoint = os.environ['COMPUTER_VISION_ENDPOINT'] else: print( "\nSet the COMPUTER_VISION_ENDPOINT environment variable." "\n**Restart your shell or IDE for changes to take effect.**") sys.exit()

```
return ComputerVisionClient(
    endpoint, CognitiveServicesCredentials(subscription_key))
```

def send_and_receive_azure_image_data( writer, input_directory_filepath, image_file_types, output_directory_filepath, output_file_type, one_or_many, image_open ): print(input_directory_filepath, "image_file_types: ", image_file_types, output_directory_filepath, output_file_type, one_or_many, image_open) for suffix in image_file_types: print("suffixes for images:",suffix) for image in os.listdir(input_directory_filepath): print(image) if(os.path.isfile(image)):  
image_name = os.fsdecode(image)

```
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
```

computervision_client = azure_setup()

use_defaults = False answer = str(input("Do you want to use defaults values in the code? Answer Y/y:")).lower() if answer=="y": use_defaults = True

if use_defaults == True: # This proceeds with default values _, directory_of_images_filepath = os.fsencode("/content/"),"/content/" image_input_filetypes = [".jpg", ".png"] pick_one_or_many = "One" image_output_filetype = "CSV" _, output_directory = os.fsencode("/content/"),"/content/" else: # This asks for input _, directory_of_images_filepath = select_directory_path(True) image_input_filetypes = select_input_file_types() pick_one_or_many = select_num_of_output_files() image_output_filetype = select_output_file_types() _, output_directory = select_directory_path(False)

output_writer = "" opened_image = None if (pick_one_or_many == "One"): output_writer, opened_image = create_new_output_file( pick_one_or_many, image_output_filetype, output_directory)

"""## Now we connect up with Azure"""

last_img_to_close = send_and_receive_azure_image_data( output_writer, directory_of_images_filepath, image_input_filetypes, output_directory, image_output_filetype, pick_one_or_many, opened_image)

if(image_output_filetype == "CSV"): last_img_to_close.close()