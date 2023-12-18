# 2.5B样条基函数的计算


令$U={u_0,u_1,u_2,...,u_m}$为节点矢量，假设我们感兴趣的是$p$次基函数，并假设$u$是固定的，$u \in [u_i,u_{i+1}]$
#### 计算节点区间的下标
通过二分法搜索
``` c
//n 控制点的数量减一
//p 曲线的次数
//u 要查找的参数值
//U 节点向量数组
int FindSpan(n,p,u,U){
    if(u == U[n+1]) return n
    low = p;
    hight = n+1;
    mid = (low+hight)/2;
    while(u<U[mid] || u>U[mid+1]){
        if(u<U[mid]) hight = mid;
        else low = mid;
    }
    return (mid);
}
```
#### 计算$N_{i-p},...,N_{i,p}$
假设$u$在第$i$个节点区间内，计算所有的非零B样条基函数的过程如下图的一个倒置三角形
![](/Nurbs/1682777846388.jpg)
然后令$left[j]=u-u_{i+1-j}, \;\;\;\;\;\; left[j]=u_{i+j}-u$，如下图
![](/Nurbs/1682777501183.jpg)
```c
BasisFuns(i,u,p,U,N){
    N[0]=1.0
    for(j=1;j<=p;j++>){
        left[j]=u-U[i+1-j];
        right[j]=U[i+j]-u;
        saved = 0.0;      
        for(r=0;r<j;r++){
            temp=N[r]/(right[r+1]+left[j-r]);
            N[r]=saved+right[r+1]*temp;
            saved=left[j-r]*temp;
        }
        N[j]=saved;
    }
}
```
#### 计算导数
#### 略
