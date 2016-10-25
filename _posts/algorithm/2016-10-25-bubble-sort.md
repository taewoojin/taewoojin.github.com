---
layout: post
title: "Bubble Sort"
date: 2016-10-25
comments: true
categories: algorithm
---

**거품 정렬**은 정렬이 필요한 자료 리스트에서 인접한 요소를 비교, 교환을 반복해서 최대값을 가장 뒷부분 위치하게 하는 정렬 방법이다.  
많은 비교와 교환이 일어나기 때문에 정렬 알고리즘 중에서 가장 느리고
성능은 최악이지만, 대신에 로직은 매우 쉽다.  
거품 정렬은 이중 루프로 구성 되어 있으므로 **O(N^2)** 성능을 가진다.

-----
[출처: 위키](https://ko.wikipedia.org/wiki/%EA%B1%B0%ED%92%88_%EC%A0%95%EB%A0%AC)

**55** **07** 78 12 42  
07 **55** **78** 12 42  첫 번째 패스  
07 55 **78** **12** 42  
07 55 12 **78** **42**  

**07** **55** 12 42 78  두 번째 패스  
07 **55** **12** 42 78  
07 12 **55** **42** 78  

**07** **12** 42 55 78  세 번째 패스  

07 **12** **42** 55 78  네 번째 패스  

**07** **12** 42 55 78  다섯번째 패스  

**07** **12** **42** **55** **78**  정렬 끝

-----


### pseudocode
```{.python}

procedure bubbleSort(list):
	for each i in 1 to len(list):
    	for each j in 0 to len(list)-i:
      		if list[j] > list[j+1]:
        		swap(list[j], list[j+1])

for all 

```


### source code (python)
```{.python}

def bubble_sort(_list):
    for i in range(1, len(_list)):
        for j in range(len(_list)-i):
            if _list[j] > _list[j+1]:
                _list[j], _list[j+1] = _list[j+1], _list[j]
		print('{0}단계: {1}'.format(i+1, _list))
    return _list

if __name__ == '__main__':
    lists = [55, 7, 78, 12, 42, 21]
    print(bubble_sort(lists))


----------output-----------
1단계: [7, 55, 12, 42, 21, 78]
2단계: [7, 12, 42, 21, 55, 78]
3단계: [7, 12, 21, 42, 55, 78]
4단계: [7, 12, 21, 42, 55, 78]
5단계: [7, 12, 21, 42, 55, 78]
```
