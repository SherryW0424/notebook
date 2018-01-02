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

给出一个单词序列，我们根据前面的单词预测（后面）每个单词的概率。语言模型允许我们测量一个句子可能出现的概率，这对于机器翻译而言是一个很重要的输入（因为概率高的句子通常是正确的）。能够预测下一个单词的side-effect是我们得到了一个生成模型，这允许我们通过输出概率生成新的文本。根据我们不同的训练数据，我们可以生成各种stuff。在语言模型中，输出通常是单词序列（例如用one-hot编码），且输出为预测的单词序列。当训练网络时，我们设置<img src="https://latex.codecogs.com/svg.latex?o_t = x_{t+1}" title="o_t = x_{t+1}" />，因为我们希望时刻t的输出为真实的下一个单词。

关于语言模型与生成文本的文章：
- <a href="https://github.com/SherryW0424/notebook/blob/master/DeepLearning/Recurrent%20neural%20network%20based%20language%20model.pdf">Recurrent neural network based language model</a>
- <a href="https://github.com/SherryW0424/notebook/blob/master/DeepLearning/Extensions%20of%20Recurrent%20neural%20network%20based%20language%20model.pdf">Extensions of Recurrent neural network based language model</a>
- <a href="https://github.com/SherryW0424/notebook/blob/master/DeepLearning/Generating%20Text%20with%20Recurrent%20Neural%20Networks.pdf">Generating Text with Recurrent Neural Networks</a>

<b>机器翻译</b>
机器翻译与语言模型类似，输出是输入语言的一个单词序列。我们想要以目标语言输出单词序列。一个关键区别为在输入全部输入单词之后，才开始输出，因为第一个翻译句子中的第一个单词可能需要获取完整输入句子的信息。

<b>语音识别</b>

略

<b>生成图片描述</b>

略

### 训练RNN
训练RNN与训练传统的神经网络类似。我们也使用反向传播算法，不过会有一点儿变化。因为在网络中各个步骤的参数是共享的，每个输出的梯度不仅需要计算当前时刻的步骤，还需要前面的步骤。例如，为了计算梯度<img src="https://latex.codecogs.com/svg.latex?t=4" title="t=4" />，我们需反向计算3步并将梯度求和。这成为Backpropagation Through Time(BPTT)。由于梯度消失/爆炸，使用BRTT训练的vanilla RNN难以学习长期的依赖。解决这些问题的方法是存在的，并且某些RNN（像LSTM）正是为解决这个问题设计的。

### RNN扩展
多年来，为了解决vanilla RNN模型的shortcoming，研究人员develop许多更复杂类型的RNN。我们将在后面的post里详细介绍，这节将做一个简要介绍，这样方便你熟悉模型的分类。

时刻t的输出可能不仅仅依赖于序列前面的元素，还有可能依赖后面的元素，<b>Bidirectional RNNs</b>正是基于这个思路。例如，预测序列中间缺失的单词，需要两侧上下文的信息。Bidirectional RNNs很简单。它只是两个RNN
stacked on top of each other. 输出将基于两个RNN的隐藏状态计算。

![Bi-RNN原理图片](/DeepLearning/bidirectional-rnn.png)

<b>Deep (Bidirectional) RNNs</b>与BiRNN类似，只是每个时刻多了几层。实际上这使得网络的计算能力更高（但我们也需要更多的训练数据）。

![Deep Bi-RNN原理图片](/DeepLearning/deepbiRNN.png)

<b>LSTM 网络</b>最近很受欢迎，并且刚才我们已经简要的介绍过。LSTM在基础结构上与RNN没什么区别，只是使用了不同的函数去计算隐藏状态。LSTM的记忆被称为cells，你可以把它当做前一个状态<img src="https://latex.codecogs.com/svg.latex?h_{t-1}" title="h_{t-1}" />与输入<img src="https://latex.codecogs.com/svg.latex?x_t" title="x_t" />的黑盒。这些cells决定在记忆里留下什么（丢弃什么）。然后它们将之前的状态，当前的记忆与输入结合。这些类型的单元对获取长期记忆十分有效。刚开始LSTM可能十分混乱，但如果你对它感兴趣，<a href="http://colah.github.io/posts/2015-08-Understanding-LSTMs/">这篇post是个很好的解释</a>。

### 总结
希望你对RBB有一个基本的了解，知道它们能干什么。在接下来的poat中我们将使用Python与Theano实现第一版RNN。
