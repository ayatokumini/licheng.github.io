# 综合应用题

## 1.设计一个递归算法，删除不带头结点单链表L值为x的结点

```c
//typedef struct LNode{
//   ElemType data;
//   struct LNode *next;
//}LNode,*LinkList;

//bool InitList(LinkList &L){
//    L=NULL;
//    return true;
//}
//这里在单链表中插入值...
void Delete(LinkList &L,int i,ElemType &x){
    LNode *p;
    p=L;
    if(L==NULL)
        return false;
    while(L->data==x){
        L=p->next;
        p=L;//这里要刷新p的地址
    }
        //删除x结点
    while(L!=NULL&&L->data!=x){
    	if(p->data==x){
        	p->data=p->next->data;
        	p->next=p->next->next;
   		}
    	else if(p->next!=NULL)
        	p=p->next;
    }
}
//好好思考一下怎么用递归呢
//显然是根据p的移动来看，排除p在头指针的情况下，每次出现X执行一次删除操作，返回删除后的链表，否则p向后移动

//递归办法
void Del_X_3(LinkList &L,ElemType x){
    LNode *p;
    if(L==NULL)
        return;
    if(L->data==x){
        p=L;
        L=L->next;
        free(p);
        Del_X_3(L,x);
    }
    else
        Del_X_3(L->next,x);
}
```

## 2.带头结点L，删除x

```c
void Del_x(LinkList &L,ElemType x){
    LNode *p;
    if(L->next==NULL)
        return;
    if(L->next->data==x){
        p=L->next;
        L->next=L->next->next;
        free(p);
        Del_X(L,x);
    }
    else 
        Del_X(L->next,x);
    
}
//p从头至尾扫描单链表



//尾插法建立单链表，不为x时连接到L之后，否则释放


//以上两种方法扫描一遍链表，时间复杂度O(n)，空间复杂度O(1)
```

## 3.L为带头结点单链表，从尾到头反向输出每个结点的值

```c
//保证每次输出时间复杂度O(1)，那如何能让指针先指到尾部呢，考虑递归倒着输出？
void Reverse(LinkList &L){
    if(L->next!=NULL)
        Reverse(L->next);
    if(L!=NULL)
    printf("%d\n",L->data);   
}
```

## 4.带头结点单链表中删除最小值结点（唯一）

```c
//最小值、无序，那就依次比较下去，留一个指针保留最小值结点，如果是最后一个结点不好删除怎么办呢，那就找前置结点
void MinDel(LinkList &L){
    if(L->next==NULL)
        return false;
    LNode *p,*q,*r;
    p=L->next;
    q=L->next;
	while(q->next!=NULL){
   		if(p->next->data>q->next->data){
    		p=q;
    		q=q->next;
   		} 
   		if(p->data<=q->data)
       		q=q->next;
	}
    r=p->next;
    p->next=p->next->next;
    free(r);//释放p->next
    return L;
}



    
```

## 5.带头结点单链表就地逆置，就地：辅助空间复杂度O(1)

```c
//那咋办嘛，p,q两个指针一前一后移动，用temp交换p->data,q->data,可是q没法往前移动怎么办就用r每次存上次q的位置，判断q->next为r就交换，这个理解完全没有考虑到链表的优势，断开排成新链表并不影响空间复杂度！！！
//解法1
LinkList Reverse(LinkList &L){
    LNode *p,*r;
    p=L->next;
    L->next=NULL;
    while(p->next!=NULL){
        r=p->next;
        p->next=L->next;
        L->next=p;
        p=r;
    }
    return L;
}
//解法2：让每个结点next指向它的前驱就好了，用r保留这个节点的后继
LinkList Reverse_2(LinkList &L){
    LNode *pre,*p=L->next,*r=p->next;
    p->next=NULL;
    while(r!=NULL){
        pre=p;
        p=r;
        r=r->next;
        p->next=pre;
    }
    L->next=p;
    return L;
}
```

## 6.带头结点单链表，设计算法递增有序

```c
//递增就比大小呗，暴力解法比较大小把小数交换到前面，双指针操作，指针pre在有序表中移动，r在无序区域移动，pre中找到的值和r里比较大小，选择插入位置，可是这样时间复杂度是O(n^2)诶，怎么能变小一点呢
//答案里给出了一个建议：复制到数组里，再用时间复杂度O(nlog_2 n)的排序算法，再插入链表，以空间换时间
//先写暴力解法
void SortL(LinkList &L){
    LNode *p=L->next,*pre;
    LNode *r=p->next;				//保留p后继结点
    p->next=NULL;					//断开,只剩下L->p->NULL
    p=r;							//p指向无序表的开头
    while(p!=NULL){					
        r=p->next;					//在无序表里每次往下找
        pre=L;						//pre从头指针开始，在有序表里找
        while(pre->next!=NULL&&pre->next->data<p->data)//其实是比较有序表里每个数据和无序表第一个元素的大小
            pre=pre->next;			//递增就往下找，找到第一个减小值位于pre->next
        p->next=pre->next;			//把p插入到pre后继的前面
        pre->next=p;				//把p和pre尾部链接
        p=r;						//p继续在有序无序区之间操作
    }//这个循环真是太严谨啦！！！respect！！！
    
}
//那这里顺便写一下快速排序算法吧！重点是递归思想
void Swap(ElemType &x,ElemType &y){
    ElemType temp;
    temp=x;
    x=y;
    y=temp;
}
void Sort(ElemType array[],int i,int j){
    int e=array[i];
    i++;
    while(i<j){
        if(e<=array[j])
            j--;
        if(e>array[j]){
            Swap(&(array[i-1]),&(array[j]));
            j--;
        }
        if(e<=array[i])
            i++;
        if(e>array[i]){
            Swap(&(array[i-1]),&(array[i]));
            i++;
        }
        Sort(array[],i,j);
    }
}
```



## 7.带头结点，无序，删除表中介于两值之间的元素

```c
//
void DeleteElem(LinkList &L,ElemType m,ElemType n){
    if(L->next==NULL)
        return false;
    LNode *p=L->next,*q=p->next;
    if(q->next==NULL){
        if(q->data>m&&q->data<n)
            p->next=NULL;
        return;
    }
    while(q->next!=NULL){
        if(q->data>m&&q->data<n){
            p->next=q->next;
        }
        p=p->next;
        q=q->next;
    }
    return L;
}
```

## 8.给定两个单链表，找出公共结点

```c
void Common(LinkList L1,LinkList L2){
    int len1=length(L1),len2=length(L2);
    LinkList longList,shortList;
    if(len1>len2){
        longList=L1->next;
        shortList=L2->next;
        dis=len1-len2;
    }
    if(len1<=len2){
        longList=L2->next;
        shortList=L1->next;
        dis=len2-len1;
    }
    while(dist--)
        longList=longList->next;
    while(longList!=NULL){
        if(longList==shortList)
            return longList;
        else{
            longList=longList->next;
            shortList=shortList->next;
        }
    }
    return NULL;
}
```

## 9.head为头指针，节点结构(data/int,next),按递增次序输出各节点数据元素，不允许使用数组

```c
void MinDel(LinkList &head){
    while(head->next!=NULL){
        pre=head;
        p=pre->next;
        while(p->next!=NULL){
            if(p->next->data<pre->data)
                pre=p;
            p=p->next;
        }
        printf(pre->next->data);
        u=pre->next;
        pre->next=u->next;
        free(u);
    }
    free(head);
}
```

## 10.带头结点单链表A分解为A和B，使得A中含有序号为奇数的元素，B中含有序号为偶数的元素，且保持其相对顺序不变

```c
void Discreat(LinkList &A){
    LinkList B=(LinkList)malloc(sizeof(LinkList));
    B->next=NULL;
    LNode *p,*q,*r;
    p=A;
    q=B;
    r=p->next;
    while(r->next!=NULL){
        p->next=r;
        p=p->next;
        r=r->next;
        p->next=NULL;//p,r均向后移位
        if(r->next!=NULL){
            q->next=r;
            q=q->next;
            r=r->next;
            q->next=NULL;
        }
    }//while
    return B;
}
```

11.线性表C={a1,b1,a2,b2...an,bn}拆分为A，B，（B为倒序）

```c
//把q的插入方法变成头插
void Discreat_2(LinkList &C){
    LinkList B=(LinkList)malloc(sizeof(LinkList));
    LinkList A=(LinkList)malloc(sizeof(LinkList));
    B->next=NULL;
    A->next=NULL;
    LNode *p,*q,*r;
    p=A;
    r=C->next;
    while(r->next!=NULL){
        p->next=r;
        p=p->next;
        r=r->next;
        p->next=NULL;//p,r均向后移位
        if(r->next!=NULL){//把这里改成头插即可，要把r后面的断开诶
            q=r;
            r=r->next;
            q->next=B->next;
            B->next=q;
        }
    }//while
    return B;
}
```

12.递增有序线性表，去掉数值相同的元素

```c
void Delrepeat(LinkList &L){
    LNode *p,*pre;
    pre=L->next;
    if(pre==NULL)
        return;
    p=pre->next;
    while(p->next!=NULL){
        while(pre->data!=p->data){
            p=p->next;
            pre=pre->next;
        }
        q=p;
        p=p->next;
        free(q);
        pre->next=p;
    }
}
```

13.两个递增有序单链表，归并为一个递减有序单链表，利用原来两个单链表的结点

```c
//逆置一般都要头插吧
void Combine(LinkList &A,LinkList &B){
    LinkList C=(LinkList)malloc(sizeof(LinkList));
    LNode *p=A->next,*q=B->next,*r,*s;
    r=C;
    while(p->next!=NULL&&q->next!=NULL){
        if(p->data>=q->data){
            s=q;
            q=q->next;
            s->next=r->next;
            r->next=s;
        }
        else{
            s=p;
            p=p->next;
            s->next=r->next;
            r->next=s;
        }
    }
    if(p->next!=NULL)
        q=p;
    while(q->next!=NULL){
        s=q;
        q=q->next;
        s->next=r->next;
        r->next=s;
    }
        
    return C;
}
```

14.A，B带头结点递增有序，从公共元素中产生单链表C，不破坏A，B结点

```c
void Get_common(LinkList A,LinkList B){
    LNode *p,*q,*r;
    LinkList C=(LinkList)malloc(sizeof(LinkList));
    p=A->next;
    q=B->next;
    r=C->next;
    while(p&&q){
        if(p->data<q->data){
            p=p->next;
        }
        if(p->data>q->data){
            q=q->next;
        }
        if(p->data==q->data){
            r->data=p->data;
            r=r->next;
            p=p->next;
            q=q->next;
        }
    }
    r->next=NULL;
}
```

15.求A，B交集，存放于A链表中，两链表递增

```c
//同14，不过要存在A中,A中不同的元素直接删掉就是了
void Get_common_2(LinkList &A,LinkList B){
    LNode *p,*q,*r;
    p=A->next;
    q=B->next;
    while(p&&q){
        if(p->data<q->data){//删除p
            p->data=p->next->data;
            r=p->next;
            p->next=p->next->next;
            free(r);
        }
        if(p->data<q->data){
            q=q->next;
        }
        if(p->data==q->data){
            p=p->next;
            q=q->next;
        }
    }
    return A;
}
//三种情况：A和B链表返回A，A和B返回C，C返回A和B
```

16.判断B是否是A的连续子序列

```c
//简单写个思路，在A中遍历找到和B相同开头整数结点，再一起往后移动，移到B截止，有不同就跳出，再在A里看有没有B的开头数，重复操作
```

17.判断带头结点的循环双链表是否对称

```c
//从头尾往中间依次比较
```

18.两个循环单链表，链表头指针h1,h2编写函数将h2链接到链表h1之后，要求仍保持循环链表

```c
//把h2的头接到h1尾，h1头接h2尾
```

19.设一个带头结点的循环单链表，反复找出节点值最小的结点并输出，然后将该结点从中删除，直到单链表空为止，再删除表头结点

```c
//结束条件变了而已
```

20.带表头结点的非循环双向链表，(pred,data,next,freq)，每当链表进行一次Locate(L,x)运算时，令元素值x结点freq值增一，并按freq递减排列，最近访问的结点排在freq相同的结点前面

```c
//对x操作freq+1,比较x之前的freq往前移动，相同就再往前移
```

21.（2009真题）(data,link)头指针list，查找倒数k位置上的结点，查找成功输出data值返回1，否则返回0

```c
//求length,数到length-k+1位置
```

22.（2012真题）找到共同后缀的起始位置

```c
//求长度差，再以长度差为起始依次比较找到第一个p=q的地方
```

23.（2015真题）删除绝对值相等的结点，

```c
//用abs函数就好啦或者分类讨论...?...:...
```

24.判断链表是否有环，有的话找到环的入口并返回，否则返回NULL

```c
//找到链表结尾，第二次重复出现的就是环的入口即出口
```

25.（2019真题）重排结点得到L'=(a1,an,a2,an-1,a3,an-2,...)

```c
//分成两半，另一半倒着插
```


