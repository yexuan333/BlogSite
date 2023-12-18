# 1.3Bezier曲线


贝塞尔曲线是一种幂基曲线。一条n次Bezier曲线可以表示为
$$
C(u)=\sum_{i=0}^nB_{i,n}(u)P_i,0\leq u \leq 1
$$
其中，基函数（也称为混合函数）$B_{i,n}(u)$是著名的n次Bernstein（伯恩斯坦多项式），其定义为
$$
B_{i,n}(u)=\frac{n!}{i!(n-i)!}u^i(1-u)^{n-i}
$$
 ![](/Nurbs/1682141901756.jpg)

#### Bernstein多项式性质
- 非负性：对所有的$i,n$和$0 \leq u \leq 1$ ,$B_{i,n}(u) \geq 0$
- 规范性：$\sum_{i=0}^nB_{i,n}(u) = 1$,对所有的$0 \leq u \leq 1$
- 端点性质：$B_{0,n}(u)=B_{n,n}(u)=1$
- 最大值$B_{i,n}(u)$在区间$[1,0]$内只达到一次，既仅当$u=\frac{ i}{n}$时，$B_{i,n}(u)$取得最大值
- 对称性：对于任意$n \gt 0$,多项式$B_{i,n}(u)$是关于$u=\frac{1}{2}$对称的
- **递推性质：$B_{i,n}(u)=(1-u)B_{i,n-1}(u) +uB_{i-1,n-1}(u)$,这里规定当$i \lt 0$或$i \gt n$时，$B_{i,n}(u) = 0$**
- **求导公式：$\dot{{B}}_{i,n}(u)= \frac{dB_{i,n}(u)}{du} = n(B_{i-1,n-1}(u)-B_{i,n-1}(u))$**

#### De Casteljau's algorithm德卡斯特里奥算法
是计算伯恩斯坦形式的多项式或贝塞尔曲线的递归方法。

#### $$P_{k,i}(u_o)=(1-u_0)P_{k-1,i}(u_o)+u_0P_{k-1,i+1}(u_o)$$

<div style="display: flex; justify-content: center;">
<image src="/Nurbs/240px-Bézier_2_big.gif"  style="width: 30%; height: auto; margin-right: 10px;">
<image src="/Nurbs/Bézier_3_big.gif"  style="width: 30%; height: auto; margin-right: 10px;">
<image src="/Nurbs/Bézier_4_big.gif"  style="width: 30%; height: auto; margin-right: 10px;">
</div>
![Bézier_2_big]()![Bézier_3_big]()![Bézier_4_big]()

利用DeCasteljau算法计算Bezier曲线上的一个点
 ![](/Nurbs/De%20Casteljau's.jpg)

```c
DeCasteljau(P,n,u){
    for (i=0;i<=n;i++)
        Q[i]=P[i];
    for (k=1;k<=n;k++)
        for(i=0;i<=n;i++)
            Q[i]=(1.0-u)*Q[i]+u*Q[i+1];
    c=Q[0];
}
//先求出P0 P1 ....Pn
//在求出P1,0 P1,1 P1,2 ....P1,n-1
//在求出P2,0 P2,1 P2,2 ....P2,n-2
//...
//在求出Pn-1,0 Pn-1,1
//Pn,0 便是曲线在u时的点
```
