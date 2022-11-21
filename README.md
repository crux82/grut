# Introduction
This repository contains the code for the paper **GrUT (Grounded language Understanding via Transformers): Embedding Contextual Information in seq2seq models for Grounded Semantic Role Labeling** published in the **AIxIA 2022** conference by *Claudiu Daniel Hromei* (Tor Vergata, University of Rome; Università Campus Bio-Medico di Roma), *Lorenzo Cristofori* (Tor Vergata, University of Rome), *Danilo Croce* (Tor Vergata, University of Rome) and *Roberto Basili* (Tor Vergata, University of Rome). The paper can be found [here]().  

GrUT is a neural approach for the interpretation of robotic spoken commands that is consistent with (i) the world (with all the entities therein), (ii) the robotic platform (with all its inner representations and capabilities), and (iii) the linguistic information derived from the user’s utterance. It is a sequence-to-sequence method that performs Grounded Semantic Role Labeling in an end-to-end manner, thus avoiding the traditional cascade of interpretation tasks to be solved and effectively linking arguments on the basis of the status and properties of the real-world.  

This code runs the GrUT experiment over a public dataset ([HuRIC 2.1](https://github.com/crux82/huric)) for the Semantic Role Labeling (SRL) task and compares its performances with a simple ready to use model, namely BART, finetuned on the same dataset.  

As a result, we show an error reduction of 26% for the Frame Prediction (FP) task and 23% for the Argument Identification and Classification (AIC) task. You can find more details about the tasks in the paper.


# Create Environment
Create a new environment:  

    conda create --name venv  

And activate it:  

    conda activate venv


# Install Required Lib
You can install them from `requirements.txt` file with:  

    pip install -r requirements.txt  

Then download spacy:  

    python -m spacy download en_core_web_sm  

**NOTE**: for torch, you need python version <= 3.9.x    
**NOTE**: you need specific version of torch based on cuda version (our current version is cuda11):    

    python -m pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 -f https://download.pytorch.org/whl/torch_stable.html


## Other requirements
- HuRIC 2.1 dataset. You can download it manually and put it in `data` folder or use:  

    `git clone https://github.com/crux82/huric.git data`  

- You should use `nohup` or `nodemon` to start your training, as you don't want to wait for it to finish :)  


# How to use GrUT
**You need to download the model first and put it in a subfolder**, in this example we put it in the `model` subfolder.
Based on map description you will get a different interpretation. Imagine there's a book on the table and you want the robot to get it to you, you should use:

    python grut.py predict -i "Take the book on the table # b1 also known as book or volume is a BOOK and t1 also known as table is a TABLE # b1 near t1" -m /model/  

you will get in output:  `Taking(Theme('the book'))`   
If the book is far from the table and you want the robot to move it there, then you should use:

    python grut.py predict -i "Take the book on the table # b1 also known as book or volume is a BOOK and t1 also known as table is a TABLE" -m /model/  

and you will get in output: `Bringing(Theme('the book'), Goal('on the table'))`


### options

    -h : help.
    -m : define path to model.
    -t : define the task.
    -i : input to be predicted.

**Notice** that the input needs to be in the form described in the paper, i.e. with existential and spatial map description prepended to the input and divided by `#`.


# How to train GrUT
This repository provides the code to fine tune 2 models: **GrUT**, that takes a command and the environment description in natural language as input to provide the interpretation, and **BART**, that takes only the command as input to provide the interpretation.
In **bold** you will find the name of the model, as reported in the paper, a brief description and the code to execute the command in order to train it.  

- **BART_base**: BART base, as released by huggingface  

    `nohup python -u main.py train -mn bart -mv base -t SRL -bs 32 -uc True -ep 50 -nf 10 -tt SRL > logs/testing_verbose_with_prints_bart_base.out &`  

- **GRUT**: BART with existential and spatial map description
