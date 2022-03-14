# json

json作为编码方式，经常用到，像

## encoding/json的基本用法

### 忽略key

json tag里面的"\*\*omitempty"**与**"-"\*\*分别代表序列化的时候如果为零值则忽略，与不参与序列化

```
type User struct {
    UID int `json:"uid,omitempty"`
}
// 忽略零值，如果UID为0则生成的字符串中无uid字段。
```

### 字符串转化为数字

前端 js对int64的支持存在问题，很多时候我们想把后端的int64转化为string传给前端，那么我们可以用如下的tag表示

```
type User struct {
    UID int64 `json:"uid,string"`
}
//result
{"uid":"123131"}
```

### 自定义MarshalJSON与UnmarshalJSON

```
type MyTime time.Time

func (m MyTime) MarshalJSON() ([]byte, error) {
	return []byte(fmt.Sprintf("\"%s\"", time.Now().Format("2006"))), nil
}

func (m *MyTime) UnmarshalJSON(data []byte) error {
	utime, err := strconv.ParseInt(string(data), 10, 64)
	if err != nil {
		return err
	}
	*m = MyTime(time.Unix(utime, 0))
	return nil
}

type args struct {
     Time MyTime `json:"time"`
}

序列化
json.Marshal(args{Time: MyTime(time.Now())})
结果是{"time":"2006"}

反序列化
var str = `{"time":1646701447}`
var res args
json.Unmarshal([]byte(str), &res)
```

##

## 高性能json解析库
