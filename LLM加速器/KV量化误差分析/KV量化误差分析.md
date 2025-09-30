## KV量化误差分析

$$
\begin{aligned}
& Attention = softmax(\frac{QK^T}{\sqrt{d_k}})V \\
=> \quad & X_T = S_T \cdot V_T \quad(T表示当前正在处理第T个token)
\end{aligned}
$$



### 1. softmax求导