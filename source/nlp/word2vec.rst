************************
词向量 Word2Vec
************************

模型表达
========

标签是目标词target word和上下文context word的word pair的形式, 有CBOW和skip-gram两种方法
CBOW: 
    (context, word)作为标记数据, 把输入的多个context做加和的预处理
    每个word带来的梯度反向传播中直接加在每个输入的词向量上 (有点儿晕,回头再想)
    输出是二元分类
skip-gram: 
    (word, context)作为标记数据

训练后的模型把context近似的词投影在一起
投影后的表达就是我们想要的词向量
词向量是经过神经网络训练来的


正向传播
============

大结构放出来: z = v_out @ v_in, h = softmax(z)

训练的标签是(word_in, word_out)的pair
模型的输入是语料的one-hot输入, 只有一个类别word_in是1其他都是0
模型的输出标签是softmax, 比对标签是one-hot

模型整体看成三个layer:输入word_in, 隐藏e, 输出h, 两组weights weight_in, weight_out 对应 word_in到e 和 e到h
但是, e没有激活函数, h的激活函数是softmax
整理得到 weight_in @ word_in = e, weight_out @ e = z, softmax(z) = h
假设总共20个单词, 隐藏层5个维度, word_in(20,1), weight_in(5,20), e(5,1), weight_out(20,5), z(20,1)
因为in是稀疏函数, 所以weight_in@word_in其实就是调出weight_in中对应word的weight列向量, 记做v_in, e=v_in
输出时与e相乘的weight_out也可以理解为用每个词的行向量v_out和e点积得到对应的z, 之后通过softmax输出
所以z_wordin_wordout = weight_wordout @ weight_wordin = v_out @ v_in, 输入词的输入向量和输出词的输出向量的点积



反向传播
==============

请参阅softmax的cost function 和求导过程
反向传播中,我们即可以按照三层网络结构来推导, 更可以一步到位用z=vout@vin更新两个向量
cost function J = sum(ylogh), MLE的套路, 这里的sum是两重sum: 输出时所有输出标签的ylogh, 和所有输入的pair
dJ/dz = 1-h if h_out==y else h, 对z的导数要考虑两种情况: z项即为我们的输出标签, 和z项是炮灰标签
直觉上就是如果是输出标签, 我们想h达到1, 所以用1-h做梯度, 离1越远更新越快; 如果不是输出标签, 我们想h是0, 离0越远更新越快
另外MLE用的梯度提升, 如果想做下降就把cost整个加个负号
更新到vin和vout, 就是分别乘上彼此, 不过需要做加和
一个v_in的变化就是每个dJ/dz乘上每个v_out的总和, 相当于以v_out为权重对dJ/dz加和weighted sum error, 运算上就是二者的点积

最后结果取v_in, 我的个人理解是v_in is more of representation of words, v_out is more of mapping function
v_in的输入是one-hot, v_out的输出不论拟合的程度还是有些残差



工程技巧
===========

运算上, 计算v_out的梯度的时候计算量极大, 有多少输出类别, 每个v_out都得计算多少结果, O(n2)
所以想出hierarchical softmax和negative sampling解决计算量的问题
两个方法思路完全不一样, 两个方法都可以用在CBOW和SG上



hierarchical softmax
-----------

v_out成为树结构而不是表格结构, v_out的更新在hierarchical softmax章节中讲了, 更新项从O(n)降到O(logn)
v_in的更新, 则是每个v_in对应的所有子节点的梯度的和
直觉上, v_in一更新, 所有子节点都会对结果产生他们的梯度的程度的影响, 所有v_in的梯度就是这个影响的总和


negative sampling
------------

样本预测一如既往 h = softmax(wx), w为v_out, x为v_in
所谓负采样就是非输出标签的样本的预测 h_neg = softmax(wx), w为其他输出标签的词向量v_out_neg, x不变还是v_in

重点在cost function的简化
原始softmax的cost function J = log(softmax(wx)), softmax这一步需要计算所有的词的exp并求和
hierarchical softmax的cost function J = log(product(softmax(wx))), 用huffman tree代替了求和, 将计算复杂的从O(n)降到O(logn)
负采样只使用一个正样本和常数项个负样本, 达到O(1)

- cost function
J 由正负样本的h简单加和, 去掉了softmax, 只保留几个采样的sigmoid
J = log(sigmoid(v_out_pos@x)) + sum(log(sigmoid(-v_out_neg@x)))
直觉上也讲得通, sigmoid(INF) = 1, log(1) = 0; sigmoid(-INF)=0, log(0)=-INF
v_out_pos@x是正确解要最大化使得正取样项的对数为0, v_out_neg@x是负采样要最小化使得其负数正取越大越好

- 求导 
dJ/dz = 1-h if z是positive sample else -h
推导过程其实就是log(s(x))的导数性质, 1/h h(1-h) 或者 1/(1-h) h(1-h) 负样本s(-x)就等于1-s(x),同时因为有负号所以结果变成负的

更新参数的时候也是只更新v_out_pos和采样到的v_out_neg的参数, 有一点随机梯度下降的意思


原理思考
==========

可以把word2vec看座word pair co-occurance matrix共现矩阵的矩阵分解, 得到weight_in和weight_out两个矩阵
其他的MF方法例如svd也可以得到类似的词向量, 不过svd需要explicit matrix, 就是真正构建出一个矩阵
w2v只需要向量参数, 和word pair, 通过神经网络优化参数, 是一种implicit matrix factorization

用word pair训练可以得到词向量, 用(user, product)训练可以得到用户模型和推荐系统
