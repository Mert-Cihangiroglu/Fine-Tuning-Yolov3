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
  12- In darknet YOLO, you can set which layer is frozen using a parameter stopbackward=1 like the image below in person-frozen.cfg file.<br />

   ![What is this](Frozen%20.png)
   
   <br />
   13- Let's Prepare the person.cfg.<br />
   14 - The another cfg file is needed when YOLO detects objects with trained weight in a image.
   15-  Copy person-frozen.cfg and create whill.cfg which will be used to detect a object in a image.
   16- Then, person.cfg should be modified<br />
   17- Remove comment out batch and subdivisions just after #Testing.<br />
   18- Add comment out batch and subdivisions just after #Training.<br />
   19- [net]
      # Testing
      batch=1
      subdivisions=1
      # Training
      # batch=16
      # subdivisions=4

