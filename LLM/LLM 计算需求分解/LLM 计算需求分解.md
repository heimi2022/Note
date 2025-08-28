# QWEN-4B的数学表达（decoder-only）

- n1 ——词汇表大小 vocab_size，151936
- n2 ——hidden_size, 2560
- n3 ——输入序列长度 seq_len，由每次的输入决定     

​    本设计不考虑PD分离，因此只能等上一个输入算完，再进行下一个输入的计算

- n5 ——自注意力头数 num_attention_heads，32
- n6 ——单头宽度，n6= n2/ n5  = 2560/32 = 80
- n7 ——全连接层宽度 intermediate size
- n8 ——解码器层数 num_hidden_layers(每一层都包含MHA和MLP）
- 限定最大输入序列长度为max_seq_length 32768=32k，最大token生成数为

## 输入序列生成 (软件实现，生成**seq_len \* 2560大小的矩阵作为decoder输入）**

注：一份作为decoder input，一份作为残差连接输入，怎么存取？

**文本to词碎**：输入序列生成是任意一段文本根据词汇表(size=n1*n2=151936*2560) 转化为一个词碎序列的过程，其中<unk> <s> </s>分别为未定义、序列开头、序列结尾。一个单词可以被拆分为若干词碎，例如unaffable能分割成una␣ff␣able。

**词碎to矩阵**：每个词碎都会被嵌入到一个行向量，该行向量的尺寸为1*n2，将所有的行向量按顺序排列起来，组成矩阵D=(d1,d2,...,dn1)， 则词典的尺寸为n1*n2 = 102400*4096。

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=OTA3ZDY5NDhjMDJiNjE2ZjRmMzNlZjc3OTQ3OGU2MzNfdVk0WVowOE5XMjJFd2FvZU11aHJmNHBZT3VKQnlBc21fVG9rZW46RzFRTGJ2OXZXb0FVVXN4NFoybmNvUERMbkpnXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

对于输入，则会被转换成**n3\*n2 = seq_len \* 2560**的一个矩阵Z，Z会经过n8层decoder的计算，最终得到输出。

## 解码器 decoder

1. #### RMSNorm层归一化 (把这一层Norm也放在上位机做更方便，但要考虑后续几层读写的延时）

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=Njc2Y2YyODc4NmY3YmU3MWM3YzVmYTQyNTQxOWRmNjJfNW11NG1aRXplQ3RUNWxmUVRyWU03VTlFcDdYRE82ZHdfVG9rZW46QndsTmJnR21ab2ZKY1J4S2xiSGNyTUN1bkdlXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

暂时无法在飞书文档外展示此内容

输入：[seq_length,2560] → 输出：n3*2560即 [batch_size,seq_length,2560]

计算量：seq_length*2560次RMSNorm

1. #### 自注意力子层 （启动KV cache的推理过程，GQA=4）

##### 附：MHA GQA MQA MLA 简介 这些手段可以降低KV cache代价

注意：多头不影响softmax的全局性，直接拼接即可，这是因为是纵向切分（隐藏层维度切开），而flash attention是沿着seq length维度切开，两者注意区分

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmQwODY0OWFhYmI3NmU4ZTFiMzY2N2I0NTM4MzM5ZWFfaUNWajJXeU9sUmZLMjFiV2RmTGJuR2VBbnhyR1BuNGtfVG9rZW46QnJZRWJuT0ZYb2RqQU94MnY4RGNOMEUwbktkXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

MLA：MLA利用低秩分解的思想将KVCache进行**低秩压缩**，在不影响模型能力的前提下，大幅减少了KVCache的内存占用。但另一方面，MLA同时带来了中间变量的内存大小以及总的计算复杂度提高，对硬件上算子的高性能实现提出了新的挑战。

GQA计算量和访存量分析

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=YmFlNzIxNzQ0ODBkZTVlOGJiNDJiNjFmNjA5NjhiYzBfWllmWUtLWk9rY2d3enVnY21qbWtHQ2RmSG9zZzlRZEVfVG9rZW46Tjltc2JYQWFZb2QxREZ4RVhtWmNWeDBibm9lXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

MLA计算量和访存量分析

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=MDUzYzMwNTQ1ZWQ0OTJlMWE4NTUxNjhjNTVmNTM4ZDBfaTQyTDNPc3IwVGlkY2NWY3NuZ2ZsUDg0bGp1Q3FSZ2dfVG9rZW46QUNDNmJJUVpQb256OFB4Q0h3OWNzcEc4bk9iXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

##### prefill阶段

暂时无法在飞书文档外展示此内容

需要非常注意的是进入softmax之前有一个上三角掩码，因此QK有一部分可以不算，这一部分要想办法调度好

计算量：1）Q权重投影 ：QKV生成：3*n3*4096次DOT，每个DOT的规模为4096次乘累加

​             2）QK乘法：32*n3*128次DOT，每个DOT的规模为128次乘累加

   3）Softmax计算：32*n3*n3次softmax

   4）atten score生成：32*n3*128次DOT，每个DOT的规模为n3次乘累加

#####  decode阶段，基于KV cache，以第i个token为例（第一个token为prefill阶段生成）

######  Kv cache原理

 transformer官方use_cache这个参数默认是True，但是它只能用于Docoder的模型，**本质因为Decoder有Casual Mask，在推理的时候前面已经生成的字符不需要与后面的字符产生attention(第k行的Q不需要第k+1列的K信息），从而使得前面已经计算的K和V可以缓存起来**。若采用KV cache则Q1K1即Softmax的输入为一个向量

 以deepseek-v2-lite为例：Head 数是 32，模型层数为 27，K/V 的 Head dimension 为 128，当序列长度为 8192，**并发数**为 1 时，按照 BF16 格式存储的 KVCache 即可达到 **5GB**, 存储代价会随着并发数和序列长度增长而增长。

 https://zhuanlan.zhihu.com/p/662498827

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=NzA4YmQ1ZDA2MDRmNmQ1YzMxODRjZDMxMGMxMjU3OTNfc2xwdDQxMURrRlJGZGxCRDVKZGpUUVlHbElyVGI4d01fVG9rZW46UnczeGJyRFNQb0Z1cVF4dk5xZGN3Nkw4bm1IXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

######  运算解析

暂时无法在飞书文档外展示此内容

1. #### 残差子层

Atten score * Wo + 输入矩阵

暂时无法在飞书文档外展示此内容

1. #### 全连接子层

暂时无法在飞书文档外展示此内容

# 运算流分析

暂时无法在飞书文档外展示此内容

### prefill阶段

暂时无法在飞书文档外展示此内容

### decode阶段

#### Standard Attention

暂时无法在飞书文档外展示此内容

#### Flash Attention

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=MjQxMTA0NzQxNDMxNzNiMWU4MTkxOGZjMzc2MzEyMjJfd0xmMjNvTUZkU1NialhYY0VNYmRjMWk0UzFWb2NIcWdfVG9rZW46Tm9BYmJnVFlXb0w3Y1l4Q002VWNzd2Fibk1iXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

考虑KV cache，则现有基本条件：Q（1*128*16b） K V（seqlength*128*16b）存储在DRAM/HBM中，设片上SRAM的大小为M KB（T28 64KB SRAM less than 0.3mm2

设每块能存的KV cache大小为128*n*16b = X 

（2*(128*n*16) /1024 ）kb +（2+2+2）kb +（n*16 / 1024） kb = M * 8 kb   →  **当M = 64KB** ，n ≈ 128   

​        KV cache                           Q K V                   中间结果

则**循环8次**可以算完最大seq_length=1024的情况

暂时无法在飞书文档外展示此内容

# 硬件映射与数据流分析

## 时间维度分析

 设我们需要计算的两个矩阵大小为1*K(A)和K*N(B)，它们相乘的结果为M*N(C)，则

暂时无法在飞书文档外展示此内容

```Plain
for m=0 to M-1
    for n=0 to N-1
        for k=0 to K-1
            C[m,n] = C[m,n] + A[m,k]*B[k,n]
```

一个PE阵列可以计算MP*KP和KP*NP的矩阵，得到MP*NP的结果，该PE阵列计算的是MP*NP个KP*KP大小的乘累加，其中PE阵列规模确定后，MP*NP*KP应为一个定值，设M=MT*MP, N=NT*NP则

暂时无法在飞书文档外展示此内容

```Python
#时间维度，一个PE ARRAY要重复计算的次数：KT*MT*NT次
for mt=0 to MT-1
    for nt=0 to NT-1
        for kt=0 to KT-1
            #空间维度，一个PE内部运算：MP*NP个KP*KP大小的乘累加
            for mp=0 to MP-1
                for np=0 to NP-1
                    for kp=0 to KP-1
                        C_TEMP[mt*MP+mp,nt*NP+np,kt] += A[mt*MP+mp,kp]*B[kp,nt*NP+np]
             #需要把kt迭代完才能得到C中的一个元素
             C[mt*MP+mp,nt*NP+np] += C_TEMP[mt*MP+mp,nt*NP+np,kt]
```

首先，对于访存和存储开销，我们从时间维度讨论，MT NT KT按照什么顺序循环，有下面几种情况：

1. **KT内循环，MT/NT外循环：最大存储MP\*NP个psum（**MN\*NP\*KP为定值时，KP越大，存储量越小**），一次从SRAM更新(MP\*KP+KP\*NP)\*BW bit的数据（最符合矩阵运算直觉）**
2. MT内循环，NT中循环，KT外循环：最大存储MT*NT*KT个psum，一次从SRAM更新MP*KP或KP*NP数据
3. **MT内循环，KT中循环，NT外循环：最大存储MT\*MP\*NP个psum，一次从SRAM更新MP\*KP或KP\*NP数据(需要在KT更新时与上一个KT循环累加，否则需要存储MT\*KT\*MP\*NP个psum）**

**平均每次更新{MP\*KP+KP\*MP/MT}\*BW bit数据**

1. NT内循环，MT中循环，KT外循环：同2
2. NT内循环，KT中循环，MT外循环：同3

存储空间代价和访存功耗开销之间需要trade off，一定计算量下分块的比例需要trade off

## 空间维度分析

### 附：关于脉动阵列的结论

一个 N×N 脉动阵列计算两个 N×N 矩阵乘法的最小周期数为 **3N-2**。实际实现中：

1. 小矩阵 (N≤32)：接近理论最优值
2. 大矩阵 (N>128)：采用分块优化，总周期 ≈ ceil(N/P)³ × (3P-2)
3. 峰值效率：当 N→∞ 时，计算效率趋近于 N²/3

> 设计启示：脉动阵列在 N≥32 时达到 >80% 的硬件利用率，是矩阵计算的黄金架构

### tiling分析

以计算**256\*2560和2560\*128**矩阵乘法为例

本部分假定已经确定硬件计算负载：M * K和K * N的矩阵乘法，PE阵列应该如何实现才最高效呢？

总计算量：M * N次K组点乘，约为M * N * K个PE的大小（一个PE(INT8 * INT8)的经验面积值为600um2），一个block为2*3=6mm2,设pr面积利用率为60%(乐观估计），则PE array可占3600000um2，则**PE的个数应小于6000**

1. **脉动阵列→时间换空间，瓶颈为延时**                  

需要$M*N$个PE,设一个PE(一次乘累加）需要1个cylce完成（面积限制脉动阵列最多为$64*64$）77

1. **广播并行→空间换时间，瓶颈为面积/功耗/PE间、PE与cache间互联**

需要$M*N$个PE，每个PE可以计算K组乘累加，完成的周期数却决于K组乘累加的周期数，计算效率高

（M*N*K<6000）18

1. **分块，块与块之间脉动，块内部广播并行**

三个维度的分块数分别为T T T_N（最好T_M = T_K =T_N，这样脉动起来比较方便），映射到硬件上就是T*T个PE（脉动），每个PE计算m*k和k*n的矩阵乘法（广播 并行），则需要满足T*T*(mnk)<6000

面效 = 计算量/（面积*周期数*周期）

能效 = 计算量/（功耗*周期数*周期）

**整体趋势：分块数越多(T越大），面积功耗越小，延时越低**

**如何找到T最优的解？**

- T=1 M=4 N=8 K=128 计算量4*128和128*8矩阵乘      周期=128组乘累加的周期

​                                    面积32个128组乘累加

- T=2 m=4 n=4 k=64   计算量8*128和128*8矩阵乘      周期=4个64组乘累加周期

​                                    面积64个64组乘累加

- T=4 m=2 n=4 k=32   计算量8*128和128*16矩阵乘    周期=10个32组乘累加周期

​                                    面积128个32组乘累加

- T=8 m=2 n=2 k=16   计算量16*128和128*16矩阵乘  周期=22个16组乘累加周期

​                                    面积256个16组乘累加

- T=16 m=1 n=2 k=8   计算量16*128和128*32矩阵乘  周期=46个8组乘累加周期

​                                    面积512个8组乘累加

- T=32 m=1 n=1 k=4   计算量32*128和128*32矩阵乘  周期=94个4组乘累加周期

​                                    面积1024个4组乘累加

- T=64 m=n=k=1        计算量64*64和64*64矩阵乘      周期=190个一次乘累加周期

​                                   面积4096个乘累加

### PE维细粒度硬件优化

暂时无法在飞书文档外展示此内容

```Python
#时间维度，一个PE ARRAY要重复计算的次数：KT*MT*NT次
for mt=0 to MT-1
    for nt=0 to NT-1
        for kt=0 to KT-1
            #空间维度，一个PE内部运算：MP*NP个KP*KP大小的乘累加
            for mp=0 to MP-1
                for np=0 to NP-1
                    for kp=0 to KP-1
                        C_TEMP[mt*MP+mp,nt*NP+np,kt] += A[mt*MP+mp,kp]*B[kp,nt*NP+np]
             #需要把kt迭代完才能得到C中的一个元素
             C[mt*MP+mp,nt*NP+np] += C_TEMP[mt*MP+mp,nt*NP+np,kt]
#时间维度，一个PE ARRAY要重复计算的次数：KT*MT*NT次
for mt=0 to MT-1
    for nt=0 to NT-1
        for kt=0 to KT-1
            #GP LEVEL 并行
            parallel for mp=0 to MP-1
                parallel for np=0 to NP-1
                    #PE level
                    #预处理部分 pipe1
                    A_e1[mt*MP+mp,kp] = (A_e[[mt*MP+mp,kp]-bias)^A_s[mt*MP+mp,kp]+A_s[mt*MP+mp,kp]
                    B_e1[mt*MP+mp,kp] = (B_e[[mt*MP+mp,kp]-bias)^B_s[mt*MP+mp,kp]+B_s[mt*MP+mp,kp]
                    A_m1[mt*MP+mp,kp] = (A_m[[mt*MP+mp,kp]-bias)^A_s[mt*MP+mp,kp]+A_s[mt*MP+mp,kp]
                    B_m1[mt*MP+mp,kp] = (B_m[[mt*MP+mp,kp]-bias)^B_s[mt*MP+mp,kp]+B_s[mt*MP+mp,kp]
                    #乘法部分  pipe2
                    e_add[mt*MP+mp,nt*NP+np,kp] = A_e1[mt*MP+mp,kp]+B_e1[mt*MP+mp,kp]
                    m_mul[mt*MP+mp,nt*NP+np,kp] = A_m1[mt*MP+mp,kp]+B_m1[mt*MP+mp,kp]
                    #对指部分  pipe3
                    e_max[mt*MP+mp,nt*NP+np]      = findmax(e_add)
                    sub_e[mt*MP+mp,nt*NP+np,kp]   = e_add - e_max
                    m_shift[mt*MP+mp,nt*NP+np,kp] = shift(m_mul,sub_e)
                    #加法部分  pipe4
                    add1,add2[mt*MP+mp,nt*NP+np] = add_tree{m_shift}
                    add_m = add1 + add2
                    #规格化部分 pipe5
                    dot_out[mt*MP+mp,nt*NP+np,kt] = norm(e_max,add_m)
                    #psum累加部分 pipe6
                    C_TEMP[mt*MP+mp,nt*NP+np,kt] += dot_out[mt*MP+mp,nt*NP+np,kt]
                    
             #需要把kt迭代完才能得到C中的一个元素
             C[mt*MP+mp,nt*NP+np] += C_TEMP[mt*MP+mp,nt*NP+np,kt]
```

观察1：A B在预处理阶段无交互，可作为共享预处理

观察2：乘法与findmax长路径匹配                              

观察3：压缩树中间结果累加(add_m)和规格化(dot_out）和psum累加(C_temp）部分可融合

暂时无法在飞书文档外展示此内容

# 关键IP硬件实现

# PE Array

添加新数制：mxfp4/mxfp8  每32为一组共享shared exponent

该数制仅在系统中增添加法运算，硬件代价增加很小

## Online Softmax

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=NzVmNTMzNzZkOWEwZWZlODczYmQ2NjNiNGM5YWIyYjRfVVlVaTZSS0tVVHFrNGxabXROMWVoNk1aRThuQUF1cWFfVG9rZW46VE00Q2JyVU9Gb290SHh4a00wd2NZdWpTbjBEXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

支持数制：BF16 FP16 FP8 INT8

Stage1 ： 线性运算单元直接传输数据，寻找最大值，计算分母和   **Done**

Stage2：  从SRAM取值，计算分子                                             **Done**

Stage3：  分子/分母除法运算                                                    **TODO**

思路一：换底运算配合log线性近似，精度不够再添加对称误差补偿系数

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=MDMwZDdkMTM5YzRjMmI5NzFiZWMzMjhmOTA1OGFkYzNfNHd2d0R5VDFLUFNocEVNU3ZmbnplanBuRGNMV3Y4aW5fVG9rZW46Q2JOV2JXdkVjbzdFNDh4a2RSQ2NTVUVPbmZkXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)

思路二：SOTA(DAC 24)可改进，均匀量化转变为非均匀量化

![img](https://zcnb1ywr4qsk.feishu.cn/space/api/box/stream/download/asynccode/?code=MzQxMmExZjVlNWZkYTljMjRiMDU0NWFjYzM1ZjY3YmJfWW5xT2dwTWZSeE5Dc2gxdU91b2NGbnBLMjF2Y0hXckdfVG9rZW46RWtZUGJUUjRkb3lWUmp4NHEzWWNRc2JxbnRjXzE3NTU3NjU5NzE6MTc1NTc2OTU3MV9WNA)
