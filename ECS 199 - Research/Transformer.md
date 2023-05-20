# Transformer

Natural Language Processing - BERT and GPT based on Transformer

RNN - neural network before Transformer

缺点: 文本长度限制

RNN - 比较简单 Transformer - 比较复杂



## 总体架构

输入 -> 黑盒(Transformer) -> 输出

Transformer: Encoders -> Decoders

第一步：读 第二步：写

读：有很多步 （很多encoder）写也有很多decoder

因为读和写都需要一个过程，like眼睛看->脑袋想

Self attention 自注意力层



Encoder: Self Attention -> Feed Forward

Decoder: Self Attention -> Encoder Decoder Attention -> Feed Forward



## 计算注意力

Transformer使用了注意力的成果来组建自己的网络

注意力的计算方法：

Input: a, b

Embedding: x1, x2 (Two 4D vectors)

QKV vector: WQ, WK, WV

Queries: x1, x2

Keys: x1, x2

Values: x1, x2



Score: q1*k1, 

q1*k2



Divide by 8

soft max

soft max*Value

Sum it up

单头注意力：一组qkv

多头注意力：多组qkv, then concat

## 词向量编码

Transformer不同于RNN，在处理数据的时候不考虑数据的位置信息，所以需要在数据中加入**位置信息**，以让处于 **不同位置**的**相同**数据有所不同，相互区分

普通编码（输入文本embedding之后）+ 位置编码 （Positional Embedding）=最终编码 (Final Embedding)

