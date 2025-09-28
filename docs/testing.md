## Чем pytest отличается от unittest?
tbd

---

## Что такое параметризованные тесты (@pytest.mark.parametrize)?
tbd

---

## Что такое фикстуры? Как их создать?
tbd

---

## Чем mock отличается от stub?
tbd

---

## Monkey patching
Monkey patching — это техника изменения поведения кода во время выполнения путем динамической замены 
или добавления методов, или атрибутов в существующем объекте. 
Эта техника может быть полезна в том случае, когда изменения не могут быть внесены в существующий код, 
и требует минимальных изменений в существующем коде.

Например, можно добавить новый метод в класс в runtime, который наследуется от базового класса:

    class MyBaseClass:
        def my_method(self):
            print('Hello from MyBaseClass')
    
    def monkey_patch():
        def new_method(self):
            print('Hello from new_method')
        MyBaseClass.my_method = new_method
    
    monkey_patch()
    obj = MyBaseClass()
    obj.my_method()  # выведет "Hello from new_method"

В этом примере мы добавляем новый метод `new_method()` в класс `MyBaseClass`, используя функцию `monkey_patch()`. 
После этого, вызов метода `obj.my_method()` выведет строку

    Hello from new_method

Важно учитывать, что использование monkey patching может усложнить отладку и поддержку в будущем, 
поэтому следует использовать эту технику с осторожностью и только при необходимости.
