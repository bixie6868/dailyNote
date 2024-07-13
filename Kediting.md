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
## IKE实现：
参见例子：http://10.1.1.12:18212/lab/workspaces/auto-D/tree/data1/Code/Edit/EasyEdit-main/tutorial-notebooks/EasyEdit_Example_IKE.ipynb
### 具体实现方式:
![image](https://github.com/bixie6868/dailyNote/assets/78329110/cc0355ad-805b-4e52-a60b-3f01e1d458f0)
* copy:表示希望能够教会模型对于注入的新知识可以实现最基本的copy
* update：知识编辑并不是简单的教LM去重复新知识，而是在编辑区域内更改prompt，提升问题难度
* retain： 对于不属于其编辑区域的输入，应保持其原输出
* 将训练集train以上述形式存入，便于后续进行相似样例的检索：
* ![image](https://github.com/bixie6868/dailyNote/assets/78329110/a9f376ae-519a-4312-aa4d-c7c312777896)
## VLKEB 数据观察：
* eval_multihop 包含 3174个样例其中包含对应于 原始不跳问题eval中的原始图片。
* 如下图所示： 数据集eval中不包含port_new参数，同时eval_multihop中也可能包含不含有port_new参数，即不跳的情况。
* 所以3174不仅包含了1278个一跳问题，包含一些多跳问题可能对应一张图片还包含有一些不跳的。所以全部的信息都保存在了eval_multihop的json文件中。
* ![image](https://github.com/user-attachments/assets/b9eaac67-7cbd-4067-9550-e698107c31ab)
* 问题：
  * 为什么加参数[HOP_NUM]之后，只是去预测portability?其他参数结果没有进行预测(其他指标)
    * 尝试compute_icl_multimodal_edit_quality的部分代码注释去掉（不仅仅去预测port.)（log中5176行开始）
    * 论文所给出的指标是算所有样例如 编辑成功率的平均吗？
  * 只能预测多跳问题吗？如何预测不跳的问题

