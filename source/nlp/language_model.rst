********
语言模型 Language Model
********

语言模型
=======
n-gram
tf, tf-idf
bag of word
vector space
Doc matrix: (docs, words)

- n-gram
n is the length of conditional words to the target word
for n=2, aka 2-gram: p(sentense) = product( p(word_i|word_i-1) )
for other n, p(word_i) will be conditional to previous n-1 words

- max likelihood
p(word) is the knowledge learnt from corpus
the model looks for the largetst p(sentense), via vebirt algorithm for hidden markov model



统计模型
========
观测作为输入, 文字text作为输出, 以语音voice为例:
贝叶斯: p(t|v) = p(v|t) pt / pv
其中p(v|t)为声学模型, pt就是语言模型, 即一列语料出现的概率
假设一句话S, 这句话的概率pS就是句子中每个词出现的概率的积,product( p(w|all words) ) = p(all words and w)/p(all words)
对给定语料库,p(w)就是w的出现频次count(w), p(all words and w)也就是到w为止的语料出现的频次count(all words and w)

- 平滑 smooth
如果有语料没有出现过, p(t)=0会影响运算, 又如果某个文字后面跟的字在语料中正好每次都一样,我们又会得到pw=1. 
为了处理极端概率, 我们需要做平滑处理, 使没出现过的语料概率不为0,且所有语料概率的和为1,
最简单的add-one smooth, 也叫laplacian smooth, 将全部语料假设为多出现过一次, 这样p(w)的分子多加1,分母多加所有可能的语料的数量

- n-gram模型
n-gram模型假设了每个词的概率只与相邻n个词相关, 一般取1或者2, 写作context(w)
若n=2, pw = p(w|context(w)) = p(w|w_prev,w_prev_prev,w_next,w_next_next)
此刻回看语言模型pt, 我们将p(w)的先验概率从整个语料的概率简化到了周围n个词的概率
即便如此参数依然巨大, 1E6个词,2-gram就有1E24个pw了, 每一个pw就是一个参数


隐语义分析
=======
Doc matrix的矩阵分解得到隐语义向量, 类似user-item rating matrix的分解
用SVD就可以, 假设得到k维隐向量, 可以理解成k个"主题"
Doc vector (k,): 一篇文章由k个主题成分的构成
Word vector (k,): 一个单词的k个主题成分的表达
Semetic vector(w,): 一个主题的单词使用分布

- pLSA
原LSA对文章单词的假设是正态分布, 但其实泊松分布更合适, pLSA改进了分布

- 对比word2vec
LSA用了文章为单位的词袋BOW模型, 使用了全局统计信息
word2vec用了多元词组n-gram模型, 使用了局部信息, 单词含义更准确, 

- GloVe
glove模型也是用了全局共现矩阵的分解
结合了LSA 算法和Word2Vec 算法的优点，既考虑了全局统计信息，又利用了局部上下文