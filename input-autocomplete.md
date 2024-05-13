# input autocomplete

常用 input autocomplete 属性（ [MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Attributes/autocomplete#%E5%80%BC) ）。



## on / off

- `on` : 浏览器自动完成输入，浏览器自己判断期望的数据类型。
- `off` : 禁止浏览器自动完成输入或选择一个值。



## name

- `honorific-prefix` : 前缀或头衔，e.g. `Mrs.` 、`Mr.` 、`Miss` 、`Ms.` 、`Dr.` 或 `Mlle.` 。
- `honorific-suffix` : 后缀，e.g. `Jr.` 、`B.Sc.` 、`PhD.` 、`MBASW` 或 `IV` 。
- `given-name` : 名字。
- `additional-name` : 中间名。
- `family-name` : 姓氏。
- `nickname` : 昵称或绰号。



## email

`email` : 电子邮件地址。



## login

### username

`username` : 用户名 / 账户名。



### password 

- `new-password` : 新密码。
- `current-password` ：用户的当前密码。
- `one-time-code` : 验证用户身份的一次性代码。



## address

### country

- `country` : 一个国家或地区的代码。
- `country-name` : 一个国家或地区的名字。



### address

- `address-level1` : 地址中的第一个地址的行政级别，通常是地址所在的省份。在美国，是 state 。在瑞士，是 canton 。在英国，是 post town。
- `address-level2` : 在具有至少有两个行政级别的地址中，第二个地址的行政级别。在具有两个行政级别的国家/地区中，通常是地址所在的城市，城镇，村庄或其他地区。
- `address-level3` : 在具有至少三个行政级别的地址中，第三个地址的行政级别。
- `address-level4` : 在具有四个行政级别的地址中，粒度最细的地址的行政级别。
- `street-address` : 街道地址。它可以是多行文本，应在第二个行政级别（通常是城市或城镇）内完全标识地址的位置，但不应包括城市名称，邮政编码或邮政编码或国家/地区名称。
- `address-line1` 、`address-line2` 、`address-line2` : 街道地址的每一行。仅在 `street-address` 不存在的情况下，才应提供这些内容。



### 邮政编码

`postal-code` : 邮政编码（在美国，是 ZIP 编码）。



## credit card

### cardholder

- `cc-name` : 打印在付款工具（例如信用卡）上或与之关联的全名。通常，使用全名字段比将名称分成多个部分更可取。
- `cc-given-name` : 在信用卡等支付工具上的名字。
- `cc-additional-name` : 信用卡等支付工具上的中间名。
- `cc-family-name` : 信用卡上的姓氏。



### card number

`cc-number` : 信用卡号码或其他标识付款方式的号码，例如账号。



### expiration

- `cc-exp` : 付款方式的到期日期，通常为“月份 / 两位年份”或“月份 / 四位年份”形式。
- `cc-exp-month` : 付款方式到期的月份。
- `cc-exp-year` : 付款方式到期的年份。



### cvv

`cc-csc` : 付款工具的安全码；在信用卡上，这是信用卡背面的 3 位数验证码。