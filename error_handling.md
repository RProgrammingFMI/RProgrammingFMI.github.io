

## Управление на грешки


```R
inputs = list(1, 2, 4, -5, 'oops', 0, 10)

for(input in inputs) {
    print(paste("log of", input, "=", log(input)))
}

```

    [1] "log of 1 = 0"
    [1] "log of 2 = 0.693147180559945"
    [1] "log of 4 = 1.38629436111989"


    Warning message in log(input):
    “NaNs produced”

    [1] "log of -5 = NaN"



    Error in log(input): non-numeric argument to mathematical function
    Traceback:


    1. print(paste("log of", input, "=", log(input)))

    2. paste("log of", input, "=", log(input))


**Функцията `try`** ще прескочи извикванията, водещи до грешка и ще продължи цикъла


```R
for(input in inputs) {
    try(print(paste("log of", input, "=", log(input))))
}
```

    [1] "log of 1 = 0"
    [1] "log of 2 = 0.693147180559945"
    [1] "log of 4 = 1.38629436111989"


    Warning message in log(input):
    “NaNs produced”

    [1] "log of -5 = NaN"
    [1] "log of 0 = -Inf"
    [1] "log of 10 = 2.30258509299405"


**Функцията `stop`** спира изпълнението на кода и печата съобщение за грешка, а **`warning`** само изкарва предупредително съобщение


```R
circleArea = function(radius) {
    if (radius < 0) {
        stop("The radius of a circle is cannot be a negative number!")
    } else if (radius == 0) {
        warning("Zero radius!")
        0
    } else {
        pi*radius*radius
    }
}
```


```R
circleArea(-5)
```


    Error in circleArea(-5): The radius of a circle is cannot be a negative number!
    Traceback:


    1. circleArea(-5)

    2. stop("The radius of a circle is cannot be a negative number!")   # at line 3 of file <text>



```R
circleArea(0)
```

    Warning message in circleArea(0):
    “Zero radius!”


0



```R
circleArea(5)
```


78.5398163397448


**Функцията `tryCatch`** може да се използва за по-пълноценен error handling 


```R
tryCatchResult = function(n) {
    tryCatch({
        initial = "this is unchanged"
        circleArea(n)
    }, warning = function(w) {
        print(paste("WARNING ", w))
        initial = "warning"
        except = "exception"
        return("warning")
    }, error = function(e) {
        print(paste("ERROR ", e))
        initial = "error"
        except = "exception"
        return("error")
    }, finally = {
        print("This is what we FINALLY get...")
        print(paste("initial: ", initial))
        tryCatch({
            msg = "This will cause an error because 'except' was not defined in the try-catch block"
            print(except)
        }, warning = function(w) {
            print("Warning-finally")
            print(msg)
        }, error = function (e) {
            print("Error-finally")
            print(msg)
        }
        )
    }
    )
}

tryCatchResult(5)
```

    [1] "This is what we FINALLY get..."
    [1] "initial:  this is unchanged"
    [1] "Error-finally"
    [1] "This will cause an error because 'except' was not defined in the try-catch block"



78.5398163397448



```R
tryCatchResult(0)
```

    [1] "WARNING  simpleWarning in circleArea(n): Zero radius!\n"
    [1] "This is what we FINALLY get..."
    [1] "initial:  this is unchanged"
    [1] "Error-finally"
    [1] "This will cause an error because 'except' was not defined in the try-catch block"



'warning'



```R
tryCatchResult(-5)
```

    [1] "ERROR  Error in circleArea(n): The radius of a circle is cannot be a negative number!\n"
    [1] "This is what we FINALLY get..."
    [1] "initial:  this is unchanged"
    [1] "Error-finally"
    [1] "This will cause an error because 'except' was not defined in the try-catch block"



'error'

