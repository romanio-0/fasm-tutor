## Unicode

Для работы с юникодом подключаем **encoding\win1250.inc** и используем **du / ru** для определения текста место **db / rb**

## Структура программы

Начинается все с **format**

Форматы для windows:

- PE
- PE console
- PE GUI 4.0

И для этого всего есть приписка **64** после **PE**

Секции указываются так:

```asm
section '.название' параметры_секции
```

Вот список большинства параметров:

- **code** - указывается для секции с кодом
- **data** - указывается для секции с данными
- **readable** - секция доступна для чтения
- **writeable** - секция доступна для записи
- **executable** - секция доступна для выполнения
- **shareable** - указывается что секция общая (хз что это значит)
- **discardable** - (хз что это, юзается для релокации)
- **notpageable** - (хз что это)
- **export** - указывает что это таблица экспорта
- **import** - указывает что это таблица
- **resource** - указывает что это секция с ресурсами, после нее можно указать **from 'файл.res'**
- **fixups** - указывает что секция заполняется автоматически

```asm
section ’.text’ code readable executable
section ’.reloc’ data readable discardable fixups
section ’.rsrc’ data readable resource from ’my.res’
section '.data' data readable writeable
section '.idata' import readable writeable
; и прочее
```

### Импорт

```asm
section '.idata' import readable 
    library kernel32, 'kernel32.dll',\
            msvcrt, 'msvcrt.dll',\
            user32, 'user32.dll'

    import kernel32,\
            ExitProcess, 'ExitProcess',\
            LoadLibrary, 'LoadLibraryA',\
            GetProcAddress, 'GetProcAddress'

    import user32,\
            msbox, 'MessageBoxA'

    import msvcrt,\
            printf, 'printf'
```

### Экспорт

```asm
section '.edata' export readable
    export 'имя_файла.dll',\
        имя_функции, 'имя-функции_на_выходе'
```

## **invoke / stdcall / proc** - процедуры

Для простого вызова функции можно юзать 

```asm
invoke MessageBox,0,szText,szCaption,0
; или для 32 бит
stdcall [MessageBox],0,szText,szCaption,0
; или для 64 бит (раздницы нет)
fastcall [MessageBox],0,szText,szCaption,0
```

Для вызова функций из Си/Си++ и прочих в 32 битных прогах лучше использовать **cinvoke / ccall** тк обычные не отчищают за собой стек

```asm
cinvoke MessageBox,0,szText,szCaption,0
; или
ccall [MessageBox],0,szText,szCaption,0
```

### Создание процедуры x32

Для создания процедуры используется ключевое слово **proc** и **endp** (**ret** также важен)

Для указания параметров мы пишем их через запятую

```asm
proc foo val1, val2
    ;...
endp
```

Мы можем через двоеточие указывать размер параметра, но важно **капслоком**

По умолчанию для переменных используется базовый размер стека (x32 == dword / x64 == qword) то есть эта запись эквивалентна

```asm
proc foo val1:DWORD, val2:DWORD
    ;...
endp
```

Мы можем создать локальную переменную, которая будет доступна только в функции используя ключевое слово **local**

А для создания сразу нескольких можно создать блок данных используя **locals** и **endl**

```asm
proc foo val1:BYTE, val2:WORD
    local per1 dd ?
    locals
        per2 db ?
        per3 dw 12
        per4 rb 5
    endl
    ; ...
endp
```

Так же есть запись по интереснее, но только для локальных переменных

```asm
proc foo val1:BYTE, val2:WORD
    local per1:DWORD, per5:BYTE
    locals
        per2:BYTE
        per3:WORD
        per4[5]:BYTE
    endl
    ; ...
endp
```

Можно указать после названия процедуры какое соглашение будет использоваться при создании процедуры **stdcall** или **c**, от этого будет зависит какой стандарт используется и будет ли отчищать стек вызывающий код (**c**) или сама процедура (**stdcall**). По умолчанию используется **stdcall**

Есть возможность сохранить значение регистров чтобы они не изменились в процедуре используя после названия процедуры и соглашения слово **uses** и перечислив регистры через пробел. После последнего регистра если есть параметры должна стоять запятая

```asm
proc foo stdcall uses ebx esi, val1, val2
    ;...
endp
```

### Создание процедуры x64

Все довольно схоже, но из-за того что для x64 в винде есть соглашение, есть ряд особенностей:

- **stdcall / c** - указывать не имеет смысла
- При передаче дробного числа надо перед ним указать **float / float dword**
- Тк первые параметры передаются через регистры **rcx, rdx, r8, r9** хоть для них при создании процедуры выделяется место, в эти переменные данные из регистров не переносятся

```asm
proc foo  a, b,c,d,e,f
    mov [a], rcx
    mov [b], rdx
    mov [c], r8
    mov [d], r9
    ; остальные заполнены
    ;...
    ret
endp
```

Есть возможность сразу для всех выделить пространство для стека используя **frame** и **endf**.это для того чтобы пространство в стеке выделилось один раз сразу для двух

```asm
frame 
    invoke ...
    invoke ...
endf
```

Есть возможность изменить ход работы **proc / ret / endp**, но тут уж книжку читани ***[тык](../book/fasm.pdf)***

## **if / else / while / repeat** - ветвления и циклы

Если подключить заголовок **win32ax.inc / win64ax.inc** можно использовать **if / else / while / repeat**

- **=** равно
- **>** больше
- **<** меньше
- **>=** больше или равно
- **<=** меньше или равно
- **<>** не равно

####

- **.if**
- **.elseif**
- **.else**
- **.endif**

```asm
    mov eax, 5
    
    .if eax = 1
        mov eax, 2
    .elseif eax = 2
        mov eax, 3
    .else
        mov eax, 123
    .endif

    invoke ExitProcess, eax ; eax == 123
```

- **.while**
- **.endw**
- **.repeat**
- **.until** 

```asm
    mov eax, 5
    mov ebx, 10

    .while ebx > 0
        add eax, 2
        dec ebx
    .endw

    invoke ExitProcess, eax ; eax == 25
```

**.repeat / .until** - подобие **do..while** только цикл продолжается пока условие **НЕ** верно

```asm
    mov eax, 5
    mov ebx, 20

    .repeat
        add eax, 5
        dec ebx
    .until ebx < 10

    invoke ExitProcess, eax ; eax == 60
```

## Макросы

Макросы тут это самодельная инструкция которая просто подставляет код

```asm
macro lol {
    mov eax, 1
    add eax, edx
}
main:
    mov edx, 5
    lol ; по факту просто подставилось
    ; mov eax, 1
    ; add eax, edx
    
    invoke ExitProcess, eax ; eax == 6
```

После названия переменной можно написать параметры через запятую

```asm
macro lol par1, par2{
    mov eax, par1
    add eax, par2
}
main:
    mov edx, 5
    lol 1, edx ; по факту просто подставилось
    ; mov eax, 1
    ; add eax, edx
    
    invoke ExitProcess, eax ; eax == 6
```

Есть ключевые слова доступные только внутри макросов:

- **local** - позволяет указать через запятую имена (меток/переменных/прочего) которые будут доступны только внутри макроса, написав имя с точки имя для нее будет каждый раз генерироваться
- **common** - то что выполняется после этой команды будет выполнятся один раз
- **reverse** - то что выполняется после этой команды будет выполнятся наоборот (снизу вверх)
- **forward** - то что выполняется после этой команды будет выполняться столько раз, сколько переменных параметров передалось

Для написания макроса с переменным кол-вом параметров, последний параметр должен быть в **[ ]**

```asm
.data
    f db '%d - %d',10,0
    val1 dd 12
    val2 dd 22
    
.code

macro lol foo, [arg]{
  common
    local move
    jmp move    
move:
    
  forward  
    add ebx, 1 ; выполниться столько раз, сколько переменных параметров
    
  reverse
    push arg ; тк параметры в стек передатся от последнего к первому, нам надо это инвертировать
    
  common
    call [foo] ; выполнитсья один раз
}

main:
    mov ebx, 0
    lol printf, f, [val1], [val2]

    invoke ExitProcess, ebx ; ebx == 3
```

Если написать перед переменной **#**, то что будет передано параметром подставится туда

```asm
macro jif op1,cond,op2,label
{
    cmp op1,op2
    j#cond label
}

main:
    mov eax, 1
    mov ebx, 1
    jif eax, z, ebx, exit
    ; заменяется на 
    ; cmp eax, ebx
    ; jz exit
    
exit:
    ;...    
```

## Структуры

Для объявления структуры используется ключевое слово **struct** после чего имя структуры, а в конце должен быть указан конец структуры **ends**

Можно узнать размер структуры или ее элементов указав перед ними **sizeof.**

Важно чтобы структура была объявлена выше переменной

```asm
struct lol
    val1 db ?
    val2 db 4
ends
    
    eee lol ; после структуры

main:
    mov eee.val1, 123
    mov eax, eee.val1 ; eax == 123
    mov edx, eee.val2 ; edx == 4
    mov ebx, sizeof.lol ; ebx == 8
    mov ecx, sizeof.lol.val1 ; ecx == 4
```

Есть возможность заполнить структуру, для этого через запятую указываем данные, для того чтобы указать конкретно для какого-то параметра можно указать их в **< >**


```asm
struct lol
    val1 db ?
    val2 db ?
ends
    
struct lox
    val1 lol
    val2 db 255 dep (?)
ends    
    
    eee lol 1, 2 ; после структуры
    aaa lox <1,2>, <'darova',0>
main:
    mov eax, eee.val2 ; eax == 2
    mov ebx, aaa.val1.val2 ;ebx == 2
```


И есть еще вариант как можно объявить структуру, тут структуры это тупо макросы которые используются для удобного инициализирования

```asm
.data
    my lol
    me lox 123, 21
    str db 'lox',10,0
    
struc lol{
    .l dd ?
    .e dd ?
}

struc lox op1, op2{
    .a dd op1
    .b db op2
}

struc db [data] {
  common
    . db data
    .size = $ - .
}

.code
    mov eax, my.l ; eax == 0
    mov edx, my.e ; edx == 0
    mov ebx, me.a ; ebx == 123
    mov ecx, me.b ; ecx == 21
    mov edi, str.size ; edi == 5 
```



## ...Прочее

Там еще макросы могут вести себя по разному зависимо от того что ты передашь туда и прочее
Вообще дахера всего для макросов, это язык макросов)) остальное ищи тут или в инете

***[fasmBook](../book/fasm.pdf)***

