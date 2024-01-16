## Joint Multimodal Entity-Relation Extraction Based on Edge-Enhanced Graph Alignment Network and Word-Pair Relation Tagging（AAAI 2023）
* Edge-Enhanced Graph Alignment Network : 边增强图对齐网络
* Word-Pair Relation Tagging ：单词对关系标记
### 动机：
* 现有的方法通常会独立处理两个任务（MNER和MRE），而忽略了它们之间的双向交互。
* 仅考虑了视觉对象与视觉和文本图中的文本实体对齐，忽略entity-entity和object-object的关系
### 贡献：
* 提出了边增强图对齐网络
* 词对关系标记
* 例子： NER ：(Curry,NBA) 属于是Per和Org，ER:(Curry,Thompson)关系是peer，然而他们的实体类别是per 和 per。所以MNER和MER是相辅相成的
* conj ： 并列连词 "near" ，dobj ： 直接宾语"holding" **what** , nusbj : 名词性主语, **who** has
#### 任务定义：
* 给定输入text以及对应的image，抽取五元组{(e1,t1,e2,t2,r)}，例如 (Thompson, Per, NBA, Org, Member of )
* 词对关系标记
* 边增强图对齐网络
### JMERE涉及的三个子任务：
* 实体识别（Entity Recognition，ER）： 识别文本和图像中提到的实体，并将其分类为预定义的类别（例如，人名、地名、组织名等）。
* 关系分类（Relation Classification，RC）： 识别文本和图像中提到的实体之间的关系，并将其分类为预定义的关系类型（例如，位置、所有权、从属关系等）。
* 实体对齐（Entity Alignment，EA）： 将文本和图像中提到的相同实体进行对齐，并建立它们之间的对应关系。
## Rethinking Multimodal Entity and Relation Extraction from a Translation Point of View（ACL 2023）
### 动机：
* 题目：从翻译角度，再就多模态实体和关系抽取进行思考。交叉模态的错位类似于机器翻译中的跨语言差异问题
* 错误对齐可以被分为——> 部分对齐 和 完全不相关对齐
