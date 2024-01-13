## Joint Multimodal Entity-Relation Extraction Based on Edge-Enhanced Graph Alignment Network and Word-Pair Relation Tagging（AAAI 2023）
* Edge-Enhanced Graph Alignment Network : 边增强图对齐网络
* Word-Pair Relation Tagging ：单词对关系标记
### 动机：
* 现有的方法通常会独立处理两个任务（MNER和MRE），而忽略了它们之间的双向交互。
* 仅考虑了视觉和文本实体的对齐，忽略entity-entity和object-object的关系
### 贡献：
* 提出了边增强图对齐网络
* 词对关系标记
* 例子： NER ：(Curry,NBA) 属于是Per和Org，ER:(Curry,Thompson)关系是peer，然而他们的实体类别是per 和 per。所以MNER和MER是相辅相成的
* conj ： 并列连词 "near" ，dobj ： 直接宾语"holding" **what** , nusbj : 名词性主语, **who** has
#### 任务定义：
* 给定输入text以及对应的image，抽取五元组{(e1,t1,e2,t2,r)}，例如 (Thompson, Per, NBA, Org, Member of )
* 词对关系标记
* 边增强图对齐网络
