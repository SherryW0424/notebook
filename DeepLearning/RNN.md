# RNN 原理&实现

>翻译：http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/

## 什么是RNN

RNN专门用于处理时序信息（sequential information）。在传统的神经网络中我们认为所有的输入（和输出）是相互独立的。但有些任务并不如此。比如，当你想要预测句子中下一个出现的单词时，你最好知道前一个单词是什么。RNN被称为Recurrent，是因为句子中的每个元素都共享参数，输出依赖于前面的计算。也可以说，RN是有记忆的，它获取至今为止计算的信息。理论上，RNN可以使用相当长的序列，但实际上它常常被限制在几步内。RNN如图所示：


![RNN原理图片](/DeepLearning/rnn.jpg)

上图展示了一个unrolled(unfolded)的RNN。通过展开RNN我们写出了一个完整网络的序列（By unrolling we simply mean that we write out the network for the complete sequence.）。例如，如果我们关心的序列包含5个单词，那么这个网络将会被展开为5层的神经网络，每层一个单词。RNN中的公式如下：

- <img src="https://latex.codecogs.com/svg.latex?x_t" title="x_t" />为时刻t的输入。例如，<img src="https://latex.codecogs.com/svg.latex?x_1" title="x_1" /> 可以是句子里的第二个单词。

- <img src="https://latex.codecogs.com/svg.latex?x_t" title="x_t" /> 为时刻t的隐藏状态，也是该神经网络的“记忆”，<img src="https://latex.codecogs.com/svg.latex?s_t" title="s_t" />基于之前的隐藏状态以及当前步骤的输入：<img src="https://latex.codecogs.com/svg.latex?s_t=f(Ux_t&space;&plus;&space;Ws_{t-1})" title="s_t=f(Ux_t + Ws_{t-1})" />。函数<img src="https://latex.codecogs.com/svg.latex?f" title="f" />通常是非线性的，比如 <img src="https://latex.codecogs.com/svg.latex?tanh" title="tanh" /> 或 <img src="https://latex.codecogs.com/svg.latex?ReLU" title="ReLU" />。<img src="https://latex.codecogs.com/svg.latex?s_{-1}" title="s_{-1}" />是需要计算的第一个隐藏状态，通常设置为0。
- <img src="https://latex.codecogs.com/svg.latex?o_t" title="o_t" />为时刻t的输出。例如，如果我们想要预测句子的下一个单词，那么它将是表示某个词汇的概率的向量。<img src="https://latex.codecogs.com/svg.latex?o_t = softmax(Vs_t)" title="o_t = softmax(Vs_t)" />

#### 注意事项：
- 你可以将隐藏状态<img src="https://latex.codecogs.com/svg.latex?s_t" title="s_t" />当做网络的记忆。<img src="https://latex.codecogs.com/svg.latex?s_t" title="s_t" />获取capture之前所有步骤（时刻）发生的信息。时刻t的输出<img src="https://latex.codecogs.com/svg.latex?o_t" title="o_t" />仅根据时刻t的记忆计算（calculated solely based on the memory at time t）。正如之前简单提到的，实际上稍复杂因为<img src="https://latex.codecogs.com/svg.latex?s_t" title="s_t" />不能记忆很久之前（很多步骤之前）的信息。
- 与传统的深度神经网络不同，深度神经网络每一层都使用相同的参数，RNN在每一步分享相同的参数（<img src="https://latex.codecogs.com/svg.latex?U,V,W" title="U,V,W" />等）。这反映出我们在每个步骤都执行相同的task，只是输入不同。这大大减少了我们需要训练参数的总数。
- 上图中，每一步都有一个输出，但这并不是必要的，具体根据任务而定。例如，当预测句子的情感时，我们只关心最后的输出，而不是每个单词输入之后的输出。类似地，我们可能也并不需要每一步的输入。RNN主要的特征是隐藏状态，该隐藏状态获取（输入）序列的信息。

## RNN可以做什么
RNN在许多NLP任务中都获得了极大的成功。这里我应该提到最常使用的RNN类型为LSTM，LSTM在获取长期依赖关系比RNN好很多。不要担心，LSTM和我们在这个tutorial开发的RNN大部分一样，它们只是计算隐藏状态的方式不同。我们将在后面介绍更多关于LSTM的细节。以下是RNN在NLP中的例子应用（非穷举清单）。

<b>语言模型和生成文本</b>
