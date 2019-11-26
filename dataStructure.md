**Data Structure Review**  
By John_k

---
### Map/Set/Hash
**Set**: 没有重复元素  
**Map**: <key, value> pairs，不能存在重复的key，每个key可以map到一个value  

HashMap/HashSet是利用**HashTable**实现的Set和Map，但是java不再使用HashTable。   
![HashMap Implementation](https://s2.ax1x.com/2019/11/26/Mx04qU.png)  
> 解决冲突有两种办法，java采取的是separate chaining，每个index后面指向的都是一条linked list。  
```java
// HashMap API
get(key)
put(key, value)
remove()

// HashSet API
add()
remove()
contains()
```

HashCode()和equals()方法需要注意:  
<span style="color:red">
如果重写了equals(), 就要重新写HashCode()，因为hashcode默认是通过内存地址来计算的，但如果重写的equals，也就是说要比较两个value的内容是否也一样，如果内容一样，但是地址不同，计算出来的hashcode还是不一样，这样就会导致hashtable存入重复元素。
</span>

