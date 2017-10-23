
## Среди, затваряния, именни пространства
---
#### Среди

В `R` среда е обект от първи клас, който указва кой символ към кой обект сочи. Също така тя съдържа и указател към "обграждащата" я среда (или иначе казано нейната среда-родител)


```R
a = 1234
e = globalenv()
ls()
```


<ol class=list-inline>
	<li>'a'</li>
	<li>'e'</li>
</ol>




```R
ls(e)
```


<ol class=list-inline>
	<li>'a'</li>
	<li>'e'</li>
</ol>




```R
ls(e$e$e$e$e$e$e$e)
print("ENVIRON-CEPTION!")
```


<ol class=list-inline>
	<li>'a'</li>
	<li>'e'</li>
</ol>



    [1] "ENVIRON-CEPTION!"


---
Всяка среда в `R` може да съдържа други обекти от тип среда. С други думи средите имат структура на дърво. Неговият корен е празната среда.


```R
i.have.no.parents = emptyenv()
i.have.no.parents
```


    <environment: R_EmptyEnv>



```R
my.parent.is.global = new.env()
parent.env(my.parent.is.global)
```


    <environment: R_GlobalEnv>



```R
assign("a", 4321, envir=my.parent.is.global)
a
my.parent.is.global$a
```


1234



4321



```R
b = "i'm in the global env"
ls(my.parent.is.global)
my.parent.is.global$b
get("b", envir=my.parent.is.global)
```


'a'



    NULL



'i\'m in the global env'


---
#### Затваряния

Всяка функция се състои от аргументи, тяло и среда. По подразбиране, средата на функцията е тази, която е била активна по време на нейното създване. В последствие обаче тя може да бъде сменена.


```R
f = function(x) {x + a}
a
f(1)
environment(f)
```


1234



1235



    <environment: R_GlobalEnv>



```R
environment(f) = my.parent.is.global
f(1)
```


4322



```R
eval(f(1), envir = globalenv())
ls(globalenv())

```


4322



<ol class=list-inline>
	<li>'a'</li>
	<li>'b'</li>
	<li>'counter'</li>
	<li>'createCounter'</li>
	<li>'e'</li>
	<li>'f'</li>
	<li>'i.have.no.parents'</li>
	<li>'my.parent.is.global'</li>
</ol>



С функцията `counter` долу имаме затваряне, съдържащо променливата `val` от средата на функцията `createCounter`. Обърнете внимание на оператора **`<<-`**.


```R
createCounter = function(val) {
    function(i) {
        val <<- val+i
    }
}

counter = createCounter(0)
counter(1)
counter(3)
a = counter(5)
a
```


9



```R
ls(environment(counter))
environment(counter)$val
```


'val'



9

