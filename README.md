# RAPT-replication-package

This repository contains source code that we used to perform experiment in paper titled "A Refactoring-Aware Pre-Trained Model for Bug Localization".

The structure of this folder is as follows：

- RAPT: Contains the source code and dataset to reproduce the result of RAPT model.
- CodeT5: Contains the source code and dataset to reproduce the result of CodeT5 baseline.
- GPT2: Contains the source code and dataset to reproduce the result of GPT2 baseline.
- RAT: Contains the source code and dataset to reproduce the result of RAT.
- Tracescore: Contains the source code and dataset to reproduce the result of Tracescore.

## Environment Requirements

* Python >= 3.8 
* pytorch==1.11.0
* 1 GPU with CUDA 11.3
* Java Development Kit >= 17
* Maven == 3.9.6

## RAPT

### Dataset

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

---------------------

### Experiment

Please follow the steps below to reproduce the result of RAPT.

#### Train

```bash
cd trace/main
python train_trace_rapt.py \
--data_dir ../data/$PROJ_NAME \
--output_dir ./output \
--model_path ./model \
--per_gpu_train_batch_size 8\
--per_gpu_eval_batch_size 8\
--logging_steps 50 \
--gradient_accumulation_steps 1 \
--num_train_epochs 5 \
--learning_rate 4e-5 \
--code_bert ../codebert \
--hard_ratio 0.5
```

#### Evaluate

```bash
python eval_trace_rapt.py \
    --data_dir ../data/$PROJ_NAME \
    --model_path ./output/$PROJ_NAME \
    --codebert ../codebert \
    --per_gpu_eval_batch_size 4 
```

## GPT2

### Dataset

Same as RAPT.

### Experiment

#### Train

```bash
cd trace/main
python train_trace_gpt2.py \
--data_dir ../data_no/$PROJ_NAME \
--output_dir ./output \
--per_gpu_train_batch_size 8\
--per_gpu_eval_batch_size 8\
--logging_steps 50 \
--gradient_accumulation_steps 1 \
--num_train_epochs 5 \
--learning_rate 4e-5 \
--gpt2 ../gpt2 \
--hard_ratio 0
```

#### Evaluates

```bash
python eval_trace_gpt2.py \
    --data_dir ../data_no/$PROJ_NAME \
    --model_path ./output/$PROJ_NAME \
    --gpt2 ../gpt2 \
    --per_gpu_eval_batch_size 4 
```

## CodeT5

### Dataset

Same as RAPT.

### Experiment

#### Train

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

#### Evaluate

```bash
python eval_trace_codet5.py \
    --data_dir ../data_no/$PROJ_NAME \
    --model_path ./output/$PROJ_NAME \
    --codet5 ../codet5 \
    --per_gpu_eval_batch_size 4 
```

## RAT

### Dataset

The following projects are all open source and can be obtained from github as code repositories.

| Project                 | Abb. | Time Span             |
| ----------------------- | ---- | --------------------- |
| JBossTransactionManager | JT   | 2012-08-20~2023-01-09 |
| WildFlyCore             | WC   | 2012-03-24~2023-07-11 |
| Debezium                | DE   | 2016-03-02~2022-05-06 |
| Weld                    | WE   | 2011-11-09~2015-04-13 |
| Undertow                | UN   | 2013-04-09~2023-06-08 |
| Teiid                   | TE   | 2012-10-10~2018-03-20 |
| Wildfly                 | WI   | 2011-10-11~2023-05-31 |
| ModeShape               | MO   | 2009-08-04~2017-04-14 |
| Infinispan              | IN   | 2011-05-09~2020-10-03 |
| WildFlyElytron          | WL   | 2014-08-13~2023-06-06 |
| HAL                     | HA   | 2016-07-07~2022-02-22 |
| JGroups                 | JG   | 2011-03-20~2023-06-29 |
| RESTEasy                | RE   | 2012-05-11~2023-06-08 |

### Experiment

According to the author's description of use：RAT is command line tool so far, it supports three types of commands:

```
> -a <git-repo-folder> -s <sqlite-file-path> # detects all code history

> -bc <git-repo-folder> <start-commit-sha1> <end-commit-sha1> -s <sqlite-file-path> # detects code history between start commit and end commit

> -c <git-repo-folder> <commit-sha1> -s <sqlite-file-path> #detects code history between last commit and this commit
```

`<git-repo-folder>`defines the path of the local repository, `<sqlite-file-path>` indicates the path for saving the SQLite database.

When packaged with maven(`mvn clean`and `mvn install`)，you got a Java Jar package.

Before you run the RAT on a git-repo project, you should create a `<project-name>.sqlite3`file and execute the sql file in the `src/main/resources` folder named `createSqliteTable.sql`.

Then you can run the example like:

```sh
java -jar TraceabilityModel-1.0-SNAPSHOT-jar-with-dependencies.jar -a /wildfly -s /wildfly.sqlite3
```

### TraceScore

#### Dataset

The following projects are all open source and can be obtained from github as code repositories. In our work, we use `xxx1.sqlite3` to represent the output of the RAT model and `xxx2.sqlite3` to represent the issue and other relevant information obtained from Jira.

| Project                 | Abb. | Time Span             |
| ----------------------- | ---- | --------------------- |
| JBossTransactionManager | JT   | 2012-08-20~2023-01-09 |
| WildFlyCore             | WC   | 2012-03-24~2023-07-11 |
| Debezium                | DE   | 2016-03-02~2022-05-06 |
| Weld                    | WE   | 2011-11-09~2015-04-13 |
| Undertow                | UN   | 2013-04-09~2023-06-08 |
| Teiid                   | TE   | 2012-10-10~2018-03-20 |
| Wildfly                 | WI   | 2011-10-11~2023-05-31 |
| ModeShape               | MO   | 2009-08-04~2017-04-14 |
| Infinispan              | IN   | 2011-05-09~2020-10-03 |
| WildFlyElytron          | WL   | 2014-08-13~2023-06-06 |
| HAL                     | HA   | 2016-07-07~2022-02-22 |
| JGroups                 | JG   | 2011-03-20~2023-06-29 |
| RESTEasy                | RE   | 2012-05-11~2023-06-08 |

#### Experiment

```sh
python ./tracescore/stopword.py #remove the stopword in the issue table of xxx2.sqlite3
# then you can run the following py file to obtain tracescore result
python ./tracescore/tracescore.py
```

