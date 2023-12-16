# RAPT-replication-package

This repository contains source code that we used to perform experiment in paper titled "RAPT".

Please follow the steps below to reproduce the result.

## Environment Setup

* Python >= 3.8 
* pytorch==1.11.0
* 1 GPU with CUDA 11.3

Run the following command in terminal (or command line) to install python packages.

```
pip install -U pip setuptools 
pip install -r requirement.txt

```

## codeT5 Result Replication Guide

### Train

```bash
cd trace/main
python train_trace_codet5.py \
--data_dir ../data_no/$PROJ_NAME \
--output_dir ./output \
--per_gpu_train_batch_size 8\
--per_gpu_eval_batch_size 8\
--logging_steps 50 \
--gradient_accumulation_steps 1 \
--num_train_epochs 5 \
--learning_rate 4e-5 \
--codet5 ../codet5 \
--hard_ratio 0

```

### Evaluate

```bash
python eval_trace_codet5.py \
    --data_dir ../data_no/$PROJ_NAME \
    --model_path ./output/$PROJ_NAME \
    --codet5 ../codet5 \
    --per_gpu_eval_batch_size 4 

```

## Dataset

The data we used to train and test is attached in the `trace/data`and `trace/data_no` directory. Among them, the data in the trace/data directory considers the reconstruction relationship, and the data in the trace/data_no directory does not consider the reconstruction relationship. You can replace the data with your own. The easiest way to do this is formatting the data into the following csv schema. After formatting the data, you can use the train/eval scripts in to conduct training and evaluatoin.

---

**bug_report:**

bug_report_id: unique id of the NL artifact

bug_report_desc: string of the content

bug_report_time: the time when bug was generated

---

**file_diff:**

file_diff: the actual content of the code file in string, in our case is the code change set

commit_time: the time when the code change set is committed

file_diff_id: the id of the file to which the code change set belongs

---

**link_file:**

bug_report_id: ids from bug_report

file_diff_id: ids from file_diff

---


