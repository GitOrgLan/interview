###链表中倒数第k个结点
##
题目描述  
>输入一个链表，输出该链表中倒数第k个结点   

[牛课网链接](http://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) 

##  
```
/*
 * 两个指针，先让第一个指针和第二个指针都指向头结点，然后再让第一个指正走(k-1)步，到达第k个节点。  
 * 然后两个指针同时往后移动，当第一个结点到达末尾的时候，第二个结点所在位置就是倒数第k个节点了  
 */
public ListNode FindKthToTail(ListNode head,int k) {
	if(head==null||k<=0){
		return null;
	}
	ListNode p=head,q=head;
	for(int i=1;i<k;i++){
		if(q.next==null){
			return null;
		}else {
			q=q.next;
		}
	}
	while(q.next!=null){
		p=p.next;
		q=q.next;
	}
	return p;
}
```
