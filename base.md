## Формат чисел

- *...* - десятичное число
- *0x...* - шестнадцатеричное число
- *...b* - двоичное число

## nop - ничего)))

**nop** - инструкция которая ничего не выполняет

## **mov** *out*, *in* - перемещение

Копирует указанные данные в указанное место

```asm
mov eax, 23
```

```asm
mov eax, 23
mov ebx, eax ;ebx == 23
```

При перемещении между регистрами важно чтобы они были одного размера

```asm
mov al, 23
mov ebx, al ;error
```

Но если нужно копировать данные из меньшего регистра в больший можно использовать **movsx** и  **movzx**

*Вторым параметром обязательно должен быть регистр*

**movsxd / movzxd** - есть первым параметром передается регистр 64 бита, а вторым 32

**movsx** -  расширяет данные так, чтобы сохранить знак

**movzx** - расширяет данные нулями, то есть убирает знак

## **add / sub** *op1*, *op2* - сложение / вычитание

Команды которые складывают / вычитают и помещают результат в первый операнд

```asm
mov ebx, 23
add ebx, 50 ; ebx == 73
```
```asm
mov ebx, 200
sub ebx, 101 ;ebx == 99
```

## **inc / dec** *param* - инктремент / декремент

```asm
mov ebx, 10
inc ebx ; ebx == 11
dec ebx ; ebx == 10
```

## **jmp** *метка* - переход к инструкции

Эта инструкция меняет выполнение программы на переданную метку

```asm
    mov eax, 10
    jmp lol
    mov eax, 22 ; не выполняется
lol:
    ;....   ; eax == 10
```

Также не обязательно передавать напрямую метку

```asm
.data
    huy dd lol ;размер зависит от разрядности 

.code
    mov eax, 10
    jmp [huy]   ; передача метки через переменную
    mov eax, 22 ; не выполняется
lol:
    ;....   ; eax == 10
```

```asm
    mov edi, lol
    mov eax, 10
    jmp edi     ; передача метки через регистр
    mov eax, 22 ; не выполняется
lol:
    ;....   ; eax == 10
```

## Преход к инструкции по условию

При различных операциях устанавливаются флаги, которые дают понять что произошло при выполнении оперции

### Флаги:

    0     | CF   | Флаг переноса (Carry flag):казывает, был ли при сложении перенос или заимствование при вычитании. Используется в качестве входных данных для инструкций сложения и вычитания.
    2     | PF   | Флаг четности: устанавливается, если младшие 8 битов результата содержат четное число единиц.
    4     | AF   | Флаг настройки: указывает, был ли при сложении перенос или заимствование при вычитании младших 4 битов.
    6     | ZF   | Флаг нуля (Zero flag): устанавливается, если результат операции равен нулю
    7     | SF   | Флаг знака (Sign flag): устанавливается, если результат операции отрицательный.
    8     | TF   | Флаг прерывания выполнения (Trap flag): используется при одношаговой отладке.
    9     | IF   | Флаг разрешения прерывания: установка этого бита разрешает аппаратные прерывания.
    10    | DF   | Флаг направления: контролирует направление обработки. Если не установлен, то порядок от самого младшего до самого старшего адреса. Если установлен, то порядок обратный - от самого старшего до самого младшего адреса.
    11    | OF   | Флаг переполнения (Overflow flag): если устанавлен, то операция привела к переполнению со знаком.
    12-13 | IOPL | Уровень привилегий ввода-вывода (I/O privilege level): уровень привилегий текущего выполняемого потока. IOPL 0 — это режим ядра, а 3 — пользовательский режим.
    14    | NT   | Флаг вложенной задачи (Nested task flag): управляет цепочкой прерываний.
    16    | RF   | Флаг возобновления (Resume flag): используется для обработки исключений во время отладки.
    17    | VM   | Флаг режима виртуальной машины 8086: если установлен, режим совместимости с 8086 активен. Этот режим позволяет запускать некоторые приложения MS-DOS в контексте операционной системы в защищенном режиме.
    18    | AC   | Флаг проверки выравнивания (Alignment check flag): если установлен, проверка выравнивания памяти активна. Например, если установлен флаг AC, сохранение 16-битного значения по нечетному адресу вызывает исключение проверки выравнивания. Процессоры x86 могут выполнять невыровненный доступ к памяти, когда этот флаг не установлен, но количество требуемых командных циклов может увеличиться.
    19    | VIF  | Флаг виртуального прерывания (Virtual interrupt flag): виртуальная версия флага IF в виртуальном режиме 8086..
    20    | VIP  | Флаг ожидания виртуального прерывания: Устанавливается, когда прерывание находится в состоянии ожидания в виртуальном режиме 8086.
    21    | ID   | Флаг ID: если этот бит установлен, то поддерживается инструкция cpuid. Эта инструкция возвращает идентификатор процессора и информацию о его функциях.

####

При писывается **j** в начало и получаетсякоманда

    o   | OF = 1 
    ---------------------------
    no  | OF = 0 
    ---------------------------
    c   | 
    b   | CF = 1 
    nae | 
    ---------------------------
    nc  | 
    ae  | CF = 0 
    nb  | 
    ---------------------------
    e   | ZF = 1 
    z   | 
    ---------------------------
    ne  | ZF = 0 
    nz  | 
    ---------------------------
    be  | CF or ZF = 1 
    na  | 
    ---------------------------
    a   | CF or ZF = 0 
    nbe | 
    ---------------------------
    s   | SF = 1 
    ---------------------------
    ns  | SF = 0 
    ---------------------------
    p   | PF = 1 
    pe  | 
    ---------------------------
    np  | PF = 0 
    po  | 
    ---------------------------
    l   | SF xor OF = 1 
    nge | 
    ---------------------------
    ge  | SF xor OF = 0 
    nl  | 
    ---------------------------
    le  | (SF xor OF) or ZF = 1 
    ng  | 
    ---------------------------
    g   | (SF xor OF) or ZF = 0 
    nle | 

####

    Установить флаг CF: stc 
    Сбросить флаг CF: clc 
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг ZF: stz 
    Сбросить флаг ZF: clz 
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг SF: sts 
    Сбросить флаг SF: cls 
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг OF: stov 
    Сбросить флаг OF: clov 
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг DF: std Устанавливает флаг DF в 1, что делает инструкции REP (repeat) выполняться в обратном направлении.
    Сбросить флаг DF: cld Сбрасывает флаг DF в 0, чтобы инструкции REP выполнялись в прямом направлении.
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг IF: sti Разрешает прерывания процессора.
    Сбросить флаг IF: cli Запрещает прерывания процессора.
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Установить флаг TF: sti Включает режим отладки, что позволяет выполнять инструкции пошагово.
    Сбросить флаг TF: cli Выключает режим отладки.
    -------------------------------------------------------------------------------------------------------------------------------------------------
    Флаг PF устанавливается и сбрасывается автоматически при выполнении арифметических и логических операций. Нет явных инструкций для управления PF.
    Флаг AF обычно используется при операциях с BCD (Binary Coded Decimal) числами. Нет явных инструкций для управления AF.    

####

    lahf: копирует флаги состояния из регистра eflags в регистр ah
    sahf: сохраняет флаги состояния из регистра ah в регистр eflags

    Инструкции lahf и sahf применяют следующий порядок битов для флагов, начиная с младшего бита:
        0 - Флаг переноса (CF)
        1 - Всегда равен 1
        2 - Флаг паритетности (PF)
        3 - Всегда равен 0
        4 - Дополнительный флаг переноса (AF)
        5 - Всегда равен 0
        6 - Флаг нуля (ZF)
        7 - Флаг знака (SF)
    Биты 1, 3, и 5 не используются.

```asm
    mov al, 255
    add al, 3    ;переполнение регистра
    jc skip    
    mov rax, 2   ;скипается потому что регистр переполняется
    jmp exit     ;
skip:          
    mov rax, 4      
exit:                  
    ret 
```

```asm
    mov rcx, 5
    mov rdx, 5
    sub rdx, rcx    ;rdx == 0
    jz zero         ;если флаг нуля установлен, переход к метке zero
    mov rdi, 2      ;скипается 
    jmp exit
zero:              
    mov rdi, 4       
exit:              
    mov rax, 1
    ret
```

## **cmp** *op1*, *op2* - сравнение

В качестве операндов могут участвовать регистры, переменные, непосредственные операнды. Если сравнивается непосредственный операнд, то он указывается вторым. **При этом оба операнда должны представлять целые числа, числа с плавающей точкой НЕ сравниваются.**   

Как иинструкция ***SUB*** она также вычитает второй операнд из первого операнда и устанавливает флаги кода условия на основе результата вычитания. Отличие состоит в том, что инструкция cmp не сохраняет результат вычитания, поскольку цель инструкции cmp состоит лишь в том, чтобы установить флаги кода условия на основе результата вычитания. То есть фактически посредством инструкции cmp мы сравниваем первый операнд со вторым.

    JE,JZ   | Переход если равно 	        | op1 = op2 | ZF = 1             | Для всех чисел
    JNE,JNZ | Переход если не равно         | op1 ≠ op2 | ZF = 0             | 
    ----------------------------------------------------------------------------------------------
    JL/JNGE | Переход если меньше 	        | op1 < op2 | SF ≠ OF            | Для чисел со знаком
    JLE/JNG | Переход если меньше или равно | op1 ≤ op2 | SF ≠ OF или ZF = 1 |
    JG/JNLE | Переход если больше 	        | op1 > op2 | SF = OF и ZF = 0   |
    JGE/JNL | Переход если больше или равно | op1 ≥ op2 | SF = OF            |
    ----------------------------------------------------------------------------------------------
    JB/JNAE | Переход если ниже 	        | op1 < op2 | CF = 1 	         | Для чисел без знака
    JBE/JNA | Переход если ниже или равно 	| op1 ≤ op2 | CF = 1 или ZF = 1  |
    JA/JNBE | Переход если выше 	        | op1 > op2 | CF = 0 и ZF = 0    |
    JAE/JNB | Переход если выше или равно 	| op1 ≥ op2 | CF = 0             |
    
Пример цикла do..while:

```asm
.data
    f db '%d',10,0
    msg0 db 'nachalo',10,0

.code
start:
    push msg0
    call [printf]
    mov ebx, 0
lol:
    push ebx
    push f
    call [printf]
    inc ebx
    cmp ebx, 10
    jl lol
    invoke ExitProcess, 0
```

## Условное копирование

- **cmovc / cmovb / cmovnae:** копирует значение, если флаг переноса CF = 1
- **cmovnc / cmovnb / cmovae:** копирует значение, если флаг переноса CF = 0
- **cmovz / cmove:** копирует значение, если флаг нуля ZF = 1
- **cmovnz / cmovne:** копирует значение, если флаг нуля ZF = 0
- **cmovs:** копирует значение, если флаг знака SF = 1
- **cmovns:** копирует значение, если флаг знака SF = 0
- **cmovo:** копирует значение, если флаг переполнения OF = 1
- **cmovno:** копирует значение, если флаг переполнения OF = 0

*Инструкции для сравнения чисел со знаком:*

- **cmovg:** копирует значение, если первый операнд больше второго (SF=OF или ZF=0)
- **cmovnle:** копирует значение, если первый операнд не меньше и не равен второму (SF=OF или ZF=0)
- **cmovge:** копирует значение, если первый операнд больше или равен второму (SF=OF)
- **cmovnl:** копирует значение, если первый операнд не меньше второго (SF=OF)
- **cmovl:** копирует значение, если первый операнд меньше второго (SF != OF)
- **cmovnge:** копирует значение, если первый операнд не больше и не равен второму (SF != OF)
- **cmovle:** копирует значение, если первый операнд меньше или равен второму (SF != OF или ZF=1)
- **cmovng:** копирует значение, если первый операнд не больше второго (SF != OF или ZF=1)

*Инструкции для сравнения беззнаковых чисел:*

- **cmova:** копирует значение, если первый операнд больше второго (CF=0, ZF=0)
- **cmovnbe:** копирует значение, если первый операнд не меньше и не равен второму (CF=0, ZF=0)
- **cmovae / cmovnc / cmovnb:** копирует значение, если первый операнд больше или равен второму (CF=0)
- **cmovnb / cmovnc / cmovae:** копирует значение, если первый операнд не меньше второго (CF=0)
- **cmovb / cmovc / cmovnae:** копирует значение, если первый операнд меньше второго (CF=1)
- **cmovnae / cmovc / cmovb:** копирует значение, если первый операнд не больше и не равен второму (CF=1)
- **cmovbe:** копирует значение, если первый операнд меньше или равен второму (CF=1 или ZF=1)
- **cmovna:** копирует значение, если первый операнд не больше второго (CF=1 или ZF=1)


*И две общие инструкции как для чисел со знаком, так и для беззнаковых чисел:*

- **cmove:** копирует значение, если первый операнд равен второму (ZF=1). Аналогичен инструкции cmovz
- **cmovne:** копирует значение, если первый операнд не равен второму (ZF=0). Аналогичен инструкции cmovnz

Первый параметр этих инструкций (куда копируем) представляет либо регистр, либо переменную (16, 32 или 64-битные). Второй параметр (что копируем) - регистр общего назначения(также 16, 32 или 64-битные). 

```asm
    mov ebx, 4
    mov edi, 10
    cmp ebx, 1
    cmovg ebx, edi ;ebx == 10 
```

```asm
    mov al, 255
    mov ah, 1
    add al, ah ;переполнение
    mov eax, 10
    mov edi, -10
    cmovc eax, edi ;eax == -10
```

## **loop** *метка* - цикл

Для циклов есть более короткая запись

Для счетчика используется регистр rcx / ecx / ... и их наименьшие биты

- **loop** - уменьшает регистр ecx на 1 и если он не равен нулю, перепрыгивает на метку
- **loope** - также по мима проверки регистра продолжает если установлен флаг нуля
- **loopne** - также по мима проверки регистра продолжает если **НЕ** установлен флаг нуля

```asm
    mov ecx, 10 ;столько проходов по циклу будет
eee:
    mov edi, ecx ;сохраняем в некий буффер, тк ecx так же используется в printf и после вызова
                 ;значение меняется
    push edi
    push f
    call [printf]

    mov ecx, edi ;возвращяем значение в ecx
    loop eee  ; пока ecx != 0 будет идти цикл и уменьшать ecx на 1
```

**jecxz / jrcxz** - если ecx / rcx == 0, переходит по метке, помогает сразу выйти из цикла если равен нулю


```asm
    mov ecx, 10 ;столько проходов по циклу будет
eee:
    jecxz exit
    mov edi, ecx ;сохраняем в некий буффер, тк ecx так же используется в printf и после вызова
                 ;значение меняется
    push edi
    push f
    call [printf]

    mov ecx, edi ;возвращяем значение в ecx
    loop eee  ; пока ecx != 0 будет идти цикл и уменьшать ecx на 1
exit:
```

## **mul / imul** *op* - умножение

**mul** - умножает числа **БЕЗ** знака  принимает только один параметр и умножает его на значение в *rax*

```asm
    mul operand8   ; если операнд 8-разрядный умножается на al, a результат в AX
    mul operand16  ; если операнд 16-разрядный умножается на ax, a результат в DX:AX
    mul operand32  ; если операнд 32-разрядный умножается на eax, a результат в EDX:EAX
    mul operand64  ; если операнд 64-разрядный умножается на rax, a результат в RDX:RAX
```

```asm
    mov al, 11
    mov bl, 11

    mul bl ; ax = ax * bx
    movsx eax, ax ; надо разширить чтобы можно было коректно запушить
    
    push eax ; eax == 121
    push f
    call [printf]
```

Тк при умножении может не хватить места в регистре, например умножая 255 на 255 в регистрах al и bl, места не хватит и поэтому записываетсяв регистр ax.\
Но когда используются регистры по больше ax,eax и rax, то данные расширяются регистрами dx,edx и rdx, то есть выглядит все примерно так:

```asm
    mov ax, 33000
    mov bx, 3

    mul bx ; теперь регистр ax переполнился и поставился флаг переполнения и переноса
           ; результат записался так: ax == 1000001010111000, dx == 0000000000000001
           ; то есть dx является как бы продолжением регистра ax

    movzx eax, ax
    movzx edx, dx
    
    shl edx, 16 ; смещаем наши данные влево и склабываем в больший регистр или переменную
    add eax, edx ; мб есть способ по легче, пока что хз)

    push eax
    push f
    call [printf]
```
###

**imul** - умножает числа со знаком и так же как и **mul** может принимать один параметр

Но так же есть еще 2 варианта умножения через **imul**

```asm
    imul op1, op2
    imul op1, op2, op3
```

Операторы могут быть только 16, 32 и 64 бита

В варианте где **imul** принимает два параметра, *первый* и *второй* параметр умножаются, и результат записывается в *первый* параметр

В варианте где **imul** принимает три параметра, *второй* и *третий* параметр умножаются, и результат записывается в *первый* параметр


## **div / idiv** *op* - деление

**div** и **idiv** имеют схожий принцип работы как **mul** и **imul**, но idiv отличается тем что он просто работает с числами со знаками, и не имеет дополнительных операндов

```asm
div op8  ; операнд 8-разрядный, делит 16-битное число в ax на операнд, помещая частное в al, а остаток в ah
div op16 ; операнд 16-разрядный, делит 32-битное число в dx:ax на операнд, помещая частное в ax, а остаток в dx
div op32 ; операнд 32-разрядный, делит 64-битное число в edx:eax на операнд, помещая частное в eax, а остаток в edx
div op64 ; операнд 64-разрядный, делит 128-битное число в rdx:rax на операнд, помещая частное в rax, а остаток в rdx
```

То есть при делении важно учитывать что надо задавать число не только в eax, но и edx

```asm
    mov eax, 13
    cdq     ; заполняем edx, можно написать просто edx == 0, но тогда может пропасть знак
    mov ebx, 5

    div ebx

    push edx ; edx == 3 
    push eax ; eax == 2
    push f
    call [printf]
```

Чтобы расширить eax, не потеряв знак
    
- **cbw:** преобразует байт в AL в слово в AX через расширение знаком
- **cwd:** преобразует 16-разрядное число в AX в 32-разрядное в DX:AX через расширение знаком
- **cdq:** преобразует 32-разрядное число в EAX в 64-разрядное в EDX:EAX с помощью расширения знаком
- **cqo:** преобразует 64-разрядное число в RAX в 128-разрядное в RDX:RAX через расширение знаком
- **cwde:** преобразует 16-разрядное число в AX в 32-разрядное в EAX с помощью расширения знаком
- **cdqe:** преобразует 32-разрядное число в EAX в 64-разрядное в RAX с помощью расширения знаком

## **and / or / xor / neg / not** - битовые логические операции

Обычные битовые операции

Результаты вычисления записываются в первый операнд

```asm
and op1, op2 ; битовое И               | op1 & op2
or op1, op2  ; битовое ИЛИ             | op1 | op2
xor op1, op2 ; битовое исключающее ИЛИ | op1 ^ op2
not op1      ; битовое исключение      | !op1
neg op1      ; битовое арифм. отрицане | -op1 -> op1 (типо знак меняет)
```

Инструкции **and**, **or** и **xor** также влияют на регистр флагов. Они всегда сбрасывают флаг переноса CF и переполнения OF. Флаг нуля ZF устанавливается, если результат равен нулю. Флаг знака SF всегда получает значение старшего бита результата.

## **shl / shr / sar / rol / ror** - битовый сдвиг

```asm
shl op1, count ; битовый сдвиг влево     | 10010 >> 2 == 01000
shr op1, count ; битовый сдвиг вправо    | 10010 >> 2 == 00100
sar op1, count ; арифметич. сдвиг вправо | 10010 >> 2 == 11100 (типо при смещении заполняется не нулями, а однерками)
rol op1, count ; битовое вращение влево  | 10010 << 2 == 01010
ror op1, count ; битовое вращение вправо | 10010 >> 2 == 10100
```

Все эти операции при смещении последний из битов копируется в флаг переноса CF, а если результат равен нулю ставят флаг ZF и если вначале бит с **1** меняется на **0** снимается флаг знака SF

```asm
rcl op1, count ; битовое вращение влево с флагом переноса
rcr op1, count ; битовое вращение вправо с флагом переноса
```

Эти две операции работают таким образом

```asm
mov eax, 11100b
rcl eax, 3      ; 11100 << 3 == 11100 -> (CF == 0) -> 11000 -> (CF == 1) -> 10001 -> (CF == 1) -> 00011
```

То есть при смещении заполняется не **0** или **1**, а тем битом который в CF, и бит который смещается попадает в него
