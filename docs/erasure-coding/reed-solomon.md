

### Reed Solomon编码

Reed Solomon（RS）编码是最典型的MDS（Maximum Distance Separable）编码。

* 编码

$$
A_{(k+m)\times k}\times D_{k\times 1}
=
\begin{bmatrix}
1 & 0 & 0 & \cdots & 0\\
0 & 1 & 0 & \cdots & 0\\
\vdots & \vdots & \vdots & &\vdots\\
0 & 0 & 0 & \cdots & 1\\
1 & 1 & 1 & \cdots &1\\
1 & 2 & 3 & \cdots &k\\
\vdots & \vdots & \vdots & & \vdots\\
1^{m-1} & 2^{m-1} & 3^{m-1} & & k^{m-1}
\end{bmatrix}
\times 
\begin{bmatrix}
D_1\\
D_2\\
\vdots\\
D_k
\end{bmatrix}
=
\begin{bmatrix}
D_1\\
D_2\\
\vdots\\
D_k\\
P_1\\
P_2\\
\vdots\\
P_m
\end{bmatrix}
$$

* 解码

$$
A^\prime \times D = R^\prime\\
\Leftrightarrow 
D= (A^\prime)^{-1}R^\prime
$$

参考文献

[1] Polynomial Codes Over Certain Finite Fields, *Journal of the society for industrial and applied mathematics*, 1960

