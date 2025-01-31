## Improving Your Model Ranking on Chatbot Arena by Vote Rigging
This repository contains the official implementation of [Improving Your Model Ranking on Chatbot Arena by Vote Rigging](https://arxiv.org/abs/2501.17858)


----
<div align=center><img src=pics/demo.png  width="80%" height="60%"></div>

We simulate rigging on new votes beyond the $$\sim$$ *1.7 million* historical votes from the Chatbot Arena Notebook. In this demo, we set the target model $$m_t$$ as Phi-3-small-8k-Instruct. Under the normal voting distribution (w/o rigging), the ranking remains steady, showing only a single rank increase despite the addition of approximately 27,000 new votes. In contrast, vote rigging significantly boosts $$m_t$$'s ranking. Using the omnipresent strategy, we achieve a 15-rank improvement while being more efficient compared to the target-only strategy.

----

### Initialize your rigging environment
To set up the initial rigging environment, you could run the following command to separate the complete voting records into the historical votes (90%), which are used to generate the initially simulated leaderboard, and other users' votes (10%), which are used to explore the impact of concurrent voting from other users.
```cmd
python initial_env.py
```


### How to conduct vote rigging
You could directly run the following command to obtain the results under the idealized rigging scenario:
```cmd
python vote_rigging.py --rigging_mode omni_bt_diff --classifier_acc 1.0 --beta 1.0
```
The default rigging strategy is Omni-BT, and we also support other rigging strategies specified by ```--rigging_mode```. Besides, you could set ```--classifier_acc``` to control the classification performance of de-anonymizing functions and set ```--beta``` to control the marginal probability of sampling the target model. If you want to explore the impact of concurrent votes from other users, you may run the following command:

```
python rigging_with_vo.py --rigging_mode omni_bt_diff
```


### How to train the multi-class classifier
First, switch to the [classifier](classifier) directory. To generate the training corpus, you could run the following example command that queries [Llama-3-8B-Instruct](https://huggingface.co/meta-llama/Meta-Llama-3-8B) using the prompt from the [HC3](https://huggingface.co/datasets/Hello-SimpleAI/HC3) dataset:
```
python dataset_cur.py --output_dir hc3 --model_id meta-llama/Meta-Llama-3-8B-Instruct
```
With the prepared training corpus, try to run the following script to fine-tune a RoBERTa-based model:
```
python train.py --dataset hc3
```

### Defense against vote rigging
To detect malicious users, you can run the following command:
```
python detect_malicious_users.py --rigging_mode omni_bt_diff
```
For vote filtering, you can run the following command and specify the parameter ```--filter_threshold``` to control the filtering threshold.
```
python vote_filtering.py --rigging_mode omni_bt_diff --filter_threshold 0.8
```
