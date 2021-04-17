# VictorNLP_DP: A multi-model, multi-lingual dependency parsing package.

## Summary

### Dependency Parsing

Dependency Parsing detects the syntactical relationship between words within a sentence, generating a labeled tree. VictorNLP-DP framework supplies various deep learning DP models like Left-To-Right Parser (Fernandez-Gonzalez, 2019), Deep Biaffine Parser (Dozat, 2018) and more. In-depth description of resources and their implementation details are recorded right here:

[VictorNLP_DP Technical Documentation](https://www.notion.so/jinulee/VictorNLP_DP-daa5e56b078647eaa6988a6797be0bd5)

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

> Note: All commands in this documentation should be executed as the same directory `.` (`.../path/to/repo/victornlp_dp`).

3. Install dependencies.

```
pip install -r victornlp_dp/requirements.txt
apt-get install default-jre # For KOMORAN PoS tagger
cd ..
```

## How-to:

### Prepare Datasets and pretrained embeddings

#### Corpus

- Modu DP corpus (Korean)

Since Modu DP corpus is under protection of NIKL, data is not provided by itself. You may acquire license and download **구문 분석 말뭉치(문어)**, **형태 분석 말뭉치(문어)** and **개체명 인식 말뭉치(문어)**, move them(.json) to `./corpus/` and rename as:
```
cd corpus
mv NXMP*.json Modu_PoS_raw.json
mv NXDP*.json Modu_DP_raw.json
mv NXNE*.json Modu_NER_raw.json
cd ..
```
, and then execute the following command line.

```
python victornlp_dp/victornlp_utils/corpora/transformatting_modu.py 
```

- Penn TreeBank (English)

You can download the VictorNLP style corpus file from here. Download all 4 files to `./corpus/`.

[Download VictorNLP-style PTB corpus](https://drive.google.com/drive/folders/1GV4uaa95kIIx7mIins2Ld-sxdOwLR2xv?usp=sharing)

> Note: The dataset splits(Train-Val-Test) is brought from [Here](https://github.com/KhalilMrini/LAL-Parser).

#### Pretrained embeddings

Refer to [VictorNLP_Utils documentation](https://github.com/jinulee-v/victornlp_dp).

Add downloaded files to `victornlp_dp/victornlp_utils/data` as instructed.

### Train model

This script automatically reads `config_file` and starts training. However, for convenience, overriding configuration file(only `"train"` section) with command line arguments is supported.

```
python victornlp_dp.train config_file(default: victornlp_dp/config_DependencyParsing.json)
```
Optional Command line arguments:
- `--model` : LeftToRightParser, DeepBiaffineParser
- `--language`: English, Korean
- `--epoch`: integer
- `--batch_size`: integer
- `--loss_fn`: loss_CE, loss_XBCE, loss_LH
- `--parse_fn`: parse_beam, parse_MST
- `--optimizer`: Any optimizer class from `torch.optim`
- `--learning_rate`: float
- `--device`: argument for `torch.device()`. Rather `cpu` or `cuda:N`(N: GPU ID).

### Parse & Test model

Use this script to test model stats or parse sentences of interest. You only require `--model-dir` argument which is a directory name that contains `model.pt` and `config.json`. These two files are automatically generated by the `victornlp_dp.train` script. Command line arguments can modify the script's behavior from testing to parsing. Details are given below.

```
python victornlp_dp.parse --model-dir ./model/(folder name)
```

Optional Command line arguments:
- `-a`, `--analyze` : `analyze_*`. Can freely select which analyzing functions(`victornlp_dp/tools/analyze.py`) to apply. 
- `--data-file` : `path/to/input/file`. Load VictorNLP_format .json file to parse. If not provided, parses `stdin` inputs(line by line, PoS automatically tagged).
- `--save-result` : `path/to/store/parsed_file.json`. Stores parsed result in the designated path.