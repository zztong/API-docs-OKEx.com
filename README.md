# 入门指引    

## API概述    

OKEx为用户提供了一整套简单而又强大的开发工具，旨在帮助用户快速、高效地将OKEx交易功能整合到自己的应用当中。    

OKEx接口是提供服务的基础，开发者在OKEx网站创建账号后，可以根据自身需求建立不同权限的API，并利用API进行自动交易或者提现。   

通过API可以快速实现以下功能：    
- 获取市场最新行情    
- 获取买卖深度信息    
- 查询可用和冻结金额    
- 查询自己当前尚未成交的挂单    
- 快速买进卖出    
- 批量撤单    
- 快速提现到您的认证地址    

获取接口权限后，可以通过阅读本接口文档来帮助开发。    
    
## 接口调用方式说明    

OKEx为用户提供了三种调用接口的方式，开发者可根据自己的使用场景和偏好选择适合自己的方式来查询行情、进行交易或提现。    

#### REST API    

REST，即Representational State Transfer的缩写，是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，正得到越来越多网站的采用。其优点如下：    
- 在RESTful架构中，每一个URL代表一种资源；    
- 客户端和服务器之间，传递这种资源的某种表现层；    
- 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。    

建议开发者使用REST API进行币币交易或者资产提现等操作。 

#### WebSocket API    

WebSocket是HTML5一种新的协议(Protocol)。它实现了客户端与服务器全双工通信，使得数据可以快速地双向传播。通过一次简单的握手就可以建立客户端和服务器连接，服务器根据业务规则可以主动推送信息给客户端。其优点如下：    
- 客户端和服务器进行数据传输时，请求头信息比较小，大概2个字节;    
- 客户端和服务器皆可以主动地发送数据给对方；    
- 不需要多次创建TCP请求和销毁，节约宽带和服务器的资源。    

强烈建议开发者使用WebSocket API获取市场行情和买卖深度等信息。    

## 开启API权限    

用户的API权限在网站的基本设置->我的API内获取。点击申请API即可获得，其中apiKey是OKEx提供给API用户的访问密钥，secretKey用于对请求参数签名的私钥。    

**_注意： 请勿向任何人泄露这两个参数，这两个参数关乎您账号的安全。_**    
     
## 参数签名    

用户提交的参数除sign外，都要参与签名。    

首先，将待签名字符串要求按照参数名进行排序(首先比较所有参数名的第一个字母，按abcd顺序排列，若遇到相同首字母，则看第二个字母，以此类推)。   

例如：对于如下的参数进行签名   

	string[] parameters={"api_key=c821db84-6fbd-11e4-a9e3-c86000d26d7c","symbol=btc_cny","type=buy","price=680","amount=1.0"};     

生成待签名的字符串    

	amount=1.0&api_key=c821db84-6fbd-11e4-a9e3-c86000d26d7c&price=680&symbol=btc_cny&type=buy    

然后，将待签名字符串添加私钥参数生成最终待签名字符串。例如：

	amount=1.0&api_key=c821db84-6fbd-11e4-a9e3-c86000d26d7c&price=680&symbol=btc_cny&type=buy&secret_key=secretKey    

注意，`&secret\_key=secretKey` 为签名必传参数。   

最后，是利用32位MD5算法，对最终待签名字符串进行签名运算，从而得到签名结果字符串(该字符串赋值于参数sign)，MD5计算结果中字母全部大写。    

## 更新日志    

2017年11月06日更新    
- 比特币现金简写BCC变更为BCH。    

2017年10月27日更新    
- API支持了ETH、ETC新合约交易。    

2017年10月12日更新    
- API支持了USDT币币交易。    

2017年9月21日更新    
1. 优化了REST API提币、取消提币、查询提币等接口：从合约交易迁移至币币交易API；    
2. 提币、取消提币、查询提币等接口支持了ETH、ETC、BCC。    

2017年8月30日更新    
- 优化了REST API接口返回码的部分内容，新增了部分接口返回码。    
    
# REST API    
    
## 开始使用    

REST，即Representational State Transfer的缩写，是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，正得到越来越多网站的采用。其优点如下：    
- 在RESTful架构中，每一个URL代表一种资源；    
- 客户端和服务器之间，传递这种资源的某种表现层；    
- 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。   

建议开发者使用REST API进行币币交易或者资产提现等操作。    
    
## 请求交互    

REST访问的根URL：`https://www.okex.com/api/v1`     

所有请求基于Https协议，请求头信息中contentType需要统一设置为：`application/x-www-form-urlencoded`    
	
请求交互说明    
1. 请求参数：根据接口请求参数规定进行参数封装。    
2. 提交请求参数：将封装好的请求参数通过POST 或GET 方式提交至服务器。    
3. 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。    
4. 数据处理：对服务器响应数据进行处理。    
	
	
错误代码（币币）    
| 错误代码        | 详细描述    |    
| :-----    | :-----   |    
|10000	|	必选参数不能为空|    
|10001	|	用户请求频率过快，超过该接口允许的限额|    
|10002	|	系统错误|    
|10004	|	请求失败|    
|10005	|	SecretKey不存在|    
|10006	|	Api\_key不存在|    
|10007	|	签名不匹配|    
|10008	|	非法参数|    
|10009	|	订单不存在|    
|10010	|	余额不足|    
|10011	|	买卖的数量小于BTC/LTC最小买卖额度|    
|10012	|	当前网站暂时只支持btc\_usd ltc\_usd|    
|10013	|	此接口只支持https请求|    
|10014	|	下单价格不得≤0或≥|1000000|    
|10015	|	下单价格与最新成交价偏差过大|    
|10016	|	币数量不足|    
|10017	|	API鉴权失败|    
|10018	|	借入不能小于最低限额\[USD:100,BTC:0.1,LTC:1] |    
|10019	|	页面没有同意借贷协议|    
|10020	|	费率不能大于1%|    
|10021	|	费率不能小于0.01%|    
|10023	|	获取最新成交价错误|    
|10024	|	可借金额不足|    
|10025	|	额度已满，暂时无法借款|    
|10026	|	借款(含预约借款)及保证金部分不能提出|    
|10027	|	修改敏感提币验证信息，24小时内不允许提现|    
|10028	|	提币金额已超过今日提币限额|    
|10029	|	账户有借款，请撤消借款或者还清借款后提币|    
|10031	|	存在BTC/LTC充值，该部分等值金额需6个网络确认后方能提出|    
|10032	|	未绑定手机或谷歌验证|    
|10033	|	服务费大于最大网络手续费|    
|10034	|	服务费小于最低网络手续费|    
|10035	|	可用BTC/LTC不足|    
|10036	|	提币数量小于最小提币数量|    
|10037	|	交易密码未设置|    
|10040	|	取消提币失败|    
|10041	|	提币地址不存在或未认证|    
|10042	|	交易密码错误|    
|10043	|	合约权益错误，提币失败|    
|10044	|	取消借款失败|    
|10047	|	当前为子账户，此功能未开放|    
|10048	|	提币信息不存在|    
|10049	|	小额委托（<0.15BTC)的未成交委托数量不得大于50个|    
|10050	|	重复撤单|    
|10052	|	提币受限|    
|10064	|	美元充值后的48小时内，该部分资产不能提出|    
|10100	|	账户被冻结|    
|10101	|	订单类型错误|    
|10102	|	不是本用户的订单|    
|10103	|	私密订单密钥错误|    
|10216	|	非开放API|    
|1002	|	交易金额大于余额|    
|1003	|	交易金额小于最小交易值|    
|1004	|	交易金额小于0|    
|1007	|	没有交易市场信息|    
|1008	|	没有最新行情信息|    
|1009	|	没有订单|    
|1010	|	撤销订单与原订单用户不一致|    
|1011	|	没有查询到该用户|    
|1013	|	没有订单类型|    
|1014	|	没有登录|    
|1015	|	没有获取到行情深度信息|    
|1017	|	日期参数错误|    
|1018	|	下单失败|    
|1019	|	撤销订单失败|    
|1024	|	币种不存在|    
|1025	|	没有K线类型|    
|1026	|	没有基准币数量|    
|1027	|	参数不合法可能超出限制|    
|1028	|	保留小数位失败|    
|1029	|	正在准备中|    
|1030	|	有融资融币无法进行交易|    
|1031	|	转账余额不足|    
|1032	|	该币种不能转账|    
|1035	|	密码不合法|    
|1036	|	谷歌验证码不合法|    
|1037	|	谷歌验证码不正确|    
|1038	|	谷歌验证码重复使用|    
|1039	|	短信验证码输错限制|    
|1040	|	短信验证码不合法|    
|1041	|	短信验证码不正确|    
|1042	|	谷歌验证码输错限制|    
|1043	|	登陆密码不允许与交易密码一致|    
|1044	|	原密码错误|    
|1045	|	未设置二次验证|    
|1046	|	原密码未输入|    
|1048	|	用户被冻结|    
|1050	|	订单已撤销或者撤单中|    
|1051	|	订单已完成交易|    
|1201	|	账号零时删除|    
|1202	|	账号不存在|    
|1203	|	转账金额大于余额|    
|1204	|	不同种币种不能转账|    
|1205	|	账号不存在主从关系|    
|1206	|	提现用户被冻结|    
|1207	|	不支持转账|    
|1208	|	没有该转账用户|    
|1209	|	当前api不可用|    
|1216	|	市价交易暂停，请选择限价交易|    
|1217	|	您的委托价格超过最新成交价的±5%，存在风险，请重新下单|    
|1218	|	下单失败，请稍后再试|    
|HTTP错误码403	|	用户请求过快，IP被屏蔽|    
|Ping不通	|	用户请求过快，IP被屏蔽|    
