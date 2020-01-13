

# JS实现分钟数和时间小时 格式的转换

1、**分钟数转换为 小时：分钟** (85 ----> 01:25)

```js
function ChangeHourMinutestr(str) {
    if (str !== "0" && str !== "" && str !== null) {
        return ((Math.floor(str / 60)).toString().length < 2 ? "0" + (Math.floor(str / 60)).toString() :
            (Math.floor(str / 60)).toString()) + ":" + ((str % 60).toString().length < 2 ? "0" + (str % 60).toString() : (str % 60).toString());
    } else {
        return "";
    }
}
```

　**2、小时分钟格式转换为分钟数** (01:25 ----> 85)

```js
function ChangeStrToMinutes(str) {
    var arrminutes = str.split(":");
    if (arrminutes.length == 2) {
        var minutes = parseInt(arrminutes[0]) * 60 + parseInt(arrminutes[1]);
        return minutes;
    } else {
        return 0;
    }
}
```

