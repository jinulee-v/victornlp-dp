# VictorNLP_DP: A multi-model, multi-lingual dependency parsing package.

## Summary

### Dependency Parsing

Dependency Parsing detects the syntactical relationship between words within a sentence, generating a labeled tree. VictorNLP-DP framework supplies various deep learning DP models like Left-To-Right Parser (Fernandez-Gonzalez, 2019), Deep Biaffine Parser (Dozat, 2018) and more. In-depth description of resources and their implementation details are recorded right here:

[VictorNLP_DP Documentation](https://www.notion.so/jinulee/VictorNLP_DP-daa5e56b078647eaa6988a6797be0bd5)

### VictorNLP Framework

VictorNLP, a PyTorch-based NLP framework, provides intuitive interfaces and carefully designed code modularity for implementing and pipelining NLP modules. For more details, refer to the VictorNLP Specification page.

[VictorNLP Specification](https://www.notion.so/VictorNLP-Specification-e03ed18b4a034e3baa16f17793781a90)

## Installation

1. Clone the repository to your local computer.
```
git clone https://github.com/jinulee-v/victornlp_dp
```

2. Install `victornlp_dp package` using `pip`.
```
cd victornlp_dp
mkdir corpus model
pip install -e ./victornlp_dp
```

> Note: All commands in this documentation should be executed in the same directory.
> 
> ls 
>
> corpus model victornlp_dp

3. Install dependencies.

```
pip install -r victornlp_dp/requirements.txt
apt-get install default-jre # For KOMORAN PoS tagger
cd ..
```

## How-to:

### Prepare Datasets and pretrained embeddings

#### Dataset

#### Modu DP corpus (Korean)

Since Modu DP corpus is under protection of NIKL, data is not provided by itself. You may acquire license and download **구문 분석 말뭉치(문어)**, **형태 분석 말뭉치(문어)** and **개체명 인식 말뭉치(문어)**, move them(.json) to `./corpus/` and then execute the following command line. Four files will be generated in the `./corpus/`, namely:
- VictorNLP_kor(Modu)_train.json
- VictorNLP_kor(Modu)_dev.json
- VictorNLP_kor(Modu)_test.json
- VictorNLP_kor(Modu)_labels.json

```
python victornlp_dp/victornlp_utils/corpora/transformatting_modu.py  --file-dir ./corpus --pos (file mame) --dp (file name) --ner (file name)
```

#### Penn TreeBank (English)

You can download the VictorNLP style corpus file from here. Download all 4 files to `./corpus/`.

[Download VictorNLP-style PTB corpus](https://drive.google.com/drive/folders/1GV4uaa95kIIx7mIins2Ld-sxdOwLR2xv?usp=sharing)

> Note: The dataset splits(Train-Val-Test) is brought from [Here](https://github.com/KhalilMrini/LAL-Parser).

#### Pretrained embeddings

Refer to [VictorNLP_Utils documentation](https://github.com/jinulee-v/victornlp_dp).

Add downloaded files to `victornlp_dp/victornlp_utils/data` as instructed.

If the script raises NotFoundError, precautious warnings containing missing dependencies are printed.

### Train model

This script automatically reads `config_file` and starts training. However, for convenience, overriding configuration file with command line arguments is supported. However, to reduce command line bizarreness, we only support major hyper-parameters. Details like model hyperparameters(num_layers, dropout, ...) are likely to be modified directly in `victornlp_dp/config.json` or other config files.

```
python -m victornlp_dp.train
```

Refer to `python -m victornlp_dp.train -h` for more information, and `train.sh` for some demos.

### Fine-tune model

This script also supports fine-tuning for different possible situations:
- Re-train with another corpus.
- Fine-tuning of pre-trained embeddings.
Refer to the following code snippet or `train-finetune.sh` for examples. You can override any cmd line argument-supported hyperparameter just as training, and you can also modify `models/Example_name/config.json` for more precise and specific tuning.

```
python -m victornlp_dp.train --finetune-model models/Example_name
```
> ls models/Example
>
> - model.pt
> - config.json

### Parse & Test model

Use this script to test model stats or parse sentences of interest. You only require `--model-dir` argument which is a directory name that contains `model.pt` and `config.json`. These two files are automatically generated by the `victornlp_dp.train` script. Command line arguments can modify the script's behavior from testing to parsing. Details are given below.

```
python victornlp_dp.parse --model-dir ./model/(folder name)
```

Optional Command line arguments:
- `-a`, `--analyze` : Can freely select which analyzing functions(`victornlp_dp/tools/analyze.py`) to apply. Test set requires golden dependencies.
- `--data-file` : `path/to/input/file`. Load VictorNLP_format .json file to parse. If not provided, parses `stdin` inputs(line by line, PoS automatically tagged).
- `--save-result` : `path/to/store/parsed_file.json`. Stores parsed result in the designated path.