# Contents

- [Contents](#contents)
    - [FCN-4 Description](#fcn-4-description)
    - [Model Architecture](#model-architecture)
    - [Features](#features)
        - [Mixed Precision](#mixed-precision)
    - [Environment Requirements](#environment-requirements)
    - [Quick Start](#quick-start)
        - [1. Download and preprocess the dataset](#1-download-and-preprocess-the-dataset)
        - [2. setup parameters (src/model_utils/default_config.yaml)](#2-setup-parameters-srcmodel_utilsdefault_configyaml)
        - [3. Train](#3-train)
        - [4. Test](#4-test)
    - [Script Description](#script-description)
        - [Script and Sample Code](#script-and-sample-code)
        - [Script Parameters](#script-parameters)
        - [Training Process](#training-process)
            - [Training](#training)
        - [Evaluation Process](#evaluation-process)
            - [Evaluation](#evaluation)
    - [Inference Process](#inference-process)
        - [Export MindIR](#export-mindir)
        - [Infer on Ascend310](#infer-on-ascend310)
        - [result](#result)
    - [Model Description](#model-description)
        - [Performance](#performance)
            - [Evaluation Performance](#evaluation-performance)
    - [ModelZoo Homepage](#modelzoo-homepage)

## [FCN-4 Description](#contents)

This repository provides a script and recipe to train the FCN-4 model to achieve state-of-the-art accuracy.

[Paper](https://arxiv.org/abs/1606.00298):  `"Keunwoo Choi, George Fazekas, and Mark Sandler, “Automatic tagging using deep convolutional neural networks,” in International Society of Music Information Retrieval Conference. ISMIR, 2016."

## [Model Architecture](#contents)

FCN-4 is a convolutional neural network architecture, its name FCN-4 comes from the fact that it has 4 layers. Its layers consists of Convolutional layers, Max Pooling layers, Activation layers, Fully connected layers.

## [Features](#contents)

### Mixed Precision

The [mixed precision](https://www.mindspore.cn/tutorials/en/master/beginner/mixed_precision.html) training method accelerates the deep learning neural network training process by using both the single-precision and half-precision data
formats, and maintains the network precision achieved by the single-precision training at the same time. Mixed precision training can accelerate the computation process, reduce memory usage, and enable a larger model or batch size to be
trained on specific hardware.
For FP16 operators, if the input data type is FP32, the backend of MindSpore will automatically handle it with reduced precision. Users could check the reduced-precision operators by enabling INFO log and then searching ‘reduce precision’.

## [Environment Requirements](#contents)

- Hardware（Ascend)
    - Prepare hardware environment with Ascend processor.
- Framework
    - [MindSpore](https://www.mindspore.cn/install/en)
- For more information, please check the resources below：
    - [MindSpore tutorials](https://www.mindspore.cn/tutorials/en/master/index.html)
    - [MindSpore Python API](https://www.mindspore.cn/docs/en/master/api_python/mindspore.html)

## [Quick Start](#contents)

After installing MindSpore via the official website, you can start training and evaluation as follows:

### 1. Download and preprocess the dataset

1. down load the classification dataset (for instance, MagnaTagATune Dataset, link: https://mirg.city.ac.uk/codeapps/the-magnatagatune-dataset, etc)
2. Extract the dataset
3. The information file of each clip should contain the label and path. Please refer to the annotations_final.csv in MagnaTagATune Dataset.
4. The provided pre-processing script use MagnaTagATune Dataset as an example. Please modify the code accprding to your own need.

### 2. setup parameters (src/model_utils/default_config.yaml)

### 3. Train

after having your dataset, first convert the audio clip into mindrecord dataset by using the following codes

```shell
python src/pre_process_data.py --device_id 0
```

Then, you can start training the model by using the following codes

```shell
SLOG_PRINT_TO_STDOUT=1 python train.py --device_id 0
```

### 4. Test

Then you can test your model

```shell
SLOG_PRINT_TO_STDOUT=1 python eval.py --device_id 0
```

- Running on [ModelArts](https://support.huaweicloud.com/modelarts/)

    ```bash
    # Train 8p with Ascend
    # (1) Perform a or b.
    #       a. Set "enable_modelarts=True" on default_config.yaml file.
    #          Set "distribute=True" on default_config.yaml file.
    #          Set "data_dir='/cache/data'" on default_config.yaml file.
    #          Set "checkpoint_path='/cache/data/musicTagger'" on default_config.yaml file.
    #          (optional)Set "checkpoint_url='s3://dir_to_your_pretrained/'" on default_config.yaml file.
    #          Set other parameters on default_config.yaml file you need.
    #       b. Add "enable_modelarts=True" on the website UI interface.
    #          Add "distribute=True" on the website UI interface.
    #          Add "data_dir=/cache/data" on the website UI interface.
    #          Add "checkpoint_path='/cache/data/musicTagger'" on the website UI interface.
    #          (optional)Add "checkpoint_url='s3://dir_to_your_pretrained/'" on the website UI interface.
    #          Add other parameters on the website UI interface.
    # (3) Upload or copy your pretrained model to S3 bucket if you want to finetune.
    # (4) Upload the original MusicTagger dataset to S3 bucket.
    # (5) Set the code directory to "/path/fcn-4" on the website UI interface.
    # (6) Set the startup file to "train.py" on the website UI interface.
    # (7) Set the "Dataset path" and "Output file path" and "Job log path" to your path on the website UI interface.
    # (8) Create your job.
    #
    # Train 1p with Ascend
    # (1) Perform a or b.
    #       a. Set "enable_modelarts=True" on default_config.yaml file.
    #          Set "data_dir='/cache/data'" on default_config.yaml file.
    #          Set "checkpoint_path='/cache/data/musicTagger'" on default_config.yaml file.
    #          (optional)Set "checkpoint_url='s3://dir_to_your_pretrained/'" on default_config.yaml file.
    #          Set other parameters on default_config.yaml file you need.
    #       b. Add "enable_modelarts=True" on the website UI interface.
    #          Add "data_dir='/cache/data'" on the website UI interface.
    #          Add "checkpoint_path='/cache/data/musicTagger'" on the website UI interface.
    #          (optional)Add "checkpoint_url='s3://dir_to_your_pretrained/'" on the website UI interface.
    #          Add other parameters on the website UI interface.
    # (3) Upload or copy your pretrained model to S3 bucket if you want to finetune.
    # (4) Upload the original MusicTagger dataset to S3 bucket.
    # (5) Set the code directory to "/path/fcn-4" on the website UI interface.
    # (6) Set the startup file to "train.py" on the website UI interface.
    # (7) Set the "Dataset path" and "Output file path" and "Job log path" to your path on the website UI interface.
    # (8) Create your job.
    #
    # Eval 1p with Ascend
    # (1) Perform a or b.
    #       a. Set "enable_modelarts=True" on default_config.yaml file.
    #          Set "data_dir='/cache/data'" on default_config.yaml file.
    #          Set "checkpoint_path='/cache/data/musicTagger'" on default_config.yaml file.
    #          Set other parameters on default_config.yaml file you need.
    #       b. Add "enable_modelarts=True" on the website UI interface.
    #          Add "data_dir='/cache/data'" on the website UI interface.
    #          Add "checkpoint_path='/cache/data/musicTagger'" on the website UI interface.
    #          Add other parameters on the website UI interface.
    # (3) Upload or copy your trained model to S3 bucket.
    # (4) Upload the original MusicTagger dataset to S3 bucket.
    # (5) Set the code directory to "/path/fcn-4" on the website UI interface.
    # (6) Set the startup file to "eval.py" on the website UI interface.
    # (7) Set the "Dataset path" and "Output file path" and "Job log path" to your path on the website UI interface.
    # (8) Create your job.
    ```

- Export on ModelArts (If you want to run in modelarts, please check the official documentation of [modelarts](https://support.huaweicloud.com/modelarts/), and you can start evaluating as follows)

1. Export s8 multiscale and flip with voc val dataset on modelarts, evaluating steps are as follows:

    ```python
    # (1) Perform a or b.
    #       a. Set "enable_modelarts=True" on base_config.yaml file.
    #          Set "file_name='fcn-4'" on base_config.yaml file.
    #          Set "file_format='MINDIR'" on base_config.yaml file.
    #          Set "checkpoint_url='/The path of checkpoint in S3/'" on beta_config.yaml file.
    #          Set "ckpt_file='/cache/checkpoint_path/model.ckpt'" on base_config.yaml file.
    #          Set other parameters on base_config.yaml file you need.
    #       b. Add "enable_modelarts=True" on the website UI interface.
    #          Add "file_name='fcn-4'" on the website UI interface.
    #          Add "file_format='MINDIR'" on the website UI interface.
    #          Add "checkpoint_url='/The path of checkpoint in S3/'" on the website UI interface.
    #          Add "ckpt_file='/cache/checkpoint_path/model.ckpt'" on the website UI interface.
    #          Add other parameters on the website UI interface.
    # (2) Upload or copy your trained model to S3 bucket.
    # (3) Set the code directory to "/path/fcn-4" on the website UI interface.
    # (4) Set the startup file to "export.py" on the website UI interface.
    # (5) Set the "Dataset path" and "Output file path" and "Job log path" to your path on the website UI interface.
    # (6) Create your job.
    ```

## [Script Description](#contents)

### [Script and Sample Code](#contents)

```shell
├── model_zoo
    ├── README.md                          // descriptions about all the models
    ├── music_auto_tagging
        ├── README.md                    // descriptions about googlenet
        ├── scripts
        │   ├──run_train.sh             // shell script for distributed on Ascend
        │   ├──run_eval.sh              // shell script for evaluation on Ascend
        │   ├──run_process_data.sh      // shell script for convert audio clips to mindrecord
        │   ├──run_train_gpu.sh         // shell script for distributed on GPU
        │   ├──run_eval_gpu.sh          // shell script for evaluation on GPU
        ├── src
        │   ├──dataset.py                     // creating dataset
        │   ├──pre_process_data.py            // pre-process dataset
        │   ├──musictagger.py                 // googlenet architecture
        │   ├──loss.py                        // loss function
        │   ├──tag.txt                        // tag for each number
        |   └─model_utils
        |     ├─config.py          // Processing configuration parameters
        |     ├─device_adapter.py  // Get cloud ID
        |     ├─local_adapter.py   // Get local ID
        |     └─moxing_adapter.py  // Parameter processing
        ├── train.py               // training script
        ├── eval.py                //  evaluation script
        ├── export.py              //  export model in air/mindir format
        ├─default_config.yaml      // Training parameter profile
        └─train.py                 // Train net
```

### [Script Parameters](#contents)

Parameters for both training and evaluation can be set in default_config.yaml

- config for FCN-4

```text

  'num_classes': 50                      # number of tagging classes
  'num_consumer': 4                      # file number for mindrecord
  'get_npy': 1 # mode for converting to npy, default 1 in this case
  'get_mindrecord': 1 # mode for converting npy file into mindrecord file，default 1 in this case
  'audio_path': "/dev/data/Music_Tagger_Data/fea/" # path to audio clips
  'npy_path': "/dev/data/Music_Tagger_Data/fea/" # path to numpy
  'info_path': "/dev/data/Music_Tagger_Data/fea/" # path to info_name, which provide the label of each audio clips
  'info_name': 'annotations_final.csv'   # info_name
  'device_target': 'Ascend'              # device running the program
  'device_id': 0                         # device ID used to train or evaluate the dataset. Ignore it when you use run_train.sh for distributed training
  'mr_path': '/dev/data/Music_Tagger_Data/fea/' # path to mindrecord
  'mr_name': ['train', 'val']            # mindrecord name

  'pre_trained': False                   # whether training based on the pre-trained model
  'lr': 0.0005                           # learning rate
  'batch_size': 32                       # training batch size
  'epoch_size': 10                       # total training epochs
  'loss_scale': 1024.0                   # loss scale
  'num_consumer': 4                      # file number for mindrecord
  'mixed_precision': False               # if use mix precision calculation
  'train_filename': 'train.mindrecord0'  # file name of the train mindrecord data
  'val_filename': 'val.mindrecord0'      # file name of the evaluation mindrecord data
  'data_dir': '/dev/data/Music_Tagger_Data/fea/' # directory of mindrecord data
  'device_target': 'Ascend'              # device running the program
  'device_id': 0,                        # device ID used to train or evaluate the dataset. Ignore it when you use run_train.sh for distributed training
  'keep_checkpoint_max': 10,             # only keep the last keep_checkpoint_max checkpoint
  'save_step': 2000,                     # steps for saving checkpoint
  'checkpoint_path': '/dev/data/Music_Tagger_Data/model/',  # the absolute full path to save the checkpoint file
  'prefix': 'MusicTagger',               # prefix of checkpoint
  'model_name': 'MusicTagger_3-50_543.ckpt', # checkpoint name
```

### [Training Process](#contents)

#### Training

- running on Ascend

  ```shell
  python train.py --device_target Ascend > train.log 2>&1 &
  ```

- running on GPU

  ```shell
  python train.py --device_target GPU --data_dir [dataset dir path]  --checkpoint_path [chekpoint save dir]  > train.log 2>&1 &
  ```

  The python command above will run in the background, you can view the results through the file `train.log`.

  After training, you'll get some checkpoint files under the script folder by default. The loss value will be achieved as follows:

  ```shell
  # grep "loss is " train.log
  epoch: 1 step: 100, loss is 0.23264095
  epoch: 1 step: 200, loss is 0.2013525
  ...
  ```

  The model checkpoint will be saved in the set directory.

### [Evaluation Process](#contents)

#### Evaluation

## Inference Process

**Before inference, please refer to [MindSpore Inference with C++ Deployment Guide](https://gitee.com/mindspore/models/blob/master/utils/cpp_infer/README.md) to set environment variables.**

### [Export MindIR](#contents)

```shell
python export.py --ckpt_file [CKPT_PATH] --file_name [FILE_NAME] --file_format [FILE_FORMAT]
```

The ckpt_file parameter is required,
`FILE_FORMAT` should be in ["AIR", "ONNX", "MINDIR"]

### Infer on Ascend310

Before performing inference, the mindir file must be exported by `export.py` script. We only provide an example of inference using MINDIR model.

```shell
# Ascend310 inference
bash run_infer_310.sh [MINDIR_PATH] [NEED_PREPROCESS] [DEVICE_ID]
```

- `NEED_PREPROCESS` means weather need preprocess or not, it's value is 'y' or 'n'.
- `DEVICE_ID` is optional, default value is 0.

### result

Inference result is saved in current path, you can find result like this in acc.log file.

```bash
AUC: 0.90995
```

## [Model Description](#contents)

### [Performance](#contents)

#### Evaluation Performance

| Parameters                 | Ascend                                                                                           | GPU                                            |
|----------------------------|--------------------------------------------------------------------------------------------------|------------------------------------------------|
| Model Version              | FCN-4                                                                                            | FCN-4                                          |
| Resource                   | Ascend 910; CPU 2.60GHz, 56cores; Memory 314G; OS Euler2.8                                       | Tesla V100-PICE-32G                            |
| uploaded Date              | 07/05/2021 (month/day/year)                                                                      | 07/26/2021 (month/day/year)                    |
| MindSpore Version          | 1.3.0                                                                                            | 1.3.0                                          |
| Training Parameters        | epoch=10, steps=534, batch_size = 32, lr=0.005                                                   | epoch=10, steps=534, batch_size = 32, lr=0.005 |
| Optimizer                  | Adam                                                                                             | Adam                                           |
| Loss Function              | Binary cross entropy                                                                             | Binary cross entropy                           |
| outputs                    | probability                                                                                      | probability                                    |
| Loss                       | AUC 0.909                                                                                        | AUC 0.909                                      |
| Speed                      | 1pc: 160 samples/sec;                                                                            | 1pc: 160 samples/sec;                          |
| Total time                 | 1pc: 20 mins;                                                                                    | 1pc: 20 mins;                                  |
| Checkpoint for Fine tuning | 198.73M(.ckpt file)                                                                              | 198.73M(.ckpt file)                            |
| Scripts                    | [music_auto_tagging script](https://gitee.com/mindspore/models/tree/master/research/audio/fcn-4) |

## [ModelZoo Homepage](#contents)

Please check the official [homepage](https://gitee.com/mindspore/models).

## IRMAS dataset

### checkpoint

You can download the trained model from [link](https://pan.baidu.com/s/14GbW2syH-4B0uWD8HpCsGw?pwd=cd7t) to validate it, and the directory tree is `./checkpoint/*/*.ckpt`.

### download

1. Download this [url](https://zenodo.org/records/1290750#.WzCwSRyxXMU) and in this experiment, only the `IRMAS-TrainingData.zip` file was used, which was unzipped to `./data/IRMAS/train` directory.
2. The dataset was processed to form the following directory tree structure:

```text
--data
    |--IRMAS
        |--train/*/*.wav
```

3. Run `src/pre_process_data_IRMAS.py` for data preprocessing, during which 80% of the data will be used as the training set and 20% as the test set for classification into 18 categories.

```bash
python src/configs/pre_process_data_IRMAS.py --device_id=5
```

```text
--data
    |--IRMAS
        |--train/*/*.wav
        |--npy/*/*.npy
        |--mr/*.mindrecord*
```

### train

```bash
python train.py --config_path='./configs/IRMAS_config.yaml' --device_id=5
```

### validation

```bash
python eval.py --config_path='./configs/IRMAS_config.yaml' --device_id=5
```

```text
AUC: 0.93674
```

## FSDKaggle2018 dataset

### checkpoint

You can download the trained model from [link](https://pan.baidu.com/s/14GbW2syH-4B0uWD8HpCsGw?pwd=cd7t) to validate, and the directory tree is `./checkpoint/*/*.ckpt`.

### download

1. Download all the data from [this link](https://zenodo.org/records/2552860#.Xk6Xgi2ZNPU) and place it in the `FSDKaggle2018` folder.
2. Unzip the data set and organize it into the following directory tree structure for further data preprocessing:

```text
--data
    |--FSDKaggle2018
        |--FSDKaggle2018.doc
        |--FSDKaggle2018.meta/*.csv
        |--FSDKaggle2018.audio_train/*.wav
        |--FSDKaggle2018.audio_test/*.wav
```

3. Run the following code for data preprocessing:

```bash
python src/dataset_utils/pre_process_data_IRMAS.py --device_id=5
```

```text
--data
    |--FSDKaggle2018
        |--FSDKaggle2018.doc
        |--FSDKaggle2018.meta/*.csv
        |--FSDKaggle2018.audio_train/*.wav
        |--FSDKaggle2018.audio_test/*.wav
        |--npy/*/*.npy
        |--mr/*.mindrecord*
```

### train

```bash
python train.py --config_path='./configs/FSDKaggle2018_config.yaml' --device_id=5
```

### validation

```bash
python eval.py --config_path='./configs/FSDKaggle2018_config.yaml' --device_id=5
```

```text
AUC: 0.97949
```

## EmoSpeech dataset

### checkpoint

You can download the trained model from [link](https://pan.baidu.com/s/14GbW2syH-4B0uWD8HpCsGw?pwd=cd7t) to validate, and the directory tree is `./checkpoint/*/*.ckpt`.

### download

1. Download all the data from [this link](https://www.kaggle.com/datasets/ujjwal9/emospeech) and place it in the `EmoSpeech` folder.
2. Unzip the data set and organize it into the following directory tree structure for further data preprocessing:

```text
--data
    |--emospeech
        |--data
            |--House/*.wav
            |--Metro/*.wav
            |--Misc/*.wav
            |--Near_Lake/*.wav
            |--Railway/*.wav
            |--webservice/*.wav
```

3. Run the following code for data preprocessing:

```bash
python src/dataset_utils/pre_process_data_EmoSpeech.py --device_id=5
```

``` text
--data
    |--emospeech
        |--data
            |--House/*.wav
            |--Metro/*.wav
            |--Misc/*.wav
            |--Near_Lake/*.wav
            |--Railway/*.wav
            |--webservice/*.wav
        |--npy/*/*.npy
        |--mr/*.mindrecord*
```

### train

```bash
python train.py --config_path='./configs/EmoSpeech_config.yaml' --device_id=5
```

### validation

```bash
python eval.py --config_path='./configs/EmoSpeech_config.yaml' --device_id=5
```

```text
AUC: 0.94081
```


## ESC-50 dataset

### checkpoint

You can download the trained model from [link](https://pan.baidu.com/s/14GbW2syH-4B0uWD8HpCsGw?pwd=cd7t) to validate, and the directory tree is `./checkpoint/*/*.ckpt`.

### download

1. Download all the data from [this link](https://www.kaggle.com/datasets/niranjankn/esc50) and place it in the `esc50` folder.
2. Unzip the data set and organize it into the following directory tree structure for further data preprocessing:

```text
--data
    |--esc50
        |--audio/*.wav
        |--meta/esc50.csv
```

3. Run the following code for data preprocessing:

```bash
python src/dataset_utils/pre_process_data_ESC-50.py --device_id=5
```

```text
--data
    |--esc50
        |--audio/*.wav
        |--meta/esc50.csv
        |--npy/*.npy
        |--mr/*.mindrecord*
```

### train

```bash
python train.py --config_path='./configs/ESC-50_config.yaml' --device_id=5
```

### validation

```bash
python eval.py --config_path='./configs/ESC-50_config.yaml' --device_id=5
```

```text

```

## UrbanSound8K dataset

### checkpoint

You can download the trained model from [link](https://pan.baidu.com/s/14GbW2syH-4B0uWD8HpCsGw?pwd=cd7t) to validate, and the directory tree is `./checkpoint/*/*.ckpt`.

### download

1. Download all the data from [this link](https://www.kaggle.com/datasets/chrisfilo/urbansound8k) and place it in the `urbansound8k` folder.
2. Unzip the data set and organize it into the following directory tree structure for further data preprocessing:

```text
--data
    |--urbansound8k
        |--fold*/*.wav
        |--UrbanSound8K.csv
```

3. Run the following code for data preprocessing:

```bash
python src/dataset_utils/pre_process_data_UrbanSound8K.py --device_id=5
```

```text
--data
    |--urbansound8k
        |--fold*/*.wav
        |--UrbanSound8K.csv
        |--npy/*/*.npy
        |--mr/*.mindrecord*
```

### train

```bash
python train.py --config_path='./configs/UrbanSound8K_config.yaml' --device_id=5
```

### validation

```bash
python eval.py --config_path='./configs/UrbanSound8K_config.yaml' --device_id=5
```

```text
AUC: 0.98497
```