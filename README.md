# Fine-Tuning-Yolov3

-- This repository explains how you can fine tune the Yolov3 model with your custom dataset. 

## The outline of my work is as folllows: <br />
  1- Collect your images. <br />
  2- Annotate with LabelImg. (This is what I have used.)<br />
  3- Modify the configuration files of darknet YOLO according to the dataset. <br /> 
  4- Train with Google Colab or on your local machine. (Fine-tuning)<br />
## Clone the Darknet: <br />
  1- After completing the first two steps mentioned above, you have to clone the [Darknet repo ](https://github.com/pjreddie/darknet) . <br />
  2- After cloning the repository, put images and annotation data to  " /darknet/data/(Name of your folder contains the images and labels ) " .<br />
## Generate Train.txt and Test.txt: <br />
  1- Use the script " Dataset-Splitter.py " to split the dataset into train and test. <br />
  2- Place the created Train.txt and Test.txt files into  " /darknet/cfg " .<br />
  
