---
tags: Java/ОсновыЯзыка
--- 
## If / else
Условие if / else
```java
int num1 = 6;  
int num2 = 4;  
if(num1>num2){  
    System.out.println("Первое число больше второго");  
}  
else{  
    System.out.println("Первое число меньше второго");  
}
```

Условие If / else if / else
``` java
int num1 = 6;  
int num2 = 8;  
if(num1>num2){  
    System.out.println("Первое число больше второго");  
}  
else if(num1<num2){  
    System.out.println("Первое число меньше второго");  
}  
else{  
    System.out.println("Числа равны");  
}
```
Можно написать неограниченное кол-во else if операторов.

**Switch**
```java
public class Main {  
    public static void main(String[] args) {  
        int num = 8;  
        switch(num){  
  
            case 1:  
                System.out.println("число равно 1");  
                break;  
            case 8:  
                System.out.println("число равно 8");  
                num++;  
                break;  
            case 9:  
                System.out.println("число равно 9");  
                break;  
            default:  
                System.out.println("число не равно 1, 8, 9");  
        }  
    }  
}
```
`case n:` Где n - целое число 

Можно использовать простые типы **byte, short, char, int**, String? 

## Тернарная операция
Тернарную операция имеет следующий синтаксис: (первый операнд - условие) ? (второй операнд) : (третий операнд). Таким образом, в этой операции участвуют сразу три операнда. В зависимости от условия тернарная операция возвращает второй или третий операнд: если условие равно true, то возвращается второй операнд; если условие равно false, то третий. Например:
``` java
int x=3;
int y=2;
int z = x<y? (x+y) : (x-y);
System.out.println(z);
```
Здесь результатом тернарной операции является переменная z. Сначала проверяется условие x<y. И если оно соблюдается, то z будет равно второму операнду - (x+y), иначе z будет равно третьему операнду.

**Пример**: переопределение метода **hashcode**
[Перегрузка методов equals() и hashCode() в Java](https://javarush.com/groups/posts/1340-peregruzka-metodov-equals-i-hashcode-v-java)
