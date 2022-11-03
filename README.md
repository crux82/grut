# Create Environment
    python3 -m venv venv
### or use 
    python -m venv venv

# Activate
    venv\Scripts\activate

# Install Required Lib
You can install them from `requirements.txt` file or manually:
    pip install pandas
    pip install sklearn
    pip install torch
    pip install simpletransformers
    pip install xlsxwriter
    pip install nltk
    pip install spacy
    python -m spacy download en_core_web_sm
    python -m spacy download it_core_news_sm

NOTE: for torch, you need python version <= 3.9.x  
NOTE: you need specific version of torch based on cuda version (our actual version is cuda11):  
    `python -m pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 -f https://download.pytorch.org/whl/torch_stable.html`

## if any error try:
    pip install simpletransformers --use-feature=2020-resolver
## or
    pip install pandas, sklearn, torch, xlsxwriter, simpletransformers --use-feature=2020-resolver

# Other requirements
You should use `nohup` or `nodemon` to start your training, as you don't want to wait for it to finish :)

# How to train!
In **bold** you will find the name of the model as reported in the paper.
- **bart_mtm**: BART multitask model with language spatial map description:  
`nohup python -u main.py train -mn bart -mv base -t SRL -bs 16 -uc True -ep 50 -nf 10 -tt SRL -mtm True -lmd True -am True > logs/bart_mtm_lmd.out &`
- **bart_stm**: BART singletask model with language spatial map description:  
`nohup python -u main.py train -mn bart -mv base -t SRL -bs 16 -uc True -ep 50 -nf 10 -tt SRL -lmd True -am True > logs/bart_stm_lmd.out &`
- **bart_base**: BART base, as released by huggingface:  
`nohup python -u main.py train -mn bart -mv base -t SRL -bs 16 -uc True -ep 50 -nf 10 -tt SRL > logs/bart_base.out &`

### options
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
Results will be written inside `./model/<model_name>`. For example: if you want to train *GrUT* (name as in the paper), <model_name> will be `bart_en_mtm_lmd_<TIMESTAMP>`. There you will find 3 files:
- `results_unified.xlsx` containing test set sentences, with predictions and gold standard (truth)
- `frames_CM_unified.txt` containing confusion matrix for frames only (Frame Prediction task), merged for all X folds
- `frame_elements_CM_unified.txt` containing confusion matrix for frame elements (arguments and types for AIC task), merged for all X folds


# How to start predict mode
    python main.py predict
### options
    -h : help.
    -m : define path to model.
    -t : define the task.
    -i : insert the text to be predict.
