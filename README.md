# Create Environment
Create the environment with python and activate it:  
    `python -m venv venv`  
    `venv\Scripts\activate`  
or with Anaconda:  
    `conda create --name venv`  
    `conda activate venv`  


# Install Required Lib
You can install them from `requirements.txt` file with:  
    `python -m pip install -r requirements.txt`  
or manually:  
    - pip install pandas  
    - pip install sklearn  
    - pip install torch  
    - pip install simpletransformers  
    - pip install xlsxwriter  
    - pip install nltk  
    - pip install spacy  
Then download spacy:  
    - python -m spacy download en_core_web_sm  
    - python -m spacy download it_core_news_sm  

**NOTE**: for torch, you need python version <= 3.9.x    
**NOTE**: you need specific version of torch based on cuda version (our actual version is cuda11):    
    `python -m pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 -f https://download.pytorch.org/whl/torch_stable.html`  

### if any error try:
    pip install simpletransformers --use-feature=2020-resolver  
### or
    pip install pandas, sklearn, torch, xlsxwriter, simpletransformers --use-feature=2020-resolver  

# Other requirements
- HuRIC 2.1 dataset. You can download it manually and put it in `data` folder or use:  
    `git clone https://github.com/crux82/huric.git data`  
- You should use `nohup` or `nodemon` to start your training, as you don't want to wait for it to finish :)  

# How to train!
In **bold** you will find the name of the model as reported in the paper and as code the command to execute in order to train it.  
- **BART_base**: BART base, as released by huggingface  
    `nohup python -u main.py train -mn bart -mv base -t SRL -bs 32 -uc True -ep 50 -nf 10 -tt SRL > logs/testing_verbose_with_prints_bart_base.out &`  

- **GRUT**: BART with existential and spatial map description  
    `nohup python -u main.py train -mn bart -mv base -t SRL -bs 32 -uc True -ep 50 -nf 10 -tt SRL -gr half -map lmd > logs/testing_verbose_with_prints_grut.out &`  

- **GRUT_LU**: BART with existential and spatial map description and Frames evoked by every single noun  
    `nohup python -u main.py train -mn bart -mv base -t SRL -bs 32 -uc True -ep 50 -nf 10 -tt SRL -gr half -map lmd -alut True > logs/testing_verbose_with_prints_grut_lu.out &`  

### Options
    -h : help.
    -nf : define numbers of fold in kfold. Only if evaluation mode is True.
    -uc : define if use GPU True/False.
    -ep : define numbers of epochs for training.
    -t  : define task type: FP(Frame Prediction), BD(Boundary Detection), AC(Argument Classification), SRL (default).
    -tt : define type of target manipulation: frame, frame+pos, frame+token, frame+sentence, SRL(default).
    -es : define if using early stopping considering epoch during training or not.
    -mn : define model name: bart (default), t5, mt5.
    -mv : define model size: small, base, large, ecc.
    -am : define whether to add map description or not, default False.

# Results
Results will be written inside `./model/<model_name>`.   
For example: if you want to train *GrUT* (name as in the paper, which means it will be trained with the lexicalized map description in input, will use the W2V retrieval method and it will perform Semantic Role Labling), <model_name> will be `bart_en_stm_lmd_halfgrounding_W2V_allLexicalReferences_<TIMESTAMP>` based on the options described here. There you will find, among others, 3 files:  
- `results_unified.xlsx` containing test set sentences, with predictions and gold standard (truth)  
- `frames_CM_unified.txt` containing confusion matrix for frames only (Frame Prediction task), merged for all X folds  
- `frame_elements_CM_unified.txt` containing confusion matrix for frame elements (arguments and types for AIC task), merged for all X folds  


# How to start predict mode
    python main.py predict
### options
    -h : help.
    -m : define path to model.
    -t : define the task.
    -i : input to be predicted.
**Notice** that the input needs to be in the form described in the paper, i.e. with existential and spatial map description prepended to the input and divided by `#`.
