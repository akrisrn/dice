# Dice

COC六版规则跑团用骰子服务器。

监听本机端口收取数据，根据传入指令决定掷骰结果，并打包成http数据包返回。

## 传入

格式：

```json
{
   "sender": "发送者",
   "content": "内容（指令）"
}
```

举例：

```json
{
    "sender": "鸽王",
    "content": "d20"
}
```

## 传出

格式（code不重要）：

```json
{
    "reply": "回复（掷骰结果）",
    "code": 0
}
```

举例：

```json
{
    "reply": "[鸽王]: (1-20) → 1",
    "code": 0
}
```

程序默认会打包成http数据包，除去json还包含以下头。可在代码里去掉（package方法）。

```
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json;charset=UTF-8
Date: Date
Content-Length: Length
Server: dice.server"
```

## 指令

有五层正则匹配，层层递进。

### 第一层

```
匹配1个xdy、xDy、dy、Dy，x(1-999)，y(1-999)，取x、y
```

### 第二层

```
匹配0或多个+xdy、+xDy、+dy、+Dy、
           -xdy、-xDy、-dy、-Dy、
           /xdy、/xDy、/dy、/Dy、
           *xdy、*xDy、*dy、*Dy、
           +z、-z、/z、*z，x(1-999)，y(1-999)，z(0-999)，取全部
```

### 第三层

```
匹配0或1个>xdy、<xdy、=xdy、!xdy、>=xdy、<=xdy、==xdy、!=xdy、
          >xDy、<xDy、=xDy、!xDy、>=xDy、<=xDy、==xDy、!=xDy、
          >dy、<dy、=dy、!dy、>=dy、<=dy、==dy、!=dy、
          >Dy、<Dy、=Dy、!Dy、>=Dy、<=Dy、==Dy、!=Dy、
          >z、<z、=z、!z、>=z、<=z、==z、!=z，x(1-999)，y(1-999)，z(0-999)，取全部
```

### 第四层

```
匹配0或多个+xdy、+xDy、+dy、+Dy、
           -xdy、-xDy、-dy、-Dy、
           /xdy、/xDy、/dy、/Dy、
           *xdy、*xDy、*dy、*Dy、
           +z、-z、/z、*z，x(1-999)，y(1-999)，z(0-999)，取全部
```

### 第五层

```
匹配0或多个空白符以及0或多个任意字符，取任意字符
```

## 复数指令

多个指令之间用`\n`隔开。

