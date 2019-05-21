# 行情和盘口WebSocket API 参考 v1

## 连接频道

以**Beta**环境为例，使用WebSocket连接 **/v1/ticker/ETH_USDT** 频道，例如：

``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/ticker/ETH_USDT");
```

客户端请求为：

``` text
GET wss://ws.sfex.net/v1/ticker/ETH_USDT HTTP/1.1
Host: ws.sfex.net
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: zVu/qw6mod9ivrbSex2GBw==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

服务端响应为：

``` text
HTTP/1.1 101 Switching Protocols
Date: Thu, 14 Jun 2018 11:49:21 GMT
Connection: upgrade
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Cache-Control: no-cache, no-store, must-revalidate
Upgrade: WebSocket
Sec-WebSocket-Accept: ip3WBDpEnyzPMRPngEgDZgM+6lU=
```

如果连接不存在的频道，服务器会响应HTTP 404状态码，例如：

``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/unknown");
```

客户端请求为：
``` text
GET wss://ws.sfex.net/v1/unknown HTTP/1.1
Host: ws.sfex.net
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: BP2dOFyaJFIIb2+E77/7fA==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

服务器响应为：

``` text
HTTP/1.1 404 Not Found
Date: Thu, 14 Jun 2018 11:51:47 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: keep-alive
Server: nginx
Content-Encoding: gzip
```

如果连接的频道存在，但参数错误，例如交易对不存在，服务器响应后会主动断开连接，例如：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/ticker/ETH");
```

客户端请求为：
``` text
GET wss://ws.sfex.net/v1/ticker/ETH HTTP/1.1
Host: ws.sfex.net
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: /ulaHe5O/DCNxbF30evMWQ==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

服务端响应为：
``` text
HTTP/1.1 101 Switching Protocols
Date: Thu, 14 Jun 2018 11:54:49 GMT
Connection: upgrade
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Cache-Control: no-cache, no-store, must-revalidate
Upgrade: WebSocket
Sec-WebSocket-Accept: 4RdyPxpw7+pNAG6bpU5l/gwSIEY=
```

## 推送格式
所有推送消息均为JSON，包含:
+ 预留状态码(**code**);
+ 数据(**data**)

示例如下：
``` javascript
{
    "code":0,   // 预留状态码，当前版本尚未使用
    "data":{
        ...     // 推送数据
    }
}
```


## 公共频道

### 大盘周期频道
URI：**/v1/market/histories/{period}**  

| 参数名         | 类型        | 说明 | 是否必需 |
| ------------- |:------------- | :-----| :-----:|
| period | 字符串 | 行情周期 | 是 |

#### 周期参数(**period**)

|  参数  | 定义  |  参数  | 定义  |  参数  | 定义  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  一分钟  |  1h   |  一小时  |  1d   |    日    |  
|  5m   |  五分钟  |  2h   |  两小时  |  1w   |    周    |
|  15m  | 十五分钟 |  4h   |  四小时  |  1M   |    月    |
|  30m  | 三十分钟 |  6h   |  六小时  |
|       |         |  12h  | 十二小时 |

连接示例：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/market/histories/1d");
```

推送示例：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1558281600000,  	// 时间，单位毫秒  
            "values": [
                "0.014215",         	// 起始价
                "0.0146",               // 结束价
                "0.013566",          	// 最低价
                "0.0153",               // 最高价
                "100513608.441913",     // 成交量
                "1443527.430826597222", // 成交额
                "0.014215",         	// 上一周期结束价
                "5608",              	// 成交笔数
                "3190.327951"           // 现量
            ],
            "pair": "SF_USDT"
        },
        {
            "time": 1558281600000,
            "values": [
                "254.71",
                "252.51",
                "245.72",
                "263.82",
                "19189.466857742429919018",
                "4876041.53586594652687020157",
                "254.71",
                "6815",
                "0.1284"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1558281600000,
            "values": [
                "7961.23",
                "8029.43",
                "7806.81",
                "8273.99",
                "864.1006923414809655731",
                "6915332.718113395735820646141",
                "7961.23",
                "6764",
                "0.75"
            ],
            "pair": "BTC_USDT"
        }
    ]
}
```

### 大盘报价频道
URI：**/v1/market/tickers**  

连接示例：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/market/tickers");
```

推送示例：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1558327862318,                  // 时间，单位毫秒
            "pair": "ETH_USDT",                     // 交易对名称
            "current": "252.29",                    // 现价
            "volume_current": "0",                  // 现量
            "increment_24h": "-0.25",               // 24小时价格变化量
            "highest_24h": "263.82",                // 24小时最高价
            "lowest_24h": "245.72",                 // 24小时最低价
            "volume_24h": "36284.85993896757",      // 24小时成交量
            "highest_bid": "252.16",                // 买一价
            "highest_bid_amount": "0.9579",         // 买一量
            "lowest_ask": "252.32",                 // 卖一价
            "lowest_ask_amount": "1.0093",          // 卖一量
            "change_24h": "0.099"                   // 24小时涨跌幅，百分比
        },
        {
            "time": 1558327861505,
            "pair": "BTC_USDT",
            "current": "8002.7",
            "volume_current": "0.0843",
            "increment_24h": "95.91",
            "highest_24h": "8273.99",
            "lowest_24h": "7793.08",
            "volume_24h": "1558.5173757789516",
            "highest_bid": "8002.3",
            "highest_bid_amount": "0.0358",
            "lowest_ask": "8007.6",
            "lowest_ask_amount": "0.0962",
            "change_24h": "1.213"
        },
        {
            "time": 1558327861462,
            "pair": "SF_USDT",
            "current": "0.014611",
            "volume_current": "0",
            "increment_24h": "0.001324",
            "highest_24h": "0.0153",
            "lowest_24h": "0.01325",
            "volume_24h": "214128723.48851597",
            "highest_bid": "0.0145",
            "highest_bid_amount": "3600",
            "lowest_ask": "0.01475",
            "lowest_ask_amount": "1500",
            "change_24h": "9.9646"
        }
    ]
}
```

### 报价频道

URI：**/v1/ticker/{pair}**   
参数：

| 参数名 | 类型   | 说明       | 是否必需 |
| ------ | :----- | :--------- | :------: |
| pair   | 字符串 | 交易对名称 |    是    |

连接示例：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/ticker/ETH_USDT");
```

推送示例：
``` javascript
{
    "code": 0,
    "data": {
        "time": 1558328022319,                      // 时间，单位毫秒
        "pair": "ETH_USDT",                         // 交易对名称
        "current": "252.07",                        // 现价
        "volume_current": "0.18180000000000002",    // 现量
        "increment_24h": "1.37",                    // 24小时价格变化量
        "highest_24h": "263.82",      	            // 24小时最高价
        "lowest_24h": "245.72",       	            // 24小时最低价
        "volume_24h": "36331.08700112615",          // 24小时成交量
        "highest_bid": "251.94",                    // 买一价
        "highest_bid_amount": "0.3094",             // 买一量
        "lowest_ask": "252.11",                     // 卖一价
        "lowest_ask_amount": "0.9994",              // 卖一量
        "change_24h": "0.5406"                      // 24小时涨跌幅，百分比
    }
}
```





### 周期频道

URI：**/v1/history/{pair}/{period}**  
参数：

| 参数名         | 类型        | 说明 | 是否必需 |
| ------------- |:------------- | :-----| :-----:|
| pair | 字符串 | 交易对名称 | 是 |
| period | 字符串 | 行情周期 | 是 |

#### 周期参数(**period**)

|  参数  | 定义  |  参数  | 定义  |  参数  | 定义  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  一分钟  |  1h   |  一小时  |  1d   |    日    |  
|  5m   |  五分钟  |  2h   |  两小时  |  1w   |    周    |
|  15m  | 十五分钟 |  4h   |  四小时  |  1M   |    月    |
|  30m  | 三十分钟 |  6h   |  六小时  |
|       |         |  12h  | 十二小时 |

连接示例：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/history/ETH_USDT/1m");
```

推送示例：  

``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1558331820000,          // 时间，单位毫秒
            "pair": "ETH_USDT",             // 交易对名称
            "values": [
                "251.77",                   // 起始价
                "252.02",                   // 结束价
                "251.77",                   // 最低价
                "252.02",                   // 最高价
                "0.8594999999999999",       // 成交量
                "216.611189999999974798",   // 成交额
                "251.77",                   // 上一周期结束价
                "1",                        // 成交笔数
                "0.8594999999999999"        // 现量
            ]
        }
    ]
}
```

### 成交频道

URI：**/v1/deal/{pair}**  
参数：

| 参数名         | 类型  | 说明          | 是否必需 |
| ------------- | :-----|:------------- | :-----:|
| pair | 字符串 | 交易对名称 | 是 |

连接示例：
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/deal/ETH_USDT");
```

推送示例：  

``` javascript
{
    "code": 0,
    "data": [        
        {
            "time": 1558332033609,  // 时间，单位毫秒
            "side": "sell",         // 成交方向
            "values": [
                "251.7",            // 成交价
                "4.23795"           // 成交量
            ]
        }
    ]
}
```



### 订单频道

URI：**/v1/orderbook/{pair}/{offset}/{accuracy}/{size}**  
参数：

| 参数名         | 类型        | 说明 | 是否必需 |
| ------------- |:------------- | :-----| :-----:|
| pair | 字符串 | 交易对名称 | 是 |
| size | 整型 | 档位数，可选：5、10、20 | 是 |
| offset | 整型 | 浮点位偏移量 | 是 |
| accuracy | 整型 | 统计精度 | 是 |

#### 统计精度(**accuracy**)
用于多空双方查询、订单推送接口，进位模式half_even

| 参数 |     定义      |
| :--: | :-----------: |
|  1   |  精度保留为1  |
|  2   | 精度保留为2.5 |
|  5   |  保留精度为5  |



#### 浮点位偏移量(**offset**)

用于涉及买卖双方的API，进位模式half_even，可选范围：**0～10**；
偏移量最大为10，当交易品种浮点位精度小于10时，最大偏移量为该交易品种浮点位精度

推送格式：

| 参数名         | 类型  | 说明          |
| :------------ | :-----|:------------- |
| asks | 数组 | 卖方订单档位集合 |
| bids | 数组 | 买方订单档位集合 |

连接示例
``` javascript
var socket = new WebSocket("wss://ws.sfex.net/v1/orderbook/ETH_USDT/0/1/5");
```

示例：
``` javascript
{
    "code": 0,
    "data": {
        "asks": [
            {
                "values": [
                    "251.53",   // 卖价
                    "1.0087"    // 卖量
                ]
            },
            {
                "values": [
                    "251.55",
                    "0.9987"
                ]
            },
            {
                "values": [
                    "251.57",
                    "0.9991"
                ]
            },
            {
                "values": [
                    "251.6",
                    "1.0083"
                ]
            },
            {
                "values": [
                    "251.62",
                    "1.0029"
                ]
            }
        ],
        "bids": [
            {
                "values": [
                    "251.36",   // 买价
                    "0.9039"    // 买量
                ]
            },
            {
                "values": [
                    "251.34",
                    "0.1897"
                ]
            },
            {
                "values": [
                    "251.31",
                    "0.9623"
                ]
            },
            {
                "values": [
                    "251.29",
                    "0.9482"
                ]
            },
            {
                "values": [
                    "251.28",
                    "0.9525"
                ]
            }
        ]
    }
}
```
