---
layout: post
title: "Merge Sort"
date: 2016-10-25
comments: true
categories: algorithm
---

**병합 정렬**은 리스트를 반으로 나누는 작업을 하나의 원소가 될때까지 재귀적으로
반복한 뒤, 분리된 서브 리스트를 비교하며 병합한다. 
최악의 경우에도 O(nlogn)의 복잡도를 가진다.  
병합 작업을 할 때 원소를 비교하여 새로운 배열에 넣어 정렬을 하기 때문에 추가적으로
정렬하려는 배열과 같은 크기의 배열이 필요하다.  
추가적인 배열 없이 정렬이 가능하긴 하지만 같은 O(nlogn)의 복잡도를 가진다고 해도
낮은 성능을 보여준다.


### 정렬 순서
1. 만약 리스트의 길이가 0 or 1이면 리턴한다.
2. 리스트를 반으로 나눈다.
3. 각 서브 리스트를 대상으로 하나의 원소가 될 때까지 2번 작업을 재귀적으로 진행한다.
4. 각 원소들을 정렬하면서 다시 합친다.

-----

### 정렬 과정을 보여주는 전체 이미지

<p><img src="{{ site.baseurl }}/images/merge_sort1.jpg" width="400"></p>


-----

### 서브 리스트를 합치는 이미지

<p><img src="{{ site.baseurl }}/images/merge_sort2.jpg" width="400"></p>

-----


### pseudocode
```{.python}
procedure merge_sort(list):
	if len(list) <= 1:
    	return list
    
    result <- list for sorted items
    i <- left index
    j <- right index
    center <- center index
    
    left <- recursive merge_sort func for left
    right <- recursive merge_sort func for right
    
    while i < len(left) and j < len(right):
    	add smaller one of left[i] and right[j] in the result list
        (left[i]와 right[j] 중 작은 값을 result에 넣는다)
        
	result <- add last one of left or right
    
    return result
    
```


### source code
```{.python}

def merge_sort(_list):
    if len(_list) <= 1:
        return _list

    result = []
    i = 0
    j = 0
    center = len(_list)//2

    left = merge_sort(_list[:center])
    right = merge_sort(_list[center:])

    while i < len(left) and j < len(right):
        if left[i] < right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result += left[i:]
    result += right[j:]

    return result

if __name__ == '__main__':
    list_ = [55, 7, 78, 12, 42, 21]
    print(merge_sort(list_))
```
