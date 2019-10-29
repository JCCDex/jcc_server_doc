## 目录
[接口定义](#接口定义)

　　[1 获取服务器列表](#1-获取服务器列表)

　　[2 获取钱包余额](#2-获取钱包余额)
  
　　[3 查询历史交易记录](#3-查询历史交易记录)
  
　　[4 查询历史转账记录](#4-查询历史转账记录)
  
　　[5 查询当前委托](#5-查询当前委托)

　　[6 获取交易序列号(前端签名需要)](#6-获取交易序列号前端签名需要)
  
　　[7 挂单](#7-挂单)

　　[8 撤单](#8-撤单)

　　[9 转账](#9-转账)

　　[10 获取市场深度](#10-获取市场深度)

　　[11 获取K线数据](#11-获取K线数据)

　　[12 获取最新成交](#12-获取最新成交)

　　[13 获取24小时行情数据](#13-获取24小时行情数据)

　　[14 获取交易详情](#14-获取交易详情)

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

GET https://explorer.jccdex.cn/wallet/trans/:uuid?p=0&s=100&t=OfferCreate,OfferCancel,OfferAffect&w=

查询下一页:

GET https://explorer.jccdex.cn/wallet/trans/:uuid?p=1&s=100&t=OfferCreate,OfferCancel,OfferAffect&w=

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
p|Number|是|页数，从0开始
s|Number|否|每页显示条数，10/20/50/100四种选择，缺省20
t|String|否|交易类型（多个类型以逗号分隔，可以不传值，不传值表示查询所有类型，历史交易用到的类型有：OfferCreate 创建委托、OfferAffect 被动成交、OfferCancel 撤消委托）
w|String|是|钱包地址（必须传值）

返回参数:

参数名|参数类型|描述
--|:--:|--:
type|String|交易类型，字符串（OfferCreate：创建委托；OfferAffect：被动成交；OfferCancel：撤消委托）
time|Number|交易发生时间，整型（可以换算成日期时间字符串new Date((time的值+946684800)*1000)）
past|Number|该交易距离查询时过去的秒数，整型
hash|String|该交易的哈希，64位字符串
block|Number|区块高度，整型
fee|String|交易gas费用，数字类型的字符串（OfferAffect和Receive时，fee=““）
success|String|交易是否成功（tesSUCCESS表示成功）
seq|Number|交易序号，整型（对于OfferAffect和Receive，该字符无意义）

返回示例

```json
{"code":"0","data":{"list":[{"type":"OfferCancel","time":621523070,"hash":"9F1D72707EFBA863B8DAD487857E4D7B4E54E90CF6348BBEA5F32509C4390DE4","block":13836328,"fee":0.00001,"success":"tesSUCCESS","seq":37,"offerSeq":36,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.0414"},"past":2310561,"flag":2},{"type":"OfferCreate","time":621523030,"hash":"310AC09A482FBC6D2BD6322C1BD5219D9CE782ABA93FDBFEE9D3851F5386AC6F","block":13836324,"fee":0.00001,"success":"tesSUCCESS","seq":36,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.0414"},"takerGetsFact":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysFact":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.0414"},"past":2310601,"flag":2},{"type":"OfferCancel","time":621520840,"hash":"A23A9FB001A5C4008C5739942037777031F51CADF3DCC93FFC491F067D49B996","block":13836105,"fee":0.00001,"success":"tesSUCCESS","seq":35,"offerSeq":11,"takerGets":{"currency":"SWTC","issuer":"","value":"66.66"},"takerPays":{"currency":"JJCC","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"6666"},"past":2312791,"flag":1},{"type":"OfferAffect","time":621311160,"hash":"A64C561C2EE1D78E8E48C59B21A585B915B2F7CADBFB21F979C0326696603ABB","block":13815137,"fee":"","success":"tesSUCCESS","seq":34,"account":"jMWE1CMpnD5rzgrXfkmjRRLRt3kt5GVdSK","offerSeq":34,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsMatch":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysMatch":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"past":2522471,"flag":2},{"type":"OfferCreate","time":621310200,"hash":"7EC01CEDD939FFE79BF4177942DD1F7F9CC03224B766B9F7C0C98332D27F2F64","block":13815041,"fee":0.00001,"success":"tesSUCCESS","seq":34,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsFact":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysFact":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"past":2523431,"flag":2},{"type":"OfferAffect","time":621309510,"hash":"26CBE76209B939DFAAD8BB451928B1101A43377AD7ED9B2DD2B76C7DC3039E84","block":13814972,"fee":"","success":"tesSUCCESS","seq":33,"account":"jKbQJvFW1xhi8RdfvYt7VZcpmr2X9x2puE","offerSeq":33,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsMatch":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysMatch":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"past":2524121,"flag":2},{"type":"OfferCreate","time":621308630,"hash":"B33872232568553D498F3D1B37F26E31708F443EC485EBAD77FAE64397307B43","block":13814884,"fee":0.00001,"success":"tesSUCCESS","seq":33,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsFact":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysFact":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"past":2525001,"flag":2},{"type":"OfferAffect","time":621137450,"hash":"C95EA6945D0D91ADF1CADD0730A1BECA3BA452033F9AED15CF3EBC65303ACF4F","block":13797766,"fee":"","success":"tesSUCCESS","seq":32,"account":"jJGpN9BdFHejqFkkb3vu69W1B4gfEq1YhM","offerSeq":32,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsMatch":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysMatch":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"matchFlag":3,"past":2696181,"flag":2},{"type":"OfferCreate","time":621137370,"hash":"15C905004B68FB2763E2DB70A78C1F9475EC8EA5EB34A4DA9CCE5F87E832E535","block":13797758,"fee":0.00001,"success":"tesSUCCESS","seq":32,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"takerGetsFact":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysFact":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00439"},"past":2696261,"flag":2},{"type":"OfferAffect","time":620822530,"hash":"B4002942484153FD12643B68D5D3B83DDC33BC1A038AC3A9F28C8ECCCCDFACBD","block":13766274,"fee":"","success":"tesSUCCESS","seq":27,"account":"jMSvQ6n5oAR88MMgF93C3sU819UVw7VpDQ","offerSeq":27,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00466000000000001"},"takerGetsMatch":{"currency":"SWTC","issuer":"","value":"1"},"takerPaysMatch":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.00466000000000001"},"past":3011101,"flag":2}],"count":49},"msg":"成功"}

```

### 4 查询历史转账记录

HTTP请求

查询第一页:

GET https://explorer.jccdex.cn/wallet/trans/:uuid?p=0&s=10&t=Receive,Send&w=

查询下一页:

GET https://explorer.jccdex.cn/wallet/trans/:uuid?p=1&s=10&t=Receive,Send&w=

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
p|Number|是|页数，从0开始
s|Number|否|每页显示条数，10/20/50/100四种选择，缺省20
t|String|否|交易类型（多个类型以逗号分隔，可以不传值，不传值表示查询所有类型，转账用到的类型有：Send(支付)、Receive(收到)）
w|String|是|钱包地址（必须传值）

返回参数:

参数名|参数类型|描述
--|:--:|--:
type|String|交易类型，转账为（Send：支付；Receive：收到）
time|Number|交易发生时间，整型（可以换算成日期时间字符串new Date((time的值+946684800)*1000))
past|Number|该交易距离查询时过去的秒数，整型
hash|String|该交易的哈希，64位字符串
block|Number|区块高度，整型
fee|String|交易gas费用，数字类型的字符串（OfferAffect和Receive时，fee=““）
success|String|交易是否成功（tesSUCCESS表示成功）
seq|Number|交易序号，整型（对于OfferAffect和Receive，该字符无意义）

返回示例

```json
{"code":"0","data":{"list":[{"type":"Receive","time":623152620,"hash":"445821D60F9294E4ACF911D95166320D2F8EFFC6B4C6C133701B53272684D166","block":13999231,"fee":"","success":"tesSUCCESS","seq":97,"account":"jHEnnKkRcws7sm7kt4hLyKfmhkx6U58Gn","amount":{"currency":"JJCC","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"2835"},"memos":[{"Memo":{"MemoData":"E8BF90E7BBB4E5B7A5E4BD9CE694AFE68C81"}}],"past":683257},{"type":"Receive","time":620999690,"hash":"3B5140997969496CCFD119E545C7B6B885D3EB8D20CCB5D66FD84FB1DF50E8AD","block":13783990,"fee":"","success":"tesSUCCESS","seq":76,"account":"jHEnnKkRcws7sm7kt4hLyKfmhkx6U58Gn","amount":{"currency":"SWTC","issuer":"","value":"3091"},"memos":[{"Memo":{"MemoData":"E5BC80E6BA90E9A1B9E79BAEE694B6E585A5E58886E9858D33303931"}}],"past":2836187},{"type":"Receive","time":617358920,"hash":"A9A751A33F1E664FC902B8BE6790590E2A06625312A665EEDEEBCA8B08C540B7","block":13419913,"fee":"","success":"tesSUCCESS","seq":728,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"200"},"memos":[{"Memo":{"MemoData":"323030","MemoType":"737472696E67"}}],"past":6476957},{"type":"Receive","time":616312710,"hash":"3C13CEE11C9149D821B62410987C35CAE388636DB52029A8326ADADACD0BE223","block":13315292,"fee":"","success":"tesSUCCESS","seq":724,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"3"},"memos":[{"Memo":{"MemoData":"32","MemoType":"737472696E67"}}],"past":7523167},{"type":"Receive","time":616303500,"hash":"5126DE53C550C3014BCB50FD9BA5633961CDE5D1ACE701C7E907C04E7D649649","block":13314371,"fee":"","success":"tesSUCCESS","seq":723,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"2"},"memos":[{"Memo":{"MemoData":"32","MemoType":"737472696E67"}}],"past":7532377},{"type":"Send","time":615120220,"hash":"0632034CC6F3B77C481D010427409CF7F253A88B40649668EB767EE6BE167986","block":13196043,"fee":0.00001,"success":"tesSUCCESS","seq":9,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"0.1"},"past":8715657},{"type":"Send","time":615118750,"hash":"30F512064CC3972EE90043CF3667AB64413710BC383EBD89F808F18AA6F2B839","block":13195896,"fee":0.00001,"success":"tesSUCCESS","seq":8,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"JJCC","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"1"},"past":8717127},{"type":"Receive","time":615118490,"hash":"0118BF3027EAFAC2102D9EB11AC9D8CE6E1C43235317294411B0B4245A71AB63","block":13195870,"fee":"","success":"tesSUCCESS","seq":691,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"JJCC","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"1"},"past":8717387},{"type":"Send","time":613486000,"hash":"4DD53AF5BFB511771AE821C8FA1A56BA4B5810A27348EF0F7FA962D54EE023C7","block":13032621,"fee":0.00001,"success":"tesSUCCESS","seq":2,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"0.00001"},"past":10349877},{"type":"Receive","time":611997270,"hash":"8F9B6C706BE43FE5F8CE9DD5846324C2C21ADCBC6CB4C211C50D74EA82998946","block":12883748,"fee":"","success":"tesSUCCESS","seq":545,"account":"jngGKZEvkKYbeXqajh4A9gbZvSuXonV2nc","amount":{"currency":"SWTC","issuer":"","value":"2"},"past":11838607}],"count":12},"msg":"成功"}

```

### 5 查询当前委托

HTTP请求

查询第一页:

GET https://explorer.jccdex.cn/wallet/offer/:uuid?p=0&s=100&w=

请求参数:

参数名|参数类型|是否必须|描述
--|:--:|--:|--:
p|Number|是|页数，从0开始
s|Number|否|每页显示条数，可以不传值，10/20/50/100四种选择，缺省20
w|String|是|钱包地址（必须传值）

返回参数:

参数名|参数类型|描述
--|:--:|--:
time|Number|委托单的挂单时间（所属区块的时间,换算当前时间new Date((time的值+946684800)*1000)），整型
past|Number|距离现在过去的秒数，整型
hash|String|委托单的挂单哈希值，64位字符串
block|Number|区块高度，整型
flag|Number|委托单性质，买/卖，整型（1:买；2:卖；0:未知）
takerGets|JSON|挂单付出的币种和数量，json对象
takerPays|JSON|挂单得到的币种和数量，json对象

返回示例

```json
{"code":"0","data":{"list":[{"time":623149880,"hash":"6E3D45E77EB20D73439D10958AF28A648DC49302FEE1212BB4D5E010624F554D","block":13998957,"seq":44,"takerGets":{"currency":"SWTC","issuer":"","value":"111"},"takerPays":{"currency":"JPG","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"111"},"past":686208,"flag":1},{"time":622977270,"hash":"A784A26BCBC7FA799D3389D3574E18E4E62F8907CC335A9797BD45003B187D2B","block":13981696,"seq":43,"takerGets":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.001"},"takerPays":{"currency":"SWTC","issuer":"","value":"1"},"past":858818,"flag":1},{"time":622976990,"hash":"9ED4D308566DFA81068A8ECAB7BB146ABD9529A8A0C6FD2D10A7ADBBFA8F373B","block":13981668,"seq":42,"takerGets":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.001"},"takerPays":{"currency":"SWTC","issuer":"","value":"1"},"past":859098,"flag":1},{"time":621917920,"hash":"73D82F74D9B4F0AAFF209BE60ABA750774206BDDE08FADCA5772C21437FD69AB","block":13875761,"seq":40,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"CNY","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"10000"},"past":1918168,"flag":2},{"time":621574520,"hash":"3B1CA23CD1B13E3591C8FFFA2EF6D732DD203DCBCB0EE7ADC4B1B0E020C367E6","block":13841421,"seq":39,"takerGets":{"currency":"SWTC","issuer":"","value":"1"},"takerPays":{"currency":"JCADT","issuer":"jGa9J9TkqtBcUoHe2zqhVFFbgUVED6o9or","value":"0.1"},"past":2261568,"flag":2}],"count":5},"msg":"成功"}

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

### 7 挂单

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

### 8 撤单

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

### 9 转账

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

### 10 获取市场深度

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

### 11 获取K线数据

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

### 12 获取最新成交

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

### 13 获取24小时行情数据

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

### 14 获取交易详情

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




