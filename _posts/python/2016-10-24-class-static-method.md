---
layout: post
title: "classmethod Vs. staticmethod"
date: 2016-10-24
comments: true
categories: python
---

classmethod와 staticmethod는 인스턴스객체를 통하지 않고 클래스를 통해 직접 호출할수 있는 메소드이다.  
생성된 인스턴스의 개수를 저장하거나 클래스 내부에 저장해야할 데이터가 있을 경우 효율적으로 사용된다.  

## 문법적 차이점
staticmethod는 첫번째 인자가 필요가 없다. 하지만 classmethod는 cls라는 클래스 객체를 첫번째 인자로 받아야 한다.   

## 기능적 차이점
상속을 받지 않은 클래스에서의 기능적 차이점은 없다. 하지만 상속을 받는 경우 동작이 달라진다.  
classmethod는 인자로 받는 cls에 의해 해당 클래스에 대한 범위를 가지지만,  
staticmethod는 따로 인자르르 전달하지 않기 때문에 구현된 클래스에 대해서만 범위를 가진다.  
의미가 어렵다면 아래 코드를 보고 알 수 있을 것이다.


```{.python}

class Foo(object):
    name = 'foo'

    @staticmethod
    def get_name_static():
        return Foo.name

    @classmethod
    def get_name_class(cls):
        return cls.name

class Bar(Foo):
    name = 'bar'

f = Foo()
b = Bar()

print(f.get_name_static())
print(f.get_name_class())

print(b.get_name_static())
print(b.get_name_class())


----------output-------------
foo
foo
foo
bar


```