
## Среди, затваряния, имени пространства
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


---
#### Сблъсъци
Както споменахме, средите образуват дърво. Може да проследим родителите на дадена среда по следния начин.

```R
g = globalenv()
while(environmentName(g) != "R_EmptyEnv"){ g = parent.env(g); cat(str(g, give.attr=F))}
```


	<environment: package:stats>
	<environment: package:graphics>
	<environment: package:grDevices>
	<environment: package:utils> 
	<environment: package:datasets> 
	<environment: package:methods> 
	<environment: 0x00000000153d4ac8> 
	<environment: base> 
	<environment: R_EmptyEnv>



Алтернативно, може да използваме функцията `search`:

```R
search()
```


	[1] ".GlobalEnv"        "package:stats"     "package:graphics" 
	[4] "package:grDevices" "package:utils"     "package:datasets" 
	[7] "package:methods"   "Autoloads"         "package:base"



Чрез функцията **`attach`** имаме възможност да дoбавим среда, съдържаща обекти, в дървото от среди. Това прави обектите видими по име, но е възмжно да настъпят сблъсъци.


```R
a = 5
objList = list(a=6, b=7)
a
b
```

	[1] 5
	Error: object 'b' not found


```R
attach(objList)
```

	The following object is masked _by_ .GlobalEnv:
	
    	a

```R
a
b
```

5


7


```R
search()
```


	 [1] ".GlobalEnv"        "objList"           "package:stats"     "package:graphics"  "package:grDevices" "package:utils"    
	 [7] "package:datasets"  "package:methods"   "Autoloads"         "package:base"



---
#### Имени пространства

Както знаем, с функцията `library` можем да заредим пакет и да добавим неговата среда във веригата от среди. Някои пакети освен това имат допълнителна среда, която играе ролята на имено пространство. Именото пространство ще бъде също и средата за функциите от съответния пакет.


```R
loadedNamespaces()
```

	[1] "compiler"  "graphics"  "utils"     "grDevices" "stats"     "datasets"  "methods"   "base"
	

Маскиране на функции:

```R
z = list(mean=function(x){ return("This function is now ruined!") })
attach(z)
```

	The following object is masked from package:base:

	    mean
	    
	  
```R
mean(5)
```


	[1] "This function is now ruined!"
	

```R
base::mean(5)
```


5

