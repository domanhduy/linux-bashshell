# Chapter 11: true, false and : commands


### Mục lục
[Section 11.1: Infinite Loop](#section111)<br>
[Section 11.2: Function Return](#section111)<br>
[Section 11.3: Code that will always/never be executed](#section113)<br>

<a name="section111"></a>
### Section 11.1: Infinite Loop

```
while true; do
    echo ok
done
```

or

```
while :; do
    echo ok
done
```

or

```
until false; do
    echo ok
done
```

<a name="section112"></a>
### Section 11.2: Function Return

```
function positive() {
    return 0
}


function negative() {
    return 1
}
```

<a name="section113"></a>
### Section 11.3: Code that will always/never be executed

```
if true; then
    echo Always executed
fi

if false; then
    echo Never executed
fi
```










