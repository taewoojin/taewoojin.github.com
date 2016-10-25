---
layout: post
title: "public, protected and private member"
date: 2016-10-24
comments: true
categories: python
---

### public 

파이썬에서 모든 멤버 변수와 메소드는 기본적으로 public이다. 때문에 모두 접근 가능하다.

```{.python}
class Cal(object):
    def __init__(self, value):
        self.value = value
	
    def setValue(self, value):
    	self.value = value
    
    def getValue(self):
        return self.value


cal = Cal(10)
print(cal.getValue())	# output: 10
cal.value = 2
print(cal.getValue())	# output: 2

```

-----

### protected

protected 멤버는 `클래스 내부`나 해당 클래스의 `서브클래스`에서만 접근이 가능하다.  
파이썬에서는 관습적으로 멤버 앞에 `single underscore(_)`를 붙여준다.

```{.python}
class Cal(object):
    def __init__(self, value):
        self._value = value
	
    def setValue(self, value):
    	self._value = value
    
    def getValue(self):
        return self._value


cal = Cal(10)
cal._left = 2
print(cal.getValue())	# output: 2

Cal._left = 9	# 클래스 외부에서는 접근되지 않음.

```

-----

### private

private 멤버는 클래스 외부에서 직접적으로 접근할 수 없고, 관습적으로 멤버 앞에 `double underscore(__)`를 붙여준다.  
private 멤버에 접근하기 위해서는 `instance._class__member`의 형식으로 접근하거나 get, set 메소드를 구현하여 접근해야 한다.  


```{.python}
class Cal(object):
    def __init__(self, value):
        self.__value = value
	
    def setValue(self, value):
    	self.__value = value
    
    def getValue(self):
        return self.__value


cal = Cal(10)
cal._Cal__value = 9
print(cal._Cal__value)	# output: 9
cal.setValue(8)
print(getValue())	# output: 8
cal.__value = 2		# error
```