# RNN 原理&实现

## 什么是RNN

RNN专门用于处理时序信息（sequential information）。在传统的神经网络中我们认为所有的输入（和输出）是相互独立的。但有些任务并不如此。比如，当你想要预测句子中下一个出现的单词时，你最好知道前一个单词是什么。RNN被称为Recurrent，是因为句子中的每个元素都共享参数，输出依赖于前面的计算。也可以说，RN是有记忆的，它获取至今为止计算的信息。理论上，RNN可以使用相当长的序列，但实际上它常常被限制在几步内。RNN如图所示：


![RNN原理图片](/DeepLearning/rnn.jpg)

上图展示了一个unrolled(unfolded)的RNN。通过展开RNN我们写出了一个完整网络的序列（By unrolling we simply mean that we write out the network for the complete sequence.）。例如，如果我们关心的序列包含5个单词，那么这个网络将会被展开为5层的神经网络，每层一个单词。RNN中的公式如下：

- <img src="http://chart.googleapis.com/chart?cht=tx&chl=  $ x_t $" style="border:none;">
为时刻t的输入。例如，<img src="http://chart.googleapis.com/chart?cht=tx&chl=  $ x_1 $" style="border:none;"> 可以是句子里的第二个单词。

- <img src="http://chart.googleapis.com/chart?cht=tx&chl=  $ s_t $" style="border:none;"> 为时刻t的隐藏状态，也是该神经网络的“记忆”，<img src="http://chart.googleapis.com/chart?cht=tx&chl=  $ s_t $" style="border:none;">基于之前的隐藏状态以及当前步骤的输入：<img src="http://chart.googleapis.com/chart?cht=tx&chl=  $ s_t=f( {U}{x_t} {+} {W}{s_{t-1}}) $" style="border:none;">


参考：http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/
