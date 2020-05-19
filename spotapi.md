# spotapi
## 1. 概述:
### 1.1 文档说明
该文档是对币界数字货币交易所币币交易模块的API接口说明。可以使用此API操作如下  接口。
### 1.2 接入说明
#### 接入URLS
REST API:https://spotapi.bijieex.com
#### 访问方式
目前Http接口使用普通的账户访问，需要在网站自己注册账户完成充值，并转账到币币账户。申请API后，使用API访问（下方认证流程有详细描述）。除标识无鉴权的接口外，其余接口均需要携带鉴权信息访问。
#### 请求格式
所有的API请求都以GET或者POST形式发出。对于GET请求，所有的参数都在路径参数里；对于POST请求，所有参数则以JSON格式发送在请求主体（body）里。
#### 返回格式
所有的接口返回都是JSON格式。在JSON最上层有两个表示请求状态和返回信息的字段："code", "message". 实际的接口返回内容在"data"字段里.
```
//示例:
{
    "code": 200, //200是成功，其余状态均为失败
    "message": "SUCCESS",
    "data": null,
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 常见错误
```
//1.服务器处于不可访问状态
{
    "timestamp": 1554779144701,
    "status": 500,
    "error": "Internal Server Error",
    "message": "For input string: \"null\"",
    "path": "/spot/v1/order/query/openOrders"
}

//2.签名失败
{
    "timestamp": 1554882204933,
    "status": 500,
    "error": "Internal Server Error",
    "message": "签名失败！",
    "path": "/spot/v1/order/place"
}
```
### 1.3 基础信息
##### 获取所有交易对(无鉴权)
此接口获取本站支持的所有交易对。
##### 请求地址
* GET ```/spot/v1/order/query/symbols```
##### 请求参数
无
##### 返回字段
| 字段        | 类型 | 说明         |
| ------------- | ------ | -------------- |
| symbol        | string | 交易对名称 |
| baseIconUrl   | string | 基币图标地址 |
| coinIconUrl   | string | 交易币图标地址 |
| baseSymbol    | string | 结算币种符号 |
| coinSymbol    | string | 交易币种符号 |
| baseCoinScale | int    | 基币小数精度 |
| coinScale     | int    | 交易币小数精度 |

##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": [
        {
            "symbol": "AUD/BTC",
            "baseIconUrl": null,
            "coinIconUrl": null,
            "baseSymbol": "BTC",
            "coinSymbol": "AUD",
            "coinScale": 4,
            "baseCoinScale": 4
        },
        {
            "symbol": "ETH/BTC",
            "baseIconUrl": null,
            "coinIconUrl": null,
            "baseSymbol": "BTC",
            "coinSymbol": "ETH",
            "coinScale": 0,
            "baseCoinScale": 0
        }
    ],
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```

### 1.4 认证流程
所有请求中的apiKey+ts+apiSecret进行MD5加密得到32位大写加密结果。
GET请求直接在请求后拼接,POST请求添加这几个参数后提交。
```
如:"ts=1554876300000,apiKey=13866191570,apiSecret=app_secret"
MD5加密后得到：8F322B2105775813215844F09F137FF0
参考网站:https://md5jiami.51240.com/
```
### 1.5 账户相关
##### 请求地址
* GET  ```/spot/v1/order/query/wallet```
##### 请求参数
无。
##### 返回字段
| 字段        | 类型 | 说明         |
| ------------- | ------ | -------------- |
| id        | Long | 钱包标识 |
| memberId   | Long | 用户标识 |
| coinId   | string | 币种名称 |
| balance    | decimal | 余额 |
| frozenBalance    | decimal | 冻结 |
| isLock | int    | 是否锁定 0:锁定 1正常 |

##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": [
        {
            "id": null,
            "memberId": 12793,
            "coinId": "AUD",
            "balance": 0,
            "frozenBalance": 0,
            "isLock": 1,
            "tradeDay": null
        },
        {
            "id": null,
            "memberId": 12793,
            "coinId": "BCH",
            "balance": 0,
            "frozenBalance": 0,
            "isLock": 1,
            "tradeDay": null
        }
    ],
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
### 1.6 币币交易
#### 1.6.1 下单
##### 请求地址
* POST  ```/spot/v1/order/place```
##### 请求参数
```
{"orderQty":2,"price":2.5,"priceType":1,"side":1,"symbol":"BTC/USDT"}
```
##### 返回字段
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| orderId        | string | 订单标识 |
| memberId   | Long | 用户标识 |
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| priceType    | int | 价格类型 0市价 1限价|
| orderQty | decimal    | 数量 |
| price | decimal    | 价格 |
| status | int    | 是否锁定 0:委托就绪 1委托接收  2委托拒绝 4部分成交 5全部成交 6已撤单|
| orderType | int    | 0:普通单 1:杠杆单 |
| tradedAmount | decimal    | 交易量 |
| turnover | decimal    | 交易额 |
| completedTime | Long    | 订单完成时间 |
| canceledTime | Long    | 订单取消时间 |
| transactTime | Long    | 交易时间 |
| server | int    | 下单服务 0:交易服务 1机器人服务 |
| remark | string    | 说明 |
##### 返回示例
```
{
    "code": 200,
    "message": "操作成功",
    "data": {
        "orderId": "E19172461939167105513",
        "memberId": 12793,
        "symbol": "BTC/USDT",
        "side": 1,
        "priceType": 1,
        "orderQty": 2,
        "price": 2.5,
        "transactTime": 1554787871504,
        "status": 1,
        "remark": "",
        "orderType": 0,
        "tradedAmount": 0,
        "turnover": 0,
        "completedTime": 0,
        "canceledTime": 0,
        "coinSymbol": "BTC",
        "baseSymbol": "USDT",
        "server": "0",
        "details": []
    },
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.2 撤单
##### 请求地址
* GET  ```/spot/v1/order/{orderId}/submitcancel```
##### 请求参数
```
orderId:订单标识
```
##### 返回字段

##### 返回示例
```
{
    "code": 200,
    "message": "撤单申请提交成功",
    "data": null,
    "count": null,
    "responseString": "200~CANCEL_ORDER_SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.3 批量撤单
##### 请求地址
* POST  ```/spot/v1/order/batchcancel```
##### 请求参数
```
//最多可一次传递50个 委托单标识数组
{"orderIds":["E3780743294399654724","E3780743294399654724"]}
```
##### 返回示例
```
//全部成功
{
    "code": 200,
    "message": "SUCCESS",
    "data": null,
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
//全部失败
{
    "code": 500,
    "message": "全部失败！",
    "data": null,
    "count": null,
    "responseString": "500~全部失败！",
    "url": null,
    "cid": null
}
//部分成功
{
    "code": 502,
    "message": "部分撤单申请成功",
    "data": {
        "errorIds": [
            "E19184242913757816114"
        ]
    },
    "count": null,
    "responseString": null,
    "url": null,
    "cid": null
}
```
#### 1.6.4 查询当前委托
##### 请求地址
* POST  ```/spot/v1/order/query/openOrders```
##### 请求参数
```
{"pageNo":1,"pageSize":10,"symbol":"BTC/USDT"}
```
##### 返回字段
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| orderId        | string | 订单标识 |
| memberId   | Long | 用户标识 |
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| priceType    | int | 价格类型 0市价 1限价|
| orderQty | decimal    | 数量 |
| price | decimal    | 价格 |
| status | int    | 是否锁定 0:委托就绪 1委托接收  2委托拒绝 4部分成交 5全部成交 6已撤单|
| orderType | int    | 0:普通单 1:杠杆单 |
| tradedAmount | decimal    | 交易量 |
| turnover | decimal    | 交易额 |
| completedTime | Long    | 订单完成时间 |
| canceledTime | Long    | 订单取消时间 |
| transactTime | Long    | 交易时间 |
| server | int    | 下单服务 0:交易服务 1机器人服务 |
| remark | string    | 说明 |
##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": {
        "pageNo": 1,
        "pageSize": 10,
        "totalPage": 1,
        "totalNum": 1,
        "list": [
            {
                "orderId": "E19172461939167105513",
                "memberId": 12793,
                "symbol": "BTC/USDT",
                "side": 1,
                "priceType": 1,
                "orderQty": 2,
                "price": 2.5,
                "transactTime": 1554787871504,
                "status": 1,
                "remark": "",
                "orderType": 0,
                "tradedAmount": 0,
                "turnover": 0,
                "completedTime": 0,
                "canceledTime": 0,
                "coinSymbol": "BTC",
                "baseSymbol": "USDT",
                "server": "0",
                "details": []
            }
        ],
        "records": null
    },
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.4.2 查询历史委托
##### 请求地址
* POST  ```/spot/v1/order/query/history```
##### 请求参数
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| pageNo        | int | 页码号 |必传
| pageSize   | int | 每页数量 |必传
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| startTime    | Long | 开始时间 不传递默认为7天前|
| endTime    | Long | 结束时间 不传递默认为当前时间|
```示例
{
	"apiKey":"test-key",
	"ts":1554876300000,
	"sign":"F080087679837EDB38BB8A223836DEE2",
	"pageNo":1,
	"pageSize":100,
	"startTime":0,
	"symbol":"BTC/USDT",
	"side":0
}
```
##### 返回字段
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| orderId        | string | 订单标识 |
| memberId   | Long | 用户标识 |
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| priceType    | int | 价格类型 0市价 1限价|
| orderQty | decimal    | 数量 |
| price | decimal    | 价格 |
| status | int    | 是否锁定 0:委托就绪 1委托接收  2委托拒绝 4部分成交 5全部成交 6已撤单|
| orderType | int    | 0:普通单 1:杠杆单 |
| tradedAmount | decimal    | 交易量 |
| turnover | decimal    | 交易额 |
| completedTime | Long    | 订单完成时间 |
| canceledTime | Long    | 订单取消时间 |
| transactTime | Long    | 交易时间 |
| server | int    | 下单服务 0:交易服务 1机器人服务 |
| remark | string    | 说明 |
##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": {
        "pageNo": 1,
        "pageSize": 100,
        "totalPage": 1,
        "totalNum": 1,
        "list": [
            {
                "orderId": "E5115793610358684825",
                "memberId": "58",
                "symbol": "BTC/USDT",
                "side": 0,
                "priceType": 1,
                "orderQty": 2.0,
                "price": 310.0,
                "transactTime": "1561631118169",
                "status": 5,
                "remark": "",
                "orderType": 1,
                "tradedAmount": 2.0,
                "actualTradedAmount": 620.0,
                "fee": 0.002,
                "turnover": 620.0,
                "completedTime": "1561710832299",
                "canceledTime": "0",
                "coinSymbol": "BTC",
                "baseSymbol": "USDT",
                "server": "0",
                "details": []
            }
        ],
        "records": null
    },
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.5 查询委托明细
##### 请求地址
* GET  ```/spot/v1/order/query/detail/{orderId}?symbol=××```
##### 请求参数
```
orderId:订单标识
symbol:交易对
```
##### 返回字段
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| orderId        | string | 订单标识 |
| memberId   | Long | 用户标识 |
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| priceType    | int | 价格类型 0市价 1限价|
| orderQty | decimal    | 数量 |
| price | decimal    | 价格 |
| status | int    | 是否锁定 0:委托就绪 1委托接收  2委托拒绝 4部分成交 5全部成交 6已撤单|
| orderType | int    | 0:普通单 1:杠杆单 |
| tradedAmount | decimal    | 交易量 |
| actualTradedAmount | decimal    | 交易额（市价买单实际交易额） |
| turnover | decimal    | 交易额 |
| completedTime | Long    | 订单完成时间 |
| canceledTime | Long    | 订单取消时间 |
| transactTime | Long    | 交易时间 |
| server | int    | 下单服务 0:交易服务 1机器人服务 |
| remark | string    | 说明 |
##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": {
        "orderId": "E33812648393251923988",
        "memberId": 13240,
        "symbol": "LTC/USDT",
        "side": 0,
        "priceType": 1,
        "orderQty": 542.2498,
        "price": 101.41,
        "transactTime": 1558764667021,
        "status": 5,
        "remark": "",
        "orderType": 0,
        "tradedAmount": 542.2498,
        "actualTradedAmount": 54966.557911,
        "transactionPrice": 101.46906369,
        "fee": 0.5422498,
        "turnover": 54966.557911,
        "completedTime": 1558764667022,
        "canceledTime": 0,
        "coinSymbol": "LTC",
        "baseSymbol": "USDT",
        "server": "2",
        "details": []
    },
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.6 查询成交明细
##### 请求地址
* GET  ```/spot/v1/order/query/fill/{orderId}?symbol=××```
##### 请求参数
```
orderId:订单标识
symbol:交易对
```
##### 返回字段
| 字段        | 类型 | 说明         
| ------------- | ------ | -------------- |
| orderId        | string | 订单标识 |
| memberId   | Long | 用户标识 |
| symbol   | string | 币种名称 |
| side    | int | 买卖方向 0买 1卖|
| priceType    | int | 价格类型 0市价 1限价|
| orderQty | decimal    | 数量 |
| price | decimal    | 价格 |
| status | int    | 是否锁定 0:委托就绪 1委托接收  2委托拒绝 4部分成交 5全部成交 6已撤单|
| orderType | int    | 0:普通单 1:杠杆单 |
| tradedAmount | decimal    | 交易量 |
| turnover | decimal    | 交易额 |
| completedTime | Long    | 订单完成时间 |
| canceledTime | Long    | 订单取消时间 |
| transactTime | Long    | 交易时间 |
| server | int    | 下单服务 0:交易服务 1机器人服务 |
| remark | string    | 说明 |
##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": [
        {
            "id": "F225808061616437860",
            "memberId": 12740,
            "orderId": "E22575304425815822166",
            "side": 1,
            "symbol": "ETH/USDT",
            "price": 190,
            "amount": 0.1347,
            "turnover": 25.593,
            "fee": 0.179151,
            "time": 1555128705887
        }
    ],
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.6.7 收藏币对
##### 请求地址
* GET  ```/spot/v1/favor/add/{symbol}```
##### 请求参数
```
symbol:如BTC-USDT
```

#### 1.6.8 删除币对
##### 请求地址
* GET  ```/spot/v1/favor/delete/{symbol}```
##### 请求参数
```
symbol:如BTC-USDT
```

#### 1.6.9 查找收藏
##### 请求地址
* GET  ```/spot/v1/favor/find```
##### 请求参数
```
无请求参数
```

### 1.7 行情信息
#### 1.7.1 深度信息(无鉴权)
##### 请求地址
* GET  ```/spotMarket/v1/market/spot/depth/{symbol}/{depth}```
##### 请求参数
```
symol:交易对 如BTC-USDT
depth:深度
```
##### 返回示例
```
{
    "code": 200,
    "message": "SUCCESS",
    "data": {
        "ask": [
            {
                "price": 2.5,
                "amount": 2
            }
        ],
        "bid": []
    },
    "count": null,
    "responseString": "200~SUCCESS",
    "url": null,
    "cid": null
}
```
#### 1.7.2 币币ticker信息(无鉴权)
##### 请求地址
* GET  ```/spotMarket/data/api//tickerSPOT/{symbol}```
##### 请求参数
```
api.bijieex.com/spotMarket/data/api/ticker/SPOT{btc_usdt}
```
##### 返回示例
```
{
	"code": 200,
	"data": {
		"baseCoinScale": 2,
		"baseUsdRate": 1,
		"change": 0,
		"chg": 0,
		"close": 3583.06,
		"coinScale": 5,
		"high": 0,
		"lastDayClose": 3583.06,
		"low": 0,
		"open": 0,
		"startTime": 1554825600000,
		"symbol": "BTC/USDT",
		"turnover": 0,
		"usdRate": 3583.06,
		"volume": 0
	},
	"message": "SUCCESS",
	"responseString": "200~SUCCESS"
}
```
#### 1.7.2 币币所有ticker信息(无鉴权)
##### 请求地址
* GET  ```/spotMarket/data/api/ticker/SPOT```
##### 请求参数
请求无参数

##### 返回结果
```
symbol：交易对
high：最高价
low：最低价
last：最新价
change：涨幅
vol：成交量
```
##### 返回示例
```
{
	"ticker": [{
		"symbol：": "btc_cm01",
		"high": 0,
		"vol": 0.00,
		"last": 0,
		"low": 0,
		"change": 0
	}, {
		"symbol": "eth_usdt",
		"high": 177.0,
		"vol": 222347.273777185241766644,
		"last": 172.57,
		"low": 0,
		"change": 0.57
	}, {
		"symbol": "btc_usdt",
		"high": 5333.0,
		"vol": 8631.567033124698562103,
		"last": 5282.39,
		"low": 0,
		"change": 19.22
	}, {
		"symbol": "spt_usdt",
		"high": 1.8,
		"vol": 0.00,
		"last": 1.8,
		"low": 0,
		"change": 0.0
	}, {
		"symbol": "bch_usdt",
		"high": 285.0,
		"vol": 30.00,
		"last": 285.0,
		"low": 0,
		"change": 0.0,
		"sell": 284
	}, {
		"symbol": "eos_usdt",
		"high": 5.41234548,
		"vol": 0.00,
		"last": 5.41234548,
		"low": 0,
		"change": 0E-8
	}],
	"data": "1555764116"
}
```


