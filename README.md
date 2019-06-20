## 目录
[接口定义](#接口定义)

　　[1 获取服务器列表](#1-获取服务器列表)

　　[2 获取钱包余额](#2-获取钱包余额)
  
　　[3 查询历史交易记录](#3-查询历史交易记录)
  
　　[4 查询历史转账记录](#4-查询历史转账记录)
  
　　[5 查询当前委托](#5-查询当前委托)

　　[6 获取交易序列号(前端签名需要)](#6-获取交易序列号前端签名需要)
  
　　[7 挂单(前端签名)](#7-挂单前端签名)

　　[8 撤单(前端签名)](#8-撤单前端签名)

　　[9 转账(前端签名)](#9-转账前端签名)

　　[10 转账(非签名)](#10-转账非签名)

　　[11 获取市场深度](#11-获取市场深度)

　　[12 获取K线数据](#12-获取K线数据)

　　[13 获取最新成交](#13-获取最新成交)

　　[14 获取24小时行情数据](#14-获取24小时行情数据)

　　[15 获取交易详情](#15-获取交易详情)

## 接口定义

### 1 获取服务器列表

HTTP请求

`GET https://jccdex.cn/static/config/jc_config.json`

请求参数: 无

返回参数: 所有服务器配置信息，配置说明如下:

exHosts：交易服务器列表，默认端口为80

infoHosts：信息服务器列表，默认端口为80

**说明**

**以下所有API请求对应的host都是基于exHosts或者infoHosts的**
* **接口路由中以/exchange开头的请调用exHosts列表中服务器**
* **接口路由中以/info开头的请调用infoHosts列表中服务器**
* **为了减轻单个服务器负载，请用轮询机制分别访问列表中每台服务器**

### 2 获取钱包余额

HTTP请求

GET [exHost]/exchange/balances/:address

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
address|String|是|钱包地址

返回参数:

**code: "0"表示正常(以下所有接口返回格式都相同，不再重复描述)**

**msg: 接口返回的状态信息描述，与code对应(以下所有接口返回格式都相同，不再重复描述)**

data: 接口返回的业务数据区,字段描述如下：

参数名|参数类型|描述
--|:--:|--:
value|String|钱包余额，包含冻结金额
currency|String|币种的token
title|String|币种名称
freezed|String|冻结金额

返回示例

```json
{"code":"0","data":[{"value":"105140.995617","currency":"SWT","issuer":"","freezed":"105080","reserve":"20","title":"SWT"},{"value":"2082.279989407674","currency":"UST","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","freezed":"0.000000","title":"UST"},{"value":"39.1177254901961","currency":"VCC","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","freezed":"0.000000","title":"VCC"},{"value":"26515.66573018317","currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","freezed":"25192.150000","title":"CNY"}],"msg":"获得余额成功"}

```

### 3 查询历史交易记录

HTTP请求

查询第一页:

GET [exHost]/exchange/tx/:address/

查询下一页:

GET [exHost]/exchange/tx/:address/?ledger=&seq=

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
address|String|是|钱包地址
ledger|String|否|区块账本编号，从第二页开始需要，该值由上一页查询返回
seq|String|否|交易序列号，从第二页开始需要，该值由上一页查询返回

返回参数:

参数名|参数类型|描述
--|:--:|--:
ledger|Number|下次请求的账本编号，
seq|Number|下次请求的交易序列号
hash|String|交易hash,   如果是用"/"分割，那么左边表示主动成交方，右边表示被动成交方
time|Timestamp|交易时间(秒)
type|String|买、卖类型 sell-卖 buy-买
pairs|String|交易对
amount|String|交易数量
sum|String|交易总价格
price|String|单价
status|String|交易状态 offer_bought-主动成交 offer_funded-被动成交 offer_partially_funded-部分成交 offer_cancelled-取消挂单 offer_created-创建挂单 

返回示例

```json
{"code":"0","data":{"marker":{"ledger":11588678,"seq":4},"transactions":[{"hash":"D166357BF8E0FBAB5B450379A0DB19BA55A773075E0F52B06B7CD6A8406E8219","time":1546070620,"type":"sell","pairs":"MOAC/CNT","amount":"818","sum":"3272","price":"4","status":"offer_cancelled"},{"hash":"DE8E6E919B047F42AC76AF9DA19578042B78FD5E0D26C37BACE4679FF61FA72D","time":1546051770,"type":"sell","pairs":"MOAC/CNT","amount":"818","sum":"3272","price":"4","status":"offer_created"},{"hash":"B3002E48CCCF63845E72EDADFD4C1F05BB18D1E8781CE1B07B8E2E5AC7FCB6C5","time":1546051670,"type":"buy","pairs":"ETH/CNT","amount":"0.01","sum":"7.97","price":"797","status":"offer_cancelled"},{"hash":"19AE039FDF6DFF6CA1F51D84CC1F34197C32E5366F0D8DD4491F75C7201065FD","time":1546050330,"type":"buy","pairs":"ETH/CNT","amount":"0.01","sum":"7.97","price":"797","status":"offer_created"},{"hash":"C0781571CDACC228C475034198D0D9CB238DFA5EDD43EE8575E9CE51D47C66D9","time":1545900180,"type":"buy","pairs":"SWTC/CNT","amount":"1000000","sum":"3050","price":"0.00305","status":"offer_cancelled"},{"hash":"89DF2578BFCC39BE7815DE9FEE0737834947C9A99410D401098AD8526EF58878/8AE6E7D1C916AA9BB4983ABCC2CE98CE9FD57E8E7E66FDCD1C0FEFF19B8A3A81/undefined","time":1545899990,"type":"sell","pairs":"MOAC/CNT","amount":"26.7","sum":"81.7019999999999","price":"3.05999999999999625468","status":"offer_bought","counterparty":{"account":"jnpCL7vYAnAhjdQR7iVS4yeJ6t2tbw3KNp","seq":72,"hash":"8AE6E7D1C916AA9BB4983ABCC2CE98CE9FD57E8E7E66FDCD1C0FEFF19B8A3A81"}},{"hash":"89DF2578BFCC39BE7815DE9FEE0737834947C9A99410D401098AD8526EF58878/610A779839F3481D49493AEC1B2CADDBD217C2F94B22CA3591E13B132C605581/undefined","time":1545899990,"type":"sell","pairs":"MOAC/CNT","amount":"1.38","sum":"4.2366","price":"3.07","status":"offer_bought","counterparty":{"account":"jJ2XV8PWwdcHNXb5kAt9LutAq45Lqrt3DS","seq":6405,"hash":"610A779839F3481D49493AEC1B2CADDBD217C2F94B22CA3591E13B132C605581"}},{"hash":"89DF2578BFCC39BE7815DE9FEE0737834947C9A99410D401098AD8526EF58878/C428557458D18DAD7F053B1FA85E29C7117CEDD3404C57C050C7ECD5B2AB0D6F/undefined","time":1545899990,"type":"sell","pairs":"MOAC/CNT","amount":"600","sum":"1824","price":"3.04","status":"offer_bought","counterparty":{"account":"jMP1BwtzxpqnxMYt5oWzCPjiSGekrw5Kkj","seq":92,"hash":"C428557458D18DAD7F053B1FA85E29C7117CEDD3404C57C050C7ECD5B2AB0D6F"}},{"hash":"89DF2578BFCC39BE7815DE9FEE0737834947C9A99410D401098AD8526EF58878/FC542B141AE876A04DE0BDB331CCD319754365AF2BE3BF31430E168272D2DB7D/undefined","time":1545899990,"type":"sell","pairs":"MOAC/CNT","amount":"180.07","sum":"547.4128","price":"3.04","status":"offer_bought","counterparty":{"account":"jpsus5muo2pt4MJiY98eXMDMGyQQiqen17","seq":11,"hash":"FC542B141AE876A04DE0BDB331CCD319754365AF2BE3BF31430E168272D2DB7D"}},{"hash":"89DF2578BFCC39BE7815DE9FEE0737834947C9A99410D401098AD8526EF58878/1A8215FB7DCA9F09A6BC02085B2E9B808EAABB4FE7E32B79B27028A061653098/undefined","time":1545899990,"type":"sell","pairs":"MOAC/CNT","amount":"9.85","sum":"30.0425","price":"3.05","status":"offer_bought","counterparty":{"account":"jpw9GNoe4M1nNoof78yWimg1jFvAubvuFv","seq":17,"hash":"1A8215FB7DCA9F09A6BC02085B2E9B808EAABB4FE7E32B79B27028A061653098"}},{"hash":"40406805D40503AB867313511DE2D0D4125B4B3A2D582F0E72750C835597A122/40406805D40503AB867313511DE2D0D4125B4B3A2D582F0E72750C835597A122/4548","time":1545838450,"type":"buy","pairs":"MOAC/CNT","amount":"818","sum":"2454","price":"3","status":"offer_funded","counterparty":{"account":"jMbmoPA3QcWKoDQrhGdiykVkqJPe4cqYnY","seq":200,"hash":"40406805D40503AB867313511DE2D0D4125B4B3A2D582F0E72750C835597A122"}},{"hash":"74ECD94DDF10AB05EC9A3BE5AD0F66C9FC9F429A6990B419B9EDC0744F84BAAD/74ECD94DDF10AB05EC9A3BE5AD0F66C9FC9F429A6990B419B9EDC0744F84BAAD/4549","time":1545806620,"type":"buy","pairs":"MOAC/CNT","amount":"818","sum":"2593.06","price":"3.17","status":"offer_funded","counterparty":{"account":"j9irCqYkPgsQhNSeBpuyjsQTsqSdkVn1Az","seq":26360,"hash":"74ECD94DDF10AB05EC9A3BE5AD0F66C9FC9F429A6990B419B9EDC0744F84BAAD"}},{"hash":"B1BD241FA6863AD626B7FA13363EABABF88064780F0FA8F54F7F430422D5C135","time":1545795830,"type":"buy","pairs":"MOAC/CNT","amount":"818","sum":"2593.06","price":"3.17","status":"offer_created"},{"hash":"1DA1BAD815E0D029EA665B271C6E3B884CD0E91F1658CA8E542342D30FDDC337","time":1545734060,"type":"buy","pairs":"MOAC/CNT","amount":"818","sum":"2454","price":"3","status":"offer_created"}]},"msg":"获取历史交易成功","isActive":true}

```

### 4 查询历史转账记录

HTTP请求

查询第一页:

GET [exHost]/exchange/payments/:address/

查询下一页:

GET [exHost]/exchange/payments/:address/?ledger=&seq=

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
address|String|是|钱包地址
ledger|String|否|区块账本编号，从第二页开始需要，该值由上一页查询返回
seq|String|否|交易序列号，从第二页开始需要，该值由上一页查询返回

返回参数:

参数名|参数类型|描述
--|:--:|--:
ledger|Number|下次请求的账本编号，
seq|Number|下次请求的交易序列号
hash|String|交易hash,
time|Timestamp|交易时间(秒)
sender|String|发送方
receiver|String|接收方
amount|String|交易数量
result|String|***该字段无效,不用处理***

返回示例

```json
{"code":"0","data":{"marker":{"ledger":11490452,"seq":6},"transactions":[{"hash":"E0B3FA8A0438F15C8C8D068B383D19E3F5E1F633BAF756F4509435DF8178A35F","time":1546842750,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"jLUkoiBvewNDvrJMR7q96s67WgoGjmttPr","currency":"MOAC","amount":"194","result":false},{"hash":"8C6796AD7DAC6A48A02C061DA571BA64DC3B3808575BF3552310043ABF274DB4","time":1546842270,"sender":"jG9ntUTuBKqDURPUqbGYZRuRDVzPY6bpxL","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"MOAC","amount":"338","result":false},{"hash":"7415E157D0948B3B8EB47F47DD94076085B65CC3D38A91B552946226B8037096","time":1546323650,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"j991JxDsLeHJp5e8AxggmodTNzZMCybtQJ","currency":"CNT","amount":"7000","result":false},{"hash":"006F38364E64CE3DADE02CAE0576A5A5A613092BE44ED35A1B2D814201577801","time":1545298730,"sender":"jGuN8i25pC14g4gdWkbyAqJ64L7w9z7yN5","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"HJT","amount":"1","result":false},{"hash":"91FF81AB4DE7E640C9D757E6F953B73A7FD6671BB2535EAC807F16181DD5EF34","time":1545298120,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"jGuN8i25pC14g4gdWkbyAqJ64L7w9z7yN5","currency":"HJT","amount":"5","result":false},{"hash":"BB39EBE6CC7AEFA7FB7E61B8AC0C0DFD9C71A99F0EC0D412EA2AE8D51E460BF9","time":1545119220,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"jUkqDXufgPLF2nxQKdu7gGnvXvoKHLNu6x","currency":"SWTC","amount":"1","result":false},{"hash":"C5FC338DBF28AA189F6EBA5F87A5B535C0D77DC93AACB5B1386DFB814D556E9C","time":1545118920,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"jG9ntUTuBKqDURPUqbGYZRuRDVzPY6bpxL","currency":"MOAC","amount":"1","result":false},{"hash":"0000F702471E659FA841DD934B1BDD1A6E0E7561B94E8D6F3570AB328EDC5A04","time":1545118640,"sender":"jG9ntUTuBKqDURPUqbGYZRuRDVzPY6bpxL","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"MOAC","amount":"1","result":false},{"hash":"D68106E33DF258AEAA1AECEDC0570F8A664057C7C92FC20EB9B441F8597F14FE","time":1545013710,"sender":"jDu7umDxKxeaHoj7eNdUn8YsGWTHZSuEGL","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"BIZ","amount":"2","result":false},{"hash":"B1564ED23112A396621AAB8F71E1FDAFDDFEB56EC5A00FE44B3713C24803A770","time":1545013380,"sender":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","receiver":"jDu7umDxKxeaHoj7eNdUn8YsGWTHZSuEGL","currency":"BIZ","amount":"1","result":false},{"hash":"CBB5509682ABD998789E979942F58E07D9C36E8BB9BC5F190566AD68C1B71980","time":1545013110,"sender":"jDu7umDxKxeaHoj7eNdUn8YsGWTHZSuEGL","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"BIZ","amount":"1","result":false},{"hash":"957F315F85E0C4EA7029B1A8B6EAF1568480867DFFACD3E362641D5E37B1C0E5","time":1544788830,"sender":"jG9ntUTuBKqDURPUqbGYZRuRDVzPY6bpxL","receiver":"jHXA8QvnogEw3exos7V2t3UXZmDWhDgHQT","currency":"MOAC","amount":"1","result":false}]},"msg":"获取历史交易成功","isActive":true}

```

### 5 查询当前委托

HTTP请求

查询第一页:

GET [exHost]/exchange/orders/:address/:page

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
address|String|是|钱包地址
page|String|是|页码，该字段暂时无效,可以随便传，但是必须要有

返回参数:

参数名|参数类型|描述
--|:--:|--:
type|String|类型 buy-买 sell-卖
pair|String|交易对
price|String|价格
amount|String|交易数量
sequence|String|交易序列号，用于取消挂单使用
passive|String|***该字段无效，不用处理***

返回示例

```json
{"code":"0","data":[{"type":"buy","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.001000","amount":"1.000000","sequence":4464,"passive":false},{"type":"sell","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.100000","amount":"1.000000","sequence":4465,"passive":false},{"type":"buy","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.001000","amount":"1.000000","sequence":4492,"passive":false},{"type":"buy","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.001000","amount":"1.000000","sequence":4490,"passive":false},{"type":"buy","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.001000","amount":"1.000000","sequence":4491,"passive":false},{"type":"buy","pair":"SWT/CNY+jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","price":"0.004000","amount":"300000.000000","sequence":4557,"passive":false}],"msg":"获得挂单列表成功","isActive":true}

```
### 6 获取交易序列号(前端签名需要)

HTTP请求

查询第一页:

GET [exHost]/exchange/sequence/:address

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
address|String|是|钱包地址

返回参数:

参数名|参数类型|描述
--|:--:|--:
sequence|String|交易序列号

返回示例

```json
{"code":"0","data":{"sequence":4568},"msg":"获得序列号成功","isActive":true}

```

### 7 挂单(前端签名)

HTTP请求

查询第一页:

POST [exHost]/exchange/sign_order

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
sign|String|是|签名后的加密内容

返回参数:

参数名|参数类型|描述
--|:--:|--:
hash|String|交易hash

返回示例

```json
{"code":"0","data":{"hash":"39EC76DAAC1A2BC0662240145C3086110A7AB8864B2B1F4CF3C38616D78DFEA4"},"msg":"挂单提交成功","isActive":true}

```

### 8 撤单(前端签名)

HTTP请求

查询第一页:

DELETE [exHost]/exchange/sign_cancel_order

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
sign|String|是|签名后的加密内容

返回参数:

参数名|参数类型|描述
--|:--:|--:
hash|String|交易hash

返回示例

```json
{"code":"0","data":{"hash":"E601D772D15F2AFC563E8CE2324F0BB684590EA1FB409011111EDDD9EFA36C20"},"msg":"取消挂单提交成功","isActive":true}

```

### 9 转账(前端签名)

HTTP请求

查询第一页:

POST [exHost]/exchange/sign_payment

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
sign|String|是|签名后的加密内容

返回参数:

参数名|参数类型|描述
--|:--:|--:
hash|String|交易hash

返回示例

```json
{"code":"0","data":{"hash":"22240FEC865A2325401BCFAEE80387033F3190B7EF506D11981D96F4F38F13E2"},"msg":"提交支付成功","isActive":true}

```

### 10 转账(非签名)

HTTP请求

查询第一页:

POST [exHost]/exchange/payment

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
secret|String|是|转出钱包的私钥
to|String|是|接收方钱包的地址
amount|String|是|数量
currency|String|是|token名称
issuer|String|是|发行方
memo|String|否|转账备注信息，最长512字节

返回参数:

参数名|参数类型|描述
--|:--:|--:
hash|String|交易hash

返回示例

```json
{"code":"0","data":{"hash":"22240FEC865A2325401BCFAEE80387033F3190B7EF506D11981D96F4F38F13E2"},"msg":"提交支付成功","isActive":true}

```

### 11 获取市场深度

HTTP请求

GET [infoHost]/info/depth/:currency/:type

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
currency|String|是|币种交易对 如:SWT-CNY
type|String|是|类型 normal-只获取最新10条记录 more-获取最新50条记录

返回参数:

参数名|参数类型|描述
--|:--:|--:
price|Number|价格
amount|Number|数量
total|Number|该字段暂时无效 可以忽略
type|String|类型 sell-卖 buy-买

返回示例

```json
{"code":"0","data":{"base":"SWT","counter":"CNY.jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","asks":[{"price":0.00445,"amount":74328,"total":74328,"type":"sell"},{"price":0.00446,"amount":769281,"total":843609,"type":"sell"},{"price":0.00447,"amount":1899950,"total":2743559,"type":"sell"},{"price":0.00448,"amount":1298921,"total":4042480,"type":"sell"},{"price":0.00449,"amount":1912880,"total":5955360,"type":"sell"}],"bids":[{"price":0.00442,"amount":261609,"total":261609,"type":"buy"},{"price":0.00441,"amount":3089357.765534,"total":3350966.765534,"type":"buy"},{"price":0.0044,"amount":1024005,"total":4374971.7655340005,"type":"buy"},{"price":0.00439,"amount":1877847,"total":6252818.7655340005,"type":"buy"},{"price":0.00438,"amount":1742312,"total":7995130.7655340005,"type":"buy"}]},"msg":"获取市场信息成功","success":true}
```

### 12 获取K线数据

HTTP请求

GET [infoHost]/info/kline/:currency/:type

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
currency|String|是|币种交易对 如:SWT-CNY
type|String|是|类型 hour-小时 day-日 week-周 month-月

返回参数: 服务器返回最近的240条数据

参数名|参数类型|描述
--|:--:|--:
array[0]|Timestamp|成交时间(毫秒)
array[1]|Number|开盘价格
array[2]|Number|收盘价格
array[3]|Number|最低价格
array[4]|Number|最高价格
array[5]|Number|交易金额
array[6]|Number|交易量
array[7]|Number|成交笔数

返回示例

```json
{"code":"0","data":[[1526313600000,0.03569,0.04150600000000021,0.03511625798095238,0.044564446944444454,14944051.348589845,371894445.0376669,2897],[1526400000000,0.041600000000000005,0.0409,0.04029380231944445,0.04399294244,10679759.133056931,256273850.651661,37749]],"msg":"获取市场信息成功","success":true}
```

### 13 获取最新成交

HTTP请求

GET [infoHost]/info/history/:currency/:type

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
currency|String|是|币种交易对 如:SWT-CNY
type|String|是|类型 normal-只获取最新10条记录 more-获取最新50条记录

返回参数: 服务器返回最近的240条数据

参数名|参数类型|描述
--|:--:|--:
array[0]|Number|交易金额
array[1]|Number|交易量
array[2]|Number|价格
array[3]|Timestamp|成交时间(毫秒)
array[4]|Number|成交类型 0-买 1-卖
array[5]|Number|撮合标记 1-非撮合 3-3方撮合 4-4方撮合 5-5方撮合 6-6方撮合

返回示例

```json
{"code":"0","data":[[62.60056,14036,0.00446,1547025420000,0,1],[49.9966,11210,0.00446,1547025350000,0,1],[99.99766,22421,0.0044599999999999996,1547025320000,0,1],[30.192000000000917,6800,0.004440000000000135,1547025300000,1,1],[15.442979999999807,3486,0.004429999999999944,1547024770000,1,1],[99.99766000000002,22421,0.004460000000000001,1547024040000,0,1],[786.768,177600,0.00443,1547023510000,1,3],[118.01970000000024,26640,0.004430168918918928,1547023330000,1,3],[0.04460000000000264,10,0.004460000000000264,1547023320000,0,1],[99.99766,22421,0.0044599999999999996,1547023300000,0,1]],"msg":"获取市场信息成功","success":true}
```

### 14 获取24小时行情数据

HTTP请求

GET [infoHost]/info/alltickers

请求参数:(无)

参数名|参数类型|是否必须|描述
--|:--:|--:|--:

参数名|参数类型|描述
--|:--:|--:
array[0]|Timestamp|请求时的当前时间(秒)
array[1]|Number|当前价格
array[2]|Number|日涨跌(大于0表示涨 小于0表示跌 换算成百分比要除100)
array[3]|Number|最高价
array[4]|Number|最低价
array[5]|Number|成交总金额
array[6]|Number|成交总量
array[7]|Number|成交笔数

返回示例

```json
{"code":"0","data":{"VCC-SWT":[1547084660,8166],"ECP-SWT":[1547084660,0.45],"JMOAC-SWT":[1547084660,883,0,883.0000000000025,883,1126821,1276.1279728199304,4],"JJCC-SWT":[1547084660,21.52,-5.738063950941738,22.83,21.519999999999985,387427.18,18003.00000000001,3],"JEKT-SWT":[1547084660,2.2052],"JCALL-SWT":[1547084660,2.39],"JDABT-SWT":[1547084660,32.3],"JBIZ-SWT":[1547084660,0.67,3.076923076923097,0.67,0.6499999999999999,1053479.83,1588380,110],"JSTM-SWT":[1547084660,1.25],"BIC-SWT":[1547084660,11],"YUT-SWT":[1547084660,10.000891000000001],"MYT-SWT":[1547084660,10002],"JSLASH-SWT":[1547084660,1.2199999999999995],"HJT-SWT":[1547084660,1,0,1,1,50000,50000,1],"SWT-JETH":[1547084660,0.000004000000000047208],"JMOAC-JETH":[1547084660,0.0034720400000441344],"VCC-UST":[1547084660,46.709999999999994],"SWT-UST":[1547084660,0.0016],"SWT-CNY":[1547084660,0.004400000000033334,-0.9009009001501275,0.0044500000003608875,0.004399999999999999,28489.051027000005,6440500.777044999,28],"JSTM-CNY":[1547084660,0.0054699999999883175],"JJCC-CNY":[1547084660,0.09841050000000001,-1.5894999999999937,0.10000136060000006,0.09841050000000001,1254.5223606,12612,6],"VCC-CNY":[1547084660,37.85488],"JETH-CNY":[1547084660,1099.5],"JMOAC-CNY":[1547084660,3.98,1.9918605471720603,3.98,3.9,8046.211926002266,2048.999999999999,7],"JCALL-CNY":[1547084660,0.01891708,12.838695092250333,0.01897417,0.01503505,57.09108935060774,3410.2869448400006,324],"JSLASH-CNY":[1547084660,0.10672729999987496]},"msg":"获取市场信息成功","success":true}
```

### 15 获取交易详情

HTTP请求

查询第一页:

POST [exHost]/exchange/detail/:hash

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
hash|String|是|交易hash

返回参数:

返回示例

```json
{"code":"0","data":{"Account":"jPisFRLYpCsfPhreWiTQC2LEHgMYXsH6zP","Amount":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"10000"},"Destination":"jByHB6FCo1M22qSx8ppaHBeEoSw5qbBVTb","Fee":"10000","Flags":0,"Sequence":1145,"SigningPubKey":"025B0FB2C81B0F5FC3379D3BF86C860B52F279D0A24D1CBB49DD74EE354B015175","TransactionType":"Payment","TxnSignature":"30440220518367051DC9BEEF6B977A380942047DF3999986DEFF0344F6E168BB8DD6BAD8022065886A2B091EB4FB96491D473F6C23007031655179001D452D6BF06E4EA0AB5F","date":614318060,"hash":"3F1E8026BF98E3CDBF77BE56AAE60D4FD140F744F77F352F61C26CCABB7A5C03","inLedger":13115827,"ledger_index":13115827,"meta":{"AffectedNodes":[{"ModifiedNode":{"FinalFields":{"Account":"jEoSyfChhUMzpRDttAJXuie8XhqyoPBYvV","Balance":"561952346220","Flags":0,"OwnerCount":1,"Sequence":37745857},"LedgerEntryType":"AccountRoot","LedgerIndex":"109E80FB8CC6D82D4F7F7D77248C2C3C116ECCD4520B3D2A88421FFF94A57B1E","PreviousFields":{"Balance":"561952336220","Sequence":37745856},"PreviousTxnID":"3D77D5E6E33B8652F6915706BE28809C977AD01E76DE7559BC74A2E6E728B28E","PreviousTxnLgrSeq":13115827}},{"ModifiedNode":{"FinalFields":{"Balance":{"currency":"CNY","issuer":"jjjjjjjjjjjjjjjjjjjjBZbvri","value":"-212429355.58"},"Flags":131072,"HighLimit":{"currency":"CNY","issuer":"jPisFRLYpCsfPhreWiTQC2LEHgMYXsH6zP","value":"1000000000000000e-4"},"HighNode":"0000000000000000","LowLimit":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0"},"LowNode":"00000000000008D6"},"LedgerEntryType":"SkywellState","LedgerIndex":"67DCCF3BECD96092FEB89A9B66BDB6DB45772DA42CA83BFB97E8E587D73C8048","PreviousFields":{"Balance":{"currency":"CNY","issuer":"jjjjjjjjjjjjjjjjjjjjBZbvri","value":"-212439355.58"}},"PreviousTxnID":"EEA42ED2334F1B2CC838EE480FFE2E875EB09F156EF01055FEB06F8F910525AA","PreviousTxnLgrSeq":13115823}},{"ModifiedNode":{"FinalFields":{"Account":"jPisFRLYpCsfPhreWiTQC2LEHgMYXsH6zP","Balance":"900831602","Flags":0,"OwnerCount":4,"Sequence":1146},"LedgerEntryType":"AccountRoot","LedgerIndex":"AA7C068878F559D59A7B3C18A4CA6D369695186CCFA1108DC7A40457DCFAB681","PreviousFields":{"Balance":"900841602","Sequence":1145},"PreviousTxnID":"EEA42ED2334F1B2CC838EE480FFE2E875EB09F156EF01055FEB06F8F910525AA","PreviousTxnLgrSeq":13115823}},{"ModifiedNode":{"FinalFields":{"Balance":{"currency":"CNY","issuer":"jjjjjjjjjjjjjjjjjjjjBZbvri","value":"30261.27447984994"},"Flags":1114112,"HighLimit":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0"},"HighNode":"0000000000001514","LowLimit":{"currency":"CNY","issuer":"jByHB6FCo1M22qSx8ppaHBeEoSw5qbBVTb","value":"10000000000"},"LowNode":"0000000000000000"},"LedgerEntryType":"SkywellState","LedgerIndex":"C7874B8620A77FE43F2E818177389990185399B2826D431DD8FC9651D10997C7","PreviousFields":{"Balance":{"currency":"CNY","issuer":"jjjjjjjjjjjjjjjjjjjjBZbvri","value":"20261.27447984994"}},"PreviousTxnID":"EEA42ED2334F1B2CC838EE480FFE2E875EB09F156EF01055FEB06F8F910525AA","PreviousTxnLgrSeq":13115823}}],"TransactionIndex":3,"TransactionResult":"tesSUCCESS"},"validated":true},"msg":"获取交易详情成功","isActive":true}

```




