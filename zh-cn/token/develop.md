## 接口



### 创建订单



> POST /openapi/order



#### 请求参数：


```json
{
    "id":"xxxxxxxxx",  // 每次请求必须不一样
    "appid":"xxxxxxxxxx",
    "sign":"xxxxxxxxxxxxxx", // 具体前面算法见后面章节
    "co":"xxxxxxxxxxxxx", // 三方应用订单号
    "blockchain":"xxx", // 所属主链，现在支持eth/neo
    "asset":"xxxxxxxxxxxxx", // 资产类型
    "value":"0x01", // 转账金额,16进制整型
    "decimals":"18", //  转账金额小数位数
    "description":"xxxxxxxxxx", // 订单描述信息
    "extend":"{\"xxx\":1}" // 订单扩展JSON字符串，由应用自行定义
}
```



成功响应报文：

```json
{
    "id":"xxxxxxxxxxx", // 请求报文ID
    "order":"xxxxxxxxxxxxx", // INWE平台订单ID
    "url":"http://xxxxx.com/openapi/order/xxxxxxxx" // INWE平台订单信息提交和查询URL
}
```





### 订单状态回调



> POST /{用户定义}



#### 请求参数



```json
{
    "id":"xxxxxxxxx",  // 每次请求必须不一样
    "appid":"xxxxxxxxxx",
    "sign":"xxxxxxxxxxxxxx", // 具体前面算法见后面章节
    "co":"xxxxxxxxxxxxx", // 三方应用订单号
    "blockchain":"xxx", // 所属主链，现在支持eth/neo
    "asset":"xxxxxxxxxxxxx", // 资产类型
    "value":"0x01", // 转账金额,16进制整型
    "decimals":"18", //  转账金额小数位数
    "description":"xxxxxxxxxx", // 订单描述信息
    "status":"", // 订单状态：ready|onchain|complete|timeout
    "tx":"", // 链上交易ID，可选
    "extend":"{\"xxx\":1}" // 订单扩展JSON字符串，由应用自行定义
}
```





#### 响应参数

成功响应报文：

```json
{
    "id":"xxxxxxxxxxx" // 请求报文ID
}
```



### 获取订单信息



> GET /openapi/order/{平台订单ID}?type={类型}



请求参数

| 请求参数 | 可选值       | 描述         |
| -------- | ------------ | ------------ |
| type     | html/json | 指定响应类型 |



响应参数（JSON）



```json
{
    "id":"xxxxxxxxx",  // 每次请求必须不一样
    "appid":"xxxxxxxxxx",
    "sign":"xxxxxxxxxxxxxx", // 具体前面算法见后面章节
    "co":"xxxxxxxxxxxxx", // 三方应用订单号
    "blockchain":"xxx", // 所属主链，现在支持eth/neo
    "asset":"xxxxxxxxxxxxx", // 资产类型
    "value":"0x01", // 转账金额,16进制整型
    "decimals":"18", //  转账金额小数位数
    "description":"xxxxxxxxxx", // 订单描述信息
    "status":"", // 订单状态：ready|onchain|complete|timeout
    "tx":"", // 链上交易ID，可选
    "extend":"{\"xxx\":1}" // 订单扩展JSON字符串，由应用自行定义
}
```





### 绑定链上交易ID



> POST /openapi/order/{平台订单ID}



请求参数

```json
{
    "id":"",
    "appid":"xxxxxxxxxx",
    "sign":"xxxxxxxxxxxxxx", // 具体前面算法见后面章节
    "tx":"xxxx" // 绑定的订单ID
}
```



成功响应报文：

```json
{
    "id":"xxxxxxxxxxx" // 请求报文ID
}
```





## 收款方接入



1. 调用***创建订单***
2. 生成收款二维码，或者唤醒INWE钱包
3. 网关等待订单状态通知



## 钱包方接入



1. 扫描或者通过应用间通讯获得订单URL；
2. 查询订单状态，展示；
3. 生成对应链上订单；
4. 调用***绑定链上交易ID***



## 接口签名



##### 拼接字符串
1.将json对象字段值按下面展示顺序拼接：


```json
{
    "id":"xxxxxxxxx",  // 每次请求必须不一样
    "appid":"xxxxxxxxxx",
    "sign":"xxxxxxxxxxxxxx", // 具体前面算法见后面章节
    "co":"xxxxxxxxxxxxx", // 三方应用订单号
    "blockchain":"xxx", // 所属主链，现在支持eth/neo
    "asset":"xxxxxxxxxxxxx", // 资产类型
    "value":"0x01", // 转账金额,16进制整型
    "decimals":"18", //  转账金额小数位数
    "description":"xxxxxxxxxx", // 订单描述信息
    "status":"", // 订单状态：ready|onchain|complete|timeout
    "tx":"", // 链上交易ID，可选
    "extend":"{\"xxx\":1}" // 订单扩展JSON字符串，由应用自行定义
}
```

2. sign字段用空字符代替；
3. 可选字段用空字符串代替；
4. 字符串按照uf8编码；
5. 结尾拼接appkey;



##### sha256签名

1. 对第一步结果进行sha256哈希；
2. 对结果再次进行sha256哈希；

##### base64编码

对上一步结果进行base64编码得到最后结果

