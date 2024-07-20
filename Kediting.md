# Knowledge editing 知识编辑
## 多模态知识编辑和纯文本知识编辑的区别之处：
 * 对于多模态的知识，对知识进行修改：
   * 图片 + 文本知识 例如
   * ![image](https://github.com/bixie6868/dailyNote/assets/78329110/f6c8055b-ee94-4b77-adf5-22beea59f99b)
   * 目前已有的工作基本上都是之前 纯文本方面的框架拿来直接用，那么多模态知识编辑的意义就无法体现。目前只有一些数据集的提出以及一些基于FT的方法
   * 这个女人是谁 --> 这个女人的出生日期
     * 修改视觉模块，让他将这个人识别成其他人？ 或者一个东西的例子，图片中是一个香蕉，改变视觉常识让他觉得那是一个苹果（但本质上都是第一个问题）
     * 修改后面的信息即，更改这个女人的出生日期，同样也可以得到 另一个答案（很像是去修改纯文本模态的信息）
     * 修改视觉模态和后续的信息也可以得到，修正的作用。
     * 那么主要问题是：如何编辑视觉模块，使得其最基本的识别问题就是错误的。
## 定义：
  * 不进行训练的情况下改变模型的答案，考量的点1、要修的问题在 In-scope中(域内的输入:直接更改知识，或者对于多跳的知识可移植性） 2、域外知识，不需要修改的问题并不影响其表现
## 为什么可以进行知识编辑：
### LLM如何存储知识：
  *  Key, Value： 浅层存储的是表面信息shallow，越深层存储的信息越抽象即语义信息。
## 如何进行知识编辑：
### 引入外部帮助external helps：
 * 基于检索增强的半参数编辑模型（SERAC）
 * IKE（in-context knowledge editing)
 * Mello(针对多跳的问题）
 * DeepEdit
### 融合知识到模型的参数中（打补丁）
 * CaliNET(外挂FFN层)
 * T-patcher(外挂神经元）
 * GRACE（维护一个键值对，一个密码本 不断增加新知识维护密码本）
 * MELO（引入额外Lora）
### 真实修改参数
 * MEND(fine-tuned FFN)
 * 定位编辑（因果追踪确定位置）rank-one model editing(ROME)
 * MEMIT(解决只更新一个位置数据的问题）
## Banchmark：KnowEdit
 * 包含增删改数据集
## 评估指标:
 * Reliability（可信度） ：衡量编辑成功率
 * Locality（局部性）：局部信息是否还能保持原来的信息
 * Generality（泛化性）：编辑成功的知识的泛化性（多模态：测试纯文本泛化性就是将文本换成一个相似的句子。测试视觉泛化性就是将图片换成一个相似的图片）

## VLKEB 数据观察：
* eval_multihop 包含 3174个样例其中包含对应于 原始不跳问题eval中的原始图片。
* 如下图所示： 数据集eval中不包含port_new参数，同时eval_multihop中也可能包含不含有port_new参数，即不跳的情况。
* 所以3174不仅包含了1278个一跳问题，包含一些多跳问题可能对应一张图片还包含有一些不跳的。所以全部的信息都保存在了eval_multihop的json文件中。
* ![image](https://github.com/user-attachments/assets/b9eaac67-7cbd-4067-9550-e698107c31ab)
* 问题：
  * 为什么加参数[HOP_NUM]之后，只是去预测portability?其他参数结果没有进行预测(其他指标)
    * 尝试compute_icl_multimodal_edit_quality的部分代码注释去掉（不仅仅去预测port.)（log中5176行开始,port 2 : 10132开始）
    * 论文所给出的指标是算所有样例如 编辑成功率的平均吗？(展示的是port1，跳一次的结果portability)
  * 只能预测多跳问题吗？如何预测不跳的问题(通过该代码实现！！)
## IKE实现：
参见例子：http://10.1.1.12:18212/lab/workspaces/auto-D/tree/data1/Code/Edit/EasyEdit-main/tutorial-notebooks/EasyEdit_Example_IKE.ipynb
### 具体实现方式:
![image](https://github.com/bixie6868/dailyNote/assets/78329110/cc0355ad-805b-4e52-a60b-3f01e1d458f0)
* copy:表示希望能够教会模型对于注入的新知识可以实现最基本的copy
* update：知识编辑并不是简单的教LM去重复新知识，而是在编辑区域内更改prompt，提升问题难度
* retain： 对于不属于其编辑区域的输入，应保持其原输出
* image 在其中充当什么作用？
    * Reliability： 即输入想要编辑的prompt，并且输入Question即对应的图片，输出结果，同时衡量其正确性
    * Generality： 问题更改为rephrase，或者是图片更改成raphrase_image，分别表示T-Gen和I-Gen
    * Localoty: T-Loc即不输入image,只更改question为对应的loc_q。I-Loc即输入Loc-image 同时输入的是 m_loc_q.那么loc_q和m_loc_q的区别在哪里？：完全不想关，是不同的问题
    * Portability: 分别评估1-hop,2-hop,3-hop,4-hop.实验主表中展示的是1-hop的结果。（其实数据集相对于eval只有1k多条，而eval有3k多跳，不太一样）
* 将训练集train以上述形式存入，便于后续进行相似样例的检索：
* ![image](https://github.com/bixie6868/dailyNote/assets/78329110/a9f376ae-519a-4312-aa4d-c7c312777896)
* 2-hop结果：
* ![image](https://github.com/user-attachments/assets/fe12c925-c4c1-4abc-9636-7aed156a7173)
* 不跳的实验结果：
* ![image](https://github.com/user-attachments/assets/3ea5f571-7317-460b-9af1-13856896cc1f)
## SERAC 方法简介和实现（基于检索增强的反事实半参数编辑模型）

### 问题：
![image](https://github.com/user-attachments/assets/845ac1c2-bd1e-4162-8973-fd1f32270677)
* 为什么SERAC同样基于外部知识库帮助的方法需要train？（是否是分别训练了Scope model和 Counterfactual model?）只看到微调llava31层正如论文所示，其他的微调部分复杂没有仔细看
* BaseTrainer中调用其中的run(),进而训练函数的代码体现在multimodalTrainer中的train_step和edit_step函数中.
* 其中MultimodalTrainer中用到的self.model就是SERAC_MULTI，其中run_classifier和generate函数中分别显示了分类函数scope classifier(bert) > 0.5 才会使用下述counterfactual model(可能是vicuna)，微调31层，(位于trainer/algs/SERAC)
* 一张A6000调不起来

## 现有的方法缺点
* 之前定位然后编辑的方法用在语言模型上，但是多模态模型这边不知道咋定位？
* 如果直接用语言那边的定位方法就太像了，单单再加个图片可能效果也不好？
* 样编辑跟视觉模态关系比较浅吗？
* 是全量微调了LLaVA 并之编辑31层的参数吗？如果如此，那么与知识编辑的低资源相违背！！
  * 冻结不需要更新的层，如下所示，因此爆显存的原因可能是 在反事实的验证器中使用vicuna-7B，因为微调vicuna-7B 以及 LLaVA 31层导致爆显存
  ```
      if '31' in k:
          v.requires_grad = True
      else:
          v.requires_grad = False
    ```
* 计划
  * 读论文ROME，了解如何定位-->再如何编辑。如果潜移到LVLM上如何定位编辑。
  * 如果是对图片进行编辑，比如现有的一些图片重构的（CVPR 2024好多工作）是否可以联系起来。
  * ROME以及后续编辑方法MEMIT的区别在哪里？ 用哪个进行复现？现有方法有哪些不好从而进行改进。
  * Rome和llama编辑结果，没编辑和编辑后的结果是一样的。原因是否是使用的模型不同，比如在此处使用的模型是Llama-2-7b-chat-hf。而源代码只写了llama-2-7b
  * ![image](https://github.com/user-attachments/assets/abd443ed-3da5-4f8d-9f9a-8db72f01cd4e)
  * ![image](https://github.com/user-attachments/assets/ce1fde82-4987-486b-b085-5728e8cd1f87)
  * ![image](https://github.com/user-attachments/assets/760a3966-473a-442b-bcdc-8920bf5ce12c)
* ROME 编辑部分：看了编辑部分的相关代码，如下是其模型图，含义是
    * k*是用于选择主题词并在l层i_token处的键向量(subject),(ai表示在第l层的第 i 个令牌的注意力机制的输出。hi表示第l-1层第i个令牌的隐藏状态。k(x)用于计算给定文本x在特定层l和特定令牌索引i处的**输入**(Wfc是MLP的一个权重矩阵，y是一个归一化非线性激活函数)。
      * ![image](https://github.com/user-attachments/assets/28b281e6-a4bd-4480-a15e-dc97e15741be)
      * ![image](https://github.com/user-attachments/assets/84ba4740-fdf3-4a11-90db-7acbc598db1a)
    * 下述是优化v*的目标函数L(z)，即表示在给定上下文xj 和 提示p 预测为更改后的 o *的概率最大化。
      * ![image](https://github.com/user-attachments/assets/d6b590a6-541d-40d3-8db9-5762311fa59f)
    * 在模型的权重矩阵中插入一个新的键值对(k*,v*)，而最小化对现有权重的干扰
      * ![image](https://github.com/user-attachments/assets/4bad09c7-b69f-4abc-b899-51a7f1736006)
  * ROME 定位部分Locate
    * ---正在看---





