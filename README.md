# Fine-Tuning-Yolov3

-- This repository explains how you can fine tune the Yolov3 model with your custom dataset. I have a dataset which I extracted from security camera recordings. I wanted to fine tune the yolov3 detection model on this particular dataset to increase the performance of the model when we deploy the model on this particular security cameras. I can not share the dataset here because it is private use.

## The outline of my work is as folllows: <br />
  1- Collect your images. <br />
  2- Annotate with LabelImg. (This is what I have used.)<br />
  3- Modify the configuration files of darknet YOLO according to the dataset. <br /> 
  4- Train with Google Colab or on your local machine. (Fine-tuning)<br />
## Clone the Darknet: <br />
  1- After completing the first two steps mentioned above, you have to clone the [Darknet repo ](https://github.com/pjreddie/darknet) . <br />
  2- After cloning the repository, put images and annotation data to  " /darknet/data/(Name of your folder contains the images and labels ) " .<br />
  3- You can first fork the darknet repo and then commit the changes that we will make, you can also download the darknet on local machine make modifications then upload everything into google drive and directly use the darknet from google colab by just calling everything from your drive.
## Generate Train.txt and Test.txt: <br />
  1- Use the script " Dataset-Splitter.py " to split the dataset into train and test. <br />
  2- Place the created Train.txt and Test.txt files into  " /darknet/cfg " .<br />
  3- In this repo in the following lines I will name these files as  person-train.txt and person-test.txt. You can name these whatevet you want.<br />
  
## Prepare cfg Files  <br />
  1- cfg files describe  settings like a number of batch and structure of the model.<br />
  2- We will create our cfg files based on existing  yolov3.cfg.<br />
  3- I create person-frozen.cfg as <strong>training </strong> and person.cfg as <strong> detecting people</strong> in an image. <br />
  4- Copy yolov3.cfg and create person-frozen.cfg.<br />
  5- Then, person- frozen.cfg should be modified.<br />
  6- Modify batch and subdivisions. You might play with this parameters according to your hardware capabilities, here  I use 16 and 4 . <br />
  7- Change max_batches. Darknet YOLO repeats learning for number of max_batches times and so learning is not over if max_batches is too large.The value of      max_batches seems to be number of classes * 2000 [*] . <br />
  8- In this repo, the value of max_batches will be 2000 because the object to detect is one (person) .<br />
  9- I set the value of max_batches to 4000 to repeat learning more.<br />
  10 - Set steps to 80% and 90% of max_batches. ' steps=3200,3600 ' . <br />
  11 - Modify classes and filters. The object to detect is one and so classes=1. Calculate filters=(classes + 5)*number of mask. In yolov3.cfg, number of mask is 3 because mask=6,7,8 is written. Therefore, set filters=18. <br />
  12- In darknet YOLO, you can set which layer is frozen using a parameter stopbackward=1 like the snippet below in person-frozen.cfg file.<br />

   ```
   [shortcut]
from=-3
activation=linear
stopbackward=1 # freeze weights above
######################

[convolutional]
batch_normalize=1
filters=512
size=1
stride=1
pad=1
activation=leaky
```
   13- Let's Prepare the person.cfg.<br />
   14 - The another cfg file is needed when YOLO detects objects with trained weight in a image.<br />
   15-  Copy person-frozen.cfg and create whill.cfg which will be used to detect a object in a image.<br />
   16- Then, person.cfg should be modified<br />
   17- Remove comment out batch and subdivisions just after #Testing.<br />
   18- Add comment out batch and subdivisions just after like the snippet below. #Training.<br /> 
  ```
      [net]
      Testing
      batch=1
      subdivisions=1
      # Training
      # batch=16
      # subdivisions=4
 ```
   19- Move these two cfg files (person-frozen.cfg and person.cfg) into " darknet/cfg " .
   
 ## Create names file <br/>  
   Names of objects to be detected should be written in names file. Create a  person.names and write person inside . (you put whatever you want to detect) . Then, move person.names in "darknet/data" . <br/>
 ## Create data file<br/>
 Create a file like person.data and fill it like the snippet below. Then move this file into  " darknet/cfg " .<br/>
   ```
classes= 1
train  = cfg/person-train.txt
valid  = cfg/person-test.txt
names = data/person.names
backup = backup
 ```
 
 ## Modify Makefile<br/>
   1- To activate the GPU you should change GPU=0 to GPU=1 in the Makefile.<br/>
   2- You might need to change the ARC type since the default value of ARC in the makefile seems to be deprecated.<br/>
   3- In the code snippet below(From original Makefile), You might need to uncomment  <strong> " ARCH= -gencode arch=compute_52,code=compute_52 " </strong><br/>


  ```
GPU=1
CUDNN=1
OPENCV=0
OPENMP=0
DEBUG=0

# choose arch here: https://developer.nvidia.com/cuda-gpus
#ARCH= -gencode arch=compute_20,code=[sm_20,sm_21] \
      -gencode arch=compute_30,code=sm_30 \
      -gencode arch=compute_35,code=sm_35 \
      -gencode arch=compute_50,code=[sm_50,compute_50] \
      -gencode arch=compute_52,code=[sm_52,compute_52]

# Tesla K80
ARCH= -gencode arch=compute_37,code=[sm_37]
 ```
 
  # Start the Training <br/>
  
  --- I have used google colab for this section, I also have placed the notebook which I used in the repo but I recommend you to follow along from here before dive into the notebook.
   ```
%%bash
git clone (the url of the repository which contains the modified darknet repo. Or simply mount to your googdrive and use from there.)
cd darknet
make
 ```
 -- Download the weights.
 ```
!wget https://pjreddie.com/media/files/darknet53.conv.74
 ```
 -- Start Training with the below snippet.
  ```
%%bash
cd ./darknet/
./darknet detector \
        train \
        cfg/person.data \
        cfg/person-frozen.cfg \
        /content/darknet53.conv.74 > /content/log
 ```
 -- Training will take a while. After the training is done save the created weight files into google drive otherwise when the session is over all the weights will disappear.
  ```
%cp /content/darknet/backup/person-* /content/gdrive/My\ Drive/tmp/
 ```
 -- After saving the weight files you can test your model with your new weight file.Notice that this time we use person.cfg file.
  ``` 
 %%bash
cd ./darknet
./darknet detector test cfg/person.data cfg/person.cfg /content/gdrive/My\ Drive/tmp/person-frozen_final.weights /content/gdrive/My\ (Path to the test image) 
  ```
  -- And finally predictions.jpg will be generated. To see the output use the snippet below.
   ``` 
  from IPython.display import Image,display_jpeg
display_jpeg(Image('darknet/predictions.jpg'))
 ``` 
