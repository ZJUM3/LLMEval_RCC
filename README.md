[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]

#RCC Evaluation Tool for LLMs

The evaluation tool for **Large Language Models(LLMs)** on **Robustness, Consistency and Credibility**.

## Table of Contents
* [Paper](#Assessing Hidden Risks of LLMs: An Empirical Study on Robustness, Consistency, and Credibility)
* [Pre-process](#Pre-process)
* [Evaluation](#Evaluation)
* [Benchmarks](#Benchmarks)
* [Contribution](#Contribution)
* [Citation](#citation)



## Assessing Hidden Risks of LLMs: An Empirical Study on Robustness, Consistency, and Credibility

[ArXiv](https://arxiv.org/pdf/2305.10235.pdf)|[Lab Page](http://jakezhao.net/)

This repo also contains code for the paper "Assessing Hidden Risks of LLMs: An Empirical Study on Robustness, Consistency, and Credibility". Please see our paper for more details and experiment results.

## Abstract

The recent popularity of large language models (LLMs) has brought a significant impact to boundless fields, particularly through their open-ended ecosystem such as the APIs, open-sourced models, and plugins. However, with their widespread deployment, there is a general lack of research that thoroughly discusses and analyzes the potential risks concealed. In that case, we intend to conduct a preliminary but pioneering study covering the robustness, consistency, and credibility of LLMs systems. With most of the related literature in the era of LLM uncharted, we propose an automated workflow that copes with an upscaled number of queries/responses. Overall, we conduct over a million queries to the mainstream LLMs including ChatGPT, LLaMA, and OPT. Core to our workflow consists of a data primitive, followed by an automated interpreter that evaluates these LLMs under different adversarial metrical systems. As a result, we draw several, and perhaps unfortunate, conclusions that are quite uncommon from this trendy community. Briefly, they are: (i)-the minor but inevitable error occurrence in the user-generated query input may, by chance, cause the LLM to respond unexpectedly; (ii)-LLMs possess poor consistency when processing semantically similar query input. In addition, as a side finding, we find that ChatGPT is still capable to yield the correct answer even when the input is polluted at an extreme level. While this phenomenon demonstrates the powerful memorization of the LLMs, it raises serious concerns about using such data for LLM-involved evaluation in academic development. To deal with it, we propose a novel *index* associated with a dataset that roughly decides the feasibility of using such data for LLM-involved evaluation. Extensive empirical studies are tagged to support the aforementioned claims.

![Framework](https://s2.loli.net/2023/06/09/V34xfDP9qTBsYKO.png)

##Installation

To install our tool, simply run this command:

```python
pip install -e .
```



# Pre-process

We define **data primitives** as components of the datasets $D$ for large-scale auto-interpretation (see paper for detailed methods).
$$
D=\{\mathbf{x}_i\}_{i=1}^n=\{(\mathbf{prompt}_i,\mathbf{p}_i,q_i,o_i,a_i)\}_{i=1}^n
$$
Preprocessing is required, and the data should be transformed into a format containing the following fields before evaluation :

```json
{
    "id" : {
        "passage" : "...",
        "sub-qa" : [
            {
                "question" : "...",
                "answer" : "..."
            }
        ]
    }
}
```

where `passage` denotes the conditions, and `sub-qa` denotes the sub-questions and corresponding answers connected to the `passage`. 

For datasets with options provided, the data format should be :

```json
{
    "id" : {
        "passage" : "...",
        "sub-qa" : [
            {
                "question" : "...",
                "answer" : "...",
                "options" : [
                    {
                        "option_type": "...",
                        "option_describe": "..."
                    }
                ]
            }
        ]
    }
}
```

Additionally, for datasets provided in `Datasets\Raw data`, `converter.py` can be used to achieve auto-conversion. Run this command in the terminal (take GSM8K dataset as the example here) :

```
python converter.py --dataset 'GSM8K'
```



# Evaluation

The evaluation will revolve around three aspects, namely **Robustness, Consistency, and Credibility**. Specific details can be found in the paper.

- **Robustness** : LLMs' ability to handle adversarial examples, aligns with the realistic deployment of LLMs.
- **Consistency** : Measure the distinction in LLMs' responses to semantically similar inputs.
- **Credibility** :  Measured by $$\textsf{RTI}$$ (Relative Training Index), reflecting the relative probability that the datasets have been trained by LLMs.

##Questions process

Use the following command to obtain a list of questions that can be fed to different models :

1. Robustness

   ```
   python processor.py \
   	--dataset GSM8K \
   	--type robustness \
   	--iter 4 2 2
   ```

2. Consistency

   ```
   python processor.py \
   	--dataset GSM8K \
   	--type consistency \
   	--prompt_dir 'Datasets\Attacked data primitive\Consistency\prompt_template.json'
   ```

3. credibility

   ```
   python processor.py \
   	--dataset GSM8K \
   	--type credibility \
   	--cred_operation ri rd rp
   ```

Also, `ChatGPT` inference is provided for direct evaluation :

```
python processor.py --useChatGPT
```

##Auto evaluation

After obtaining a list of responses of LLMs, use the following command to auto-evaluation

```
python eval.py \
	--response_dir '' \
	--indir '' \
	--type robustness
```

where `indir` denotes to the path of data primitives format file.



# Benchmarks

`Datasets\*` directory stores all datasets as benchmarks tests :

- `Raw data`: Raw format datasets;
- `Data primitive`: Data primitives format datasets;
- `Attacked data primitive`: Attacked data primitives format datasets;

Related repos:
  - API: [Reverse engineered ChatGPT API](https://github.com/acheong08/ChatGPT)
  - Datasets: [AQuA](https://github.com/deepmind/AQuA/blob/master/test.json) | [creak](https://github.com/yasumasaonoe/creak/blob/main/data/convert_faviq.py) | [NoahQA](https://github.com/Don-Joey/NoahQA) | [GSM8K](https://github.com/openai/grade-school-math) | [bAbI-tasks](https://github.com/facebookarchive/bAbI-tasks) | [Sen-Making](https://github.com/wangcunxiang/Sen-Making-and-Explanation/tree/master) | [strategyqa](https://github.com/eladsegal/strategyqa)
  - Data augment: [eda_nlp: Data augmentation for NLP, presented at EMNLP 2019](https://github.com/jasonwei20/eda_nlp)



# Contribution

his project welcomes contributions and suggestions.  Most contributions require you to agree to a Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions provided by the bot. You will only need to do this once across all repos using our CLA.



# Citation

Please use the below BibTeX entry to cite this dataset:

```latex
@misc{ye2023assessing,
      title={Assessing Hidden Risks of LLMs: An Empirical Study on Robustness, Consistency, and Credibility}, 
      author={Wentao Ye and Mingfeng Ou and Tianyi Li and Yipeng chen and Xuetao Ma and Yifan Yanggong and Sai Wu and Jie Fu and Gang Chen and Haobo Wang and Junbo Zhao},
      year={2023},
      eprint={2305.10235},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}
```
