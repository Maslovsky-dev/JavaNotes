---
tags: Spring
--- 
Аспектно-ориентированное программирование (АОП) — это парадигма программирования являющейся дальнейшим развитием процедурного и объектно-ориентированного программирования (ООП). Идея АОП заключается в выделении так называемой сквозной функциональности. **Отделение служебного функционала от бизнес логики**
### Выделении сквозной функциональности
  
До  
  
![image|400](https://habrastorage.org/r/w1560/webt/op/jq/pp/opjqppb5lfntanv8vnfg-pzztxa.png)  
  
и после  
  
![image|400](https://habrastorage.org/r/w1560/webt/um/nm/ee/umnmeezodtpulyveyyfe0bkp3qe.png)  
  
Т.е. есть функциональность которая затрагивает несколько модулей, но она не имеет прямого отношения к бизнес коду, и ее хорошо бы вынести в отдельное место, это и показано на рисунке выше.

### Join point
  
![image|400](https://habrastorage.org/r/w1560/webt/ki/2z/fn/ki2zfnepuexaxcngpxq0iljx5w8.png)  
  
**Join point** — следующее понятие АОП, это точки наблюдения, присоединения к коду, где планируется введение функциональности.

### Pointcut
  
![image](https://habrastorage.org/r/w1560/webt/dl/qf/sn/dlqfsnohne28cglbv9eptqpv08c.png)  
  
**Pointcut** — это срез, запрос точек присоединения, — это может быть **одна и более точек**. Правила запросов точек очень разнообразные, на рисунке выше, запрос по аннотации на методе и конкретный метод. Правила можно объединять по &&, ||,!

### Advice
  
![image|400](https://habrastorage.org/r/w1560/webt/oo/8x/u0/oo8xu02w9ymyfni10sjm6rnukr0.png)  
  
Advice — набор инструкций выполняемых на точках среза (Pointcut). Инструкции можно выполнять по событию разных типов:  
- **Before** — перед вызовом метода
- **After** — после вызова метода
- **After returning** — после возврата значения из функции
- **After throwing** — в случае exception
- **After finally** — в случае выполнения блока finally
- **Around** — можно сделать пред., пост., обработку перед вызовом метода, а также вообще обойти вызов метода.
на один Pointcut можно «повесить» несколько Advice разного типа.

### Aspect

  
![image|400](https://habrastorage.org/r/w1560/webt/wh/bo/5d/whbo5d_vyupqimqrjalrvsx0y50.png)  
  
Aspect — модуль в котором собраны описания Pointcut и Advice.  
  