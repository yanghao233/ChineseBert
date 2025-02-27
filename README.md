# OntoNotes4.0数据集复现


# OntoNotes NER task 
OntoNotes 4.0 is a Chinese named entity recognition dataset and contains 18 named entity types. 
OntoNotes 4.0 contains 15K/4K/4K instances for training/dev/test. <br>

## Dataset
The OntoNotes 4.0 NER dataset using BMES tagging schema can be find [HERE](https://drive.google.com/file/d/1qH4NDMRYRZYX9CWPLyF5KQLTpX-vvm39/view?usp=sharing)  
Download the corpus and save data at `[ONTONOTES_DATA_PATH]`

## Train and Evaluate
For reproducing experiment results, please **install and use** `torch1.7.1+cu101` via `pip install torch==1.7.1+cu101 torchvision==0.8.2+cu101 torchaudio==0.7.2 -f https://download.pytorch.org/whl/torch_stable.html`. <br>
Download ChineseBERT model and save at `[CHINESEBERT_PATH]`.  
Run the following scripts to train and evaluate. <br>
For baseline models including [BERT](https://storage.googleapis.com/bert_models/2018_11_03/chinese_L-12_H-768_A-12.zip), [RoBERTa](https://drive.google.com/open?id=1eHM3l4fMo6DsQYGmey7UZGiTmQquHw25) and [RoBERTa-Large](https://drive.google.com/open?id=1-2vEZfIFCdM1-vJ3GD6DlSyKT4eVXMKq), please see [bert.sh](../baseline/ontonotes4/bert.sh), [roberta.sh](../baseline/ontonotes4/roberta.sh) and [roberta_large.sh](../baseline/ontonotes4/roberta_large.sh), respectively. <br> 

For ChineseBERT-Base (see [chinesebert_base.sh](./chinesebert_base.sh)), 

```bash 
CUDA_VISIBLE_DEVICES=0 python3 $REPO_PATH/tasks/OntoNotes/OntoNotes_trainer.py \
--lr 3e-5 \
--max_epochs 5 \
--max_length 275 \
--weight_decay 0.001 \
--hidden_dropout_prob 0.2 \
--warmup_proportion 0.1  \
--train_batch_size 26 \
--accumulate_grad_batches 1 \
--save_topk 20 \
--val_check_interval 0.25 \
--gpus="1" \
--precision=16 \
--optimizer torch.adam \
--classifier multi \
--bert_path [CHINESEBERT_PATH] \
--data_dir [ONTONOTES_DATA_PATH] \
--save_path [OUTPUT_PATH] 
```

For ChineseBERT-Large (see [chinesebert_large.sh](./chinesebert_large.sh)), 

```bash 
CUDA_VISIBLE_DEVICES=1 python3 $REPO_PATH/tasks/OntoNotes/OntoNotes_trainer.py \
--lr 3e-5 \
--max_epochs 5 \
--max_length 275 \
--weight_decay 0.002 \
--hidden_dropout_prob 0.2 \
--warmup_proportion 0.1 \
--train_batch_size 18 \
--accumulate_grad_batches 2 \
--save_topk 20 \
--val_check_interval 0.25 \
--gpus="1" \
--precision=16 \
--optimizer torch.adam \
--classifier multi \
--bert_path [CHINESEBERT_PATH] \
--data_dir [ONTONOTES_DATA_PATH] \
--save_path [OUTPUT_PATH] 
```

## Result
The evaluation metric is Span-Level F1. 
Result of our model and previous models are:

base model: 
| Model  |  Test Precision |  Test Recall |  Test F1 |  
|  ----  | ----  | ----  | ----  |
| BERT | 79.69 | 82.09 | 80.87 | 
| RoBERTa |  80.43 | 80.30 |  80.37 | 
| ChineseBERT | 80.03 | 83.33 | 81.65 |
| 复现数据 | 78.89 | 84.99 | 81.83 | 

