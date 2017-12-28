# sorted函数
Python内置的排序函数sorted可以对list或者iterator进行排序

```python
sorted(iterable, cmp=None, key=None, reverse=False) --> new sorted list
```

>iterable：是可迭代类型;  
cmp：用于比较的函数，比较什么由key决定,有默认值，迭代集合中的一项;  
key：用列表元素的某个属性和函数进行作为关键字，有默认值，迭代集合中的一项;  
reverse：排序规则. reverse = True 或者 reverse = False，有默认值。  
返回值：是一个经过排序的可迭代类型，与iterable一样。  

```python
a = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10)]
#根据第二个域和第三个域进行排序
sorted(students, key=operator.itemgetter(1,2))
```
