# TAAN - The Advisors Alliance Network of Mixture of Experts

In the field of artificial intelligence, the combination of smaller AI models to form a larger, more complex model is a topic of ongoing research. Various strategies have been proposed and implemented to achieve this goal.

The Mixture of Experts (MoE) model [1] is a well-known approach that combines multiple "expert" models, each of which specializes in a different part of the input space. The idea is to weight the outputs of these experts according to their competence in the given input, thereby creating a more powerful and flexible model. The Switch Transformer (ST) [2] is a variant of the Transformer model that replaces the dense feed-forward network (FFN) layer in the Transformer with a sparse Switch FFN layer. This layer operates independently on tokens in the sequence and routes them to multiple FFN experts. The Switch FFN layer returns the output of the chosen FFN, multiplies it by the router threshold, and then merges it. This approach allows the model to dynamically allocate computational resources, focusing on the most relevant experts for each specific task. The Expert Choice (EC) method [3] sets up a group of experts with a predetermined buffer capacity. It assigns the top k tokens to the experts, generates a score matrix from the tokens to the experts, and then makes routing decisions based on this matrix. This approach ensures that each token can be routed to a variable number of experts, and each expert can have a fixed bucket size, improving training convergence time and performance in fine-tuning tasks. The Generalist Language Model (GLaM) [4] uses a sparsely-activated mixture-of-experts architecture to increase model capacity while significantly reducing training costs compared to dense variants. By focusing on the relative importance of different tokens and allowing each token to be routed to a variable number of experts, GLaM achieves higher performance in a range of tasks.

In addition to the Mixture of Experts (MoE) method, there are several other strategies for combining AI model such as bagging and boosting, involve training multiple models and combining their predictions to reduce overfitting and improve robustness. Stacking generalization combines models hierarchically, using the outputs of multiple models as inputs to a higher-level model. Federated learning trains models across multiple devices or servers, each with its own local dataset, and aggregates the learned parameters to form a global model. Neural Architecture Search (NAS) automatically finds the best neural network architecture for a given task, combining the best components from a search space of models.

The Advisors Alliance Network (TAAN) is a solution that combines the Mixture of Experts (MoE) and model stacking methods. In simple terms, TAAN is composed of a series of homogeneous Transformer models that are fine-tuned to different characteristics and stacked together. It consists of N+2 models, where N models are fine-tuned for different types of expert knowledge, referred to as Advisors. It needs to be emphasized that all advisors must originate from the exact same base model copy. Otherwise, it wouldn't be TAAN, but some other architecture. According to research[5], continued training of the base model in specialized fields helps to improve evaluation scores. Therefore, further training each advisor in their respective areas of expertise contributes to the enhancement of individual sub-scores, subsequently increasing the overall average score of the TAAN network on the MMLU evaluation.

The architecture of TAAN is similar to MoE, with a gating system. However, unlike the typical MoE architecture, the gating of TAAN is handled by a dedicated Transformer model. This Transformer model, known as the Transformer As Gate (TAG), originates from the same base model as the other expert models but is specifically fine-tuned and optimized for its ability to recognize, but not answer, various expert knowledge.

When TAG receives an external task, such as a Q&A task, it only identifies professional and industry questions, and then forwards the original question to the corresponding Advisor for processing. Unlike MoE, after task processing, the results are returned directly without election or aggregation. 

In addition, there is another model that is fine-tuned by extracting from various classification data, called the Generic Advisor, which handles tasks that TAG cannot classify.

<img width="516" alt="image" src="https://github.com/chattyfish/taa/blob/main/taan_general_diagram.png">


The specific training method for TAAN involves preparing N+1 identical base models, such as the LLaMA2 - 13B base model. N sets of different professional datasets are prepared to continue training models 1 to N. Due to the model's forgetting problem, the evaluation standard is limited to the improvement of the model's test scores in various professional fields, which serves as the Advisor. Then, random selections are made from datasets 1 to N, and classification prompts are appended to the data to fine-tune model N+1 until it can correctly identify the type of knowledge, serving as the TAG.

Then, the entire TAAN network is evaluated using the MMLU (Multi-task Language Understanding) metric. If the average MMLU score improves, the network training is considered successful.

TAAN's advantages include:

- Efficiency: TAAN leverages the strengths of multiple specialized models, enabling it to handle a diverse range of tasks more efficiently than a singular, generalized model.

- Expertise: Each Advisor within TAAN is fine-tuned on a distinct professional dataset, allowing it to become proficient in that particular domain. This level of expertise can lead to superior performance on tasks within the Advisor's area of specialization.

- Scalability: TAAN is designed with scalability in mind, with the capacity to incorporate additional Advisors as required to accommodate more types of knowledge. This adaptability makes it suitable for evolving or changing task demands.

- Robustness and Backup Readiness: The implementation of a dedicated Transformer As Gate (TAG) for task routing enhances the network's robustness. If an Advisor underperforms or fails, the TAG can reroute the task to another Advisor or the Generic Advisor. In the event of an Advisor failure, the remaining models, due to their shared origin with the base model, can take over and perform the missing tasks to a certain extent. This makes TAAN highly fault-tolerant and flexible when deploying models.

- Versatility: The inclusion of a Generic Advisor ensures TAAN's ability to manage tasks that do not fit into the specific domains of the other Advisors, making TAAN a versatile solution capable of handling a broad spectrum of tasks.

- Ease of Training and Knowledge Retention: All Advisors and the TAG in TAAN continue training on top of the base model without modifying the original model, making TAAN easy to implement and train. Furthermore, this approach mitigates the issue of 'forgetting' other knowledge when the model is trained on specialized data.

References:

[1] @misc{gormley2018mixtures,
      title={Mixtures of Experts Models}, 
      author={Isobel Claire Gormley and Sylvia Frühwirth-Schnatter},
      year={2018},
      eprint={1806.08200},
      archivePrefix={arXiv},
      primaryClass={stat.ME}
}

[2] @misc{fedus2022switch,
      title={Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity}, 
      author={William Fedus and Barret Zoph and Noam Shazeer},
      year={2022},
      eprint={2101.03961},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}

[3] @misc{zhou2022mixtureofexperts,
      title={Mixture-of-Experts with Expert Choice Routing}, 
      author={Yanqi Zhou and Tao Lei and Hanxiao Liu and Nan Du and Yanping Huang and Vincent Zhao and Andrew Dai and Zhifeng Chen and Quoc Le and James Laudon},
      year={2022},
      eprint={2202.09368},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}

[4] @misc{du2022glam,
      title={GLaM: Efficient Scaling of Language Models with Mixture-of-Experts}, 
      author={Nan Du and Yanping Huang and Andrew M. Dai and Simon Tong and Dmitry Lepikhin and Yuanzhong Xu and Maxim Krikun and Yanqi Zhou and Adams Wei Yu and Orhan Firat and Barret Zoph and Liam Fedus and Maarten Bosma and Zongwei Zhou and Tao Wang and Yu Emma Wang and Kellie Webster and Marie Pellat and Kevin Robinson and Kathleen Meier-Hellstern and Toju Duke and Lucas Dixon and Kun Zhang and Quoc V Le and Yonghui Wu and Zhifeng Chen and Claire Cui},
      year={2022},
      eprint={2112.06905},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}

[5] @misc{gururangan2020dont,
      title={Don't Stop Pretraining: Adapt Language Models to Domains and Tasks}, 
      author={Suchin Gururangan and Ana Marasović and Swabha Swayamdipta and Kyle Lo and Iz Beltagy and Doug Downey and Noah A. Smith},
      year={2020},
      eprint={2004.10964},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
