# multiple-objects-gan
## 训练过程
* 使用Adam优化器，分别训练生成器netG和判别器netG
* 加入噪音，使用netG生成假图像
* 在判别器训练过程中，判别器会不断地学习如何区分真实图像和生成图像。如果判别器能够很好地区分真实图像和生成图像，则说明生成器生成的图像还不够逼真。
* 在生成器训练过程中，需要让生成器生成的图像能够欺骗判别器，认为生成图像是真实的。

一、本周工作进展

1. 尝试在infoseek问题上使用PromptCap

* 在infoseek数据集上效果不佳，会直接产生包含问题答案的Caption，但产生的答案是错误答案（尝试样例不多，考虑到可能尝试PromptCap对后续的工作帮助不大）

​2.使用Gemini生成数据，一共包含以下三种情况
* 不加入样例，仅输入当前的 问题 答案 和 图片 去生成summary包含问题答案，问题相关）
  - 生成的Summary大部分包含了答案，但是生成的Summary(Caption)过长，可能需要提供一些样例
* 加入样例的方式：
  - 问题，答案，原始caption，summary，多张Images (Gemini可以放入多张图片，但是问题是它无法将图片和问题进行一一对应，另外通过一些测试，有时输入10个问题，回答的结果却是9个或11个)
  - 问题、答案、原始caption，summary（暂时未尝试）
二、下周工作计划

1. 尝试上周阅读论文中的PromptCap（让模型生成和问题相关的文本描述）
2. 使用PromptCap相同思路，使用Gemini根据Original Caption、Image、Question、Answer生成和问题（entity）相关的图片描述Summary（即Summary中包含问题的答案），构建这样的数据集用于训练。
