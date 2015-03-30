#lec9 虚存置换算法spoc练习

## 个人思考题
1. 置换算法的功能？

2. 全局和局部置换算法的不同？

3. 最优算法、先进先出算法和LRU算法的思路？

4. 时钟置换算法的思路？

5. LFU算法的思路？

6. 什么是Belady现象？

7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？

8. 什么是工作集？

9. 什么是常驻集？

10. 工作集算法的思路？

11. 缺页率算法的思路？

12. 什么是虚拟内存管理的抖动现象？

13. 操作系统负载控制的最佳状态是什么状态？

## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象

```
设S为物理页面数量为n的LRU算法维护的栈，S1是物理页面数量为n+k的LRU算法维护的栈
证明在任意时刻，S属于S1，且任意S中元素a，对应到S1中元素a1，满足a的位置小于等于a1的栈位置,即可证明物理页面数量增加的缺页率不会降低。
 
数学归纳法：
在初始情况下，S与S1都为空，满足任意S中元素a，对应到S1中元素a1
在t-1时刻满足S属于S1，且任意S中元素a，对应到S1中元素a1，满足a的位置小于等于a1的栈位置
在T时刻，对于对x页的页面访问请求，可能出现三种情况
情况1：x属于S，且x属于S1,则经过这一步，需要将S与S1中的x页都置于栈顶部，因为S1的栈大小大于S，所以对于x元素，还是满足x在S中的位置，小于x在S1中的位置。对于原有在S与S1中的元素，在x元素前的元素位置不变，在x后的元素位置整体前移，大小关系保持不变。所以这种情况下依旧满足条件。
情况2：x不属于S，且x属于S1，x不属于S，就在栈顶压入元素，x位置为n；在s1中找到x，将x元素移至栈定于，依旧满足S中x位置小于等于S1中位置。对于原有在S中的元素，整体前移了一位，S1中元素x前的不变，x后的整体前移1位，所以整体大小关系依旧满足。
情况3：x不属于S，且x不属于S1，则都在栈尾部加入x，S中位置为n，S1中位置为n+k。同时对于被弹出栈的元素，如果弹出元素相同，则依旧满足。如果弹出元素不同，因为S中的对应元素位置小于等于S1的，所以S1中弹出的元素必然已经不属于S了。所以弹出后依旧满足S属于S1.即在这种情况下依旧满足假设。
由于假设的存在，s属于S1，即不会出现x属于s，x不属于s1的情况。
 
综上所述，由数学归纳法得，对任意时刻，任意时刻，S属于S1，且任意S中元素a，对应到S1中元素a1，满足a的位置小于等于a1的栈位置。
即对任意时刻，对S1的缺页数量不会大于S。即物理页数量增加，缺页率不会上升。
 
即证明了，LRU算法，不会出现belady现象。
```

(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)

```
 const int T = 3;
 int tlast = -1, cur = -1;
 map<Page *, int> num, timing;
 set<int> unused;
 int findPage(Page *page){
 	++cur;
 	if (num.find(page) != num.end())
 		return num[page];
 	int allo = unused.upper_bound(-1);
 	num[page] = allo;
 	timing[page] = cur;
 	unused.erase(allo);
 	if (cur - tlast > T){
 		for (auto it = num.begin(); it != num.end(); ++it)
 			if (timing[it->first] < tlast){
 				timing.erase(timing.find(it->first));
 				num.erase(it);
 			}
 	}
 	tlast = cur;
 	return allo;
 }
```
 
## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)
