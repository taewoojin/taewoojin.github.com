---
layout: post
title: "Selection Sort"
date: 2016-10-25
comments: true
categories: algorithm
---

**선택정렬**은 가장 앞쪽의 값과 나머지 값중에 최소값을 비교하여 교환하는 정렬 방식이다.  
이중 루프가 사용되기 때문에 **O(N^2)**의 성능을 가진다.



### pseudocode
```{.python}

procedure selection_sort(list):
	for each i in 0 to len(list)-1:
    	index <- i+1
        for each j in index+1 to len(list):
        	if list[index] > list[j]:
            	index <- j
        if list[i] > list[index]:
        	swap(list[i], list[index])
            
```


### source code
```{.python}

def selection_sort(_list):
    for i in range(len(_list)-1):
        index = i+1
        for j in range(index+1, len(_list)):
            if _list[index] > _list[j]:
                index = j
        if _list[i] > _list[index]:
            _list[index], _list[i] = _list[i], _list[index]
        print('{0}단계: {1}'.format(i+1, _list))
    return _list

if __name__ == '__main__':
    _lists = [55, 7, 78, 12, 42, 21]
    print(selection_sort(_lists))


----------output-----------
1단계: [7, 55, 78, 12, 42, 21]
2단계: [7, 12, 78, 55, 42, 21]
3단계: [7, 12, 21, 55, 42, 78]
4단계: [7, 12, 21, 42, 55, 78]
5단계: [7, 12, 21, 42, 55, 78]
```