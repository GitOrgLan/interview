###和为S的连续正数序列
##
题目描述  
>小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。  
>但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。  
>没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。  
>现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列?   

输出描述:
>输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序   

[牛课网链接](http://www.nowcoder.com/practice/c451a3fd84b64cb19485dad758a55ebe?tpId=13&tqId=11194&rp=3&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) 

##  
```
>解法一:  
public ArrayList<ArrayList<Integer>> FindContinuousSequence(int sum) {
	if (sum <= 0) {
		return new ArrayList<>();
	}
	ArrayList<ArrayList<Integer>> res = new ArrayList<>();
	for (int i = 1; i <= (sum / 2); i++) {
		ArrayList<Integer> tmp = new ArrayList<>();
		tmp.add(i);
		find(tmp, sum - i, res);
	}
	return res;
}

public void find(ArrayList<Integer> list, int sum, ArrayList<ArrayList<Integer>> res) {
	if (sum == 0) {
		if (list.size() > 1) {
			res.add(list);
		}
	}
	if (sum < 0) {
		return;
	}
	if (list.size() == 0) {
		list.add(1);
		find(list, sum - 1, res);
		return;
	} else {
		int last = list.get(list.size() - 1);
		if (last + 1 <= sum) {
			list.add(last + 1);
			find(list, sum - last - 1, res);
		}
	}
}
>解法二:
public ArrayList<ArrayList<Integer>> FindContinuousSequence2(int sum) {
	if (sum <= 0) {
		return new ArrayList<>();
	}
	ArrayList<ArrayList<Integer>> res = new ArrayList<>();
	for (int i = 1; i <= (sum / 2); i++) {
		ArrayList<Integer> tmp = new ArrayList<>();
		tmp.add(i);
		int last=i,value=i;
		//不能有等于号，要不会等于后还会继续
		while(last<sum){
			value++;
			tmp.add(value);
			last+=value;
		}
		if(last==sum){
			res.add(tmp);
		}
	}
	return res;
}
```
