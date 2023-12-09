# BERT vs T5 vs GPT

参考文献：

https://zhuanlan.zhihu.com/p/409771204
https://zhuanlan.zhihu.com/p/88438851
https://zhuanlan.zhihu.com/p/664143604
https://zhuanlan.zhihu.com/p/350017443
[语言预训练模型梳理: BERT & GPT & T5 & BART](https://zhuanlan.zhihu.com/p/656767554)

从架构上，BERT，T5， GPT中GPT应该是最弱的
![Alt text](image.png)

设计理念上：
- BERT: 是先学Embedding，再task-specific finetune；BERT的预训练任务Masked Language Model，Next Sentence Prediction
- GPT： 单向生成式，数据&模型越大能力越强；从GPT-1到GPT-2，模型不仅学会了顺着句子语义说下去的能力，也学会了揣摩用户在输入中的任务要求，并回答该任务的能力（涌现能力，作者认为当一个语言模型的容量足够大时，它足以覆盖所有的有监督任务，即**所有有监督学习都是无监督语言模型的一个子集**）；GPT-3 尝试将任务相关的固定提升魔改成“固定提升+少样本示范”，用**少样本微调**比**零样本微调**更可以发掘模型的涌现能力

    其实GPT系列每篇文章名都是很好的说明：

    GPT-1 Improve Language Understanding by Generative Pre-training

    GPT-2 Language Models are Unsupervised Multitask Learners

    GPT-3 Language Models are Few Shot Learners
- T5: 相比BERT、GPT-1, 在微调时，需要根据不同下游任务在预训练模型上加不同的网络架构以实现微调目的。T5为所有下游任务提供了一个统一的接口，所有下游任务都可以转化为Text-to-Text的形式；仿照GPT-2的提示学习

根据以上信息，我认为GPT的成功不在于架构本身，而在于对于“学习”的定义，更趋向AGI而不是在做digital engineering。

https://zhuanlan.zhihu.com/p/665046029

GPT-4进化路上一些关键技术：
- SFT是早期GPT进化的推动者
- 帮助GPT提升编码能力的最大功臣是SFT和RLHF(**LLM仍可以通过SFT和RLHF，不断将内在能力（但需要多次尝试）转化成一次性解决问题的能力，不断逼近LLM的能力上限。**)
- 在预训练中加入代码数据则提升了后续GPT版本的各方面能力，尤其是推理

**GPT-4在变笨并非危言耸听**，这项评测发现GPT进化路上出现了明显的“跷跷板现象”，即模型进化过程中一部分能力提升另一部分下降

https://www.zhihu.com/question/591038461/answer/3309520186

**基础模型无疑是人工智能的第一性原理，其中，基础网络架构（Model Architecture）和学习范式（Learning Paradigm）是其两大核心基础。**

**为了突破这些局限，我们推出了如 RetNet 和 BitNet 等旨在取代 Transformer 的新型网络架构**

https://www.zhihu.com/question/588325646/answer/2932131238

**论文最主要的一个结论是decoder-only模型在没有任何tuning数据的情况下、zero-shot表现最好，而encoder-decoder则需要在一定量的标注数据上做multitask finetuning才能激发最佳性能。**

**前面说到，5B参数量+170B token数据量时，在做multitask finetuning后encoder-decoder相比decoder-only反而在新任务上会有一定的优势。**

**涌现能力的一个表现是，参数量达到一定量级后，模型具有了"复杂的推理能力"——譬如从非结构化的文本中自动地提取结构化的知识。对于常见的NLP任务、LLM可以视为已经self finetuning过了；对于复杂问题，LLM的推理能力可以把这些问题转换成几个基本任务的”和“。**

按照这篇论文的思路，decoder-only的架构相比encoder-decoder在In-Context的学习上会更有优势，因为前者的prompt可以更加直接地作用于decoder每一层的参数，微调信号更强。也因此，更适合ChatGPT这类开放域的对话模型作为基础模型。

encoder-decoder有两个特点可能会使得它在以下两类任务上有优势:
1. encoder的多样性。许多多模态工作(BLIP、ALBEF、SimVLM)上可以看见encoder-decoder的影子，因为encoder可以用来encode多种模态的信息，而decoder-only的多模态工作相对较少（微软近日的visual-ChatGPT更多偏向模型级联pipeline）
2. Deep Encoder+Shallow Decoder的推理优势。Encoder-decoder架构本来在计算效率上（以FLOPs衡量）就是优于其他LM的，且工业界目前常使用Deep Encoder+Shallow Decoder的组合，由于encoder本身并行度高，这类encoder-decoder的infer速度远超大型的decoder-only。在有较多标注数据的任务上，encoder-decoder还是具有成本优势的。
