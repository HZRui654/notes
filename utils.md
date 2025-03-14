# 各种需求对应工具



## Log

**制定日志输出规范**

1. **日志等级**

   根据消息的重要性分类，由低到高：

   - `DEBUG` ：详细的开发时信息，用于调试应用。
   - `INFO` ：重要事件的简要信息，如系统启动、配置等。
   - `WARN` ：系统能正常运行，但有潜在错误的情况。
   - `ERROR` ：由于严重的问题，某些功能无法正常运行。
   - `FATAL` ：非常严重的问题，可能导致系统崩溃。

2. **日志内容**

   应该包含足够的信息，以便于开发者理解发生了什么：

   - `时间戳` ：精确到毫秒的事件发生时间。
   - `日志等级` ：当前日志消息的等级。
   - `消息描述` ：描述事件的详细信息。
   - `错误堆栈` ：如果是错误，提供错误堆栈信息。

3. **日志格式**

   ``` bash
   [时间戳] [日志等级] [消息内容] [错误堆栈]
   ```

   ``` bash
   [2025-01-017T12:00:00.000Z] [ERROR] Failed to load user data. {stack}
   ```

4. **日志输出**

   使用 `console` ：

   - `console.debug` ：用于 `DEBUG` 级别。
   - `console.info` ：用于 `INFO` 级别。
   - `console.warn` ：用于 `WARN` 级别。
   - `console.error` ：用于 `ERROR` / `FATAL` 级别。

5. **封装**

   ``` typescript
   class Logger {
      static level = 'DEBUG' // 默认为DEBUG级别
   
     static setLevel(newLevel) {
       this.level = newLevel
     }
     
     static shouldLog(level) {
       const levels = ['DEBUG', 'INFO', 'WARN', 'ERROR', 'FATAL']
       return levels.indexOf(level) >= levels.indexOf(this.level)
     }
     
     static log(level, message, error) {
       if (!this.shouldLog(level)) {
         return
       }
       
       const timestamp = new Date().toISOString()
       const stack = error ? error.stack : ''
       const formattedMessage = `[${timestamp}] [${level}] ${message} ${stack}`
   
       switch (level) {
         case 'DEBUG':
           console.debug(formattedMessage)
           break
         case 'INFO':
           console.info(formattedMessage)
           break
         case 'WARN':
           console.warn(formattedMessage)
           break
         case 'ERROR':
         case 'FATAL':
           console.error(formattedMessage)
           break
         default:
           console.log(formattedMessage)
       }
     }
   
     static debug(message) {
       this.log('DEBUG', message)
     }
   
     static info(message) {
       this.log('INFO', message)
     }
   
     static warn(message) {
       this.log('WARN', message)
     }
   
     static error(message, error) {
       this.log('ERROR', message, error)
     }
   
     static fatal(message, error) {
       this.log('FATAL', message, error)
     }
   }
   
   // 生产环境中设置日志等级
   if (process.env.NODE_ENV === 'production') {
     Logger.setLevel('WARN')
   }
   
   // 使用示例
   Logger.info('Application is starting...')
   Logger.error('Failed to load user data', new Error('Network Error'))
   
   ```



## 日期计算

[datejs](https://dayjs.fenxianglu.cn/)

### 标准化时间

目前最常用的标准化时间系统是**协调世界时**（Coordinated Universal Time，简称 UTC）。UTC 是基于原子钟的国际标准时间。

计算机中的时间采用 ISO 日期格式，它是 ISO-8601 扩展格式的简化版本。

``` javascript
/** ISO
 * e.g. 2018-07-31 12:15:00.233 +01:00
 * 2018 -> year
 * 07 -> montg
 * 31 -> day
 * 12 -> hour
 * 15 -> minutes
 * 00 -> seconds
 * 233 -> milliseconds
 * +01:00 -> timezone offset
 */

```



### 使用原生 Date 操作

- **Date 对象**

  创建一个新Date对象的唯一方法是通过 `new` 操作符，例如：`let now = new Date();` 若将它作为常规函数调用（即不加 `new` 操作符），将返回一个字符串，而非 `Date` 对象。

  ``` javascript
  new Date()
  new Date(value)
  new Date(dateString)
  new Date(year, monthIndex [, day [, hours [, minutes [, seconds [, milliseconds]]]]])
  
  ```

- **获取当前时间**

  如果不向 Date 构造函数传递任何内容，则返回的日期对象就是当前的日期和时间。然后，就可以将其格式化为仅提取日期部分。

  需要注意，**月份是从 0 开始的**，一月就是 0，依此类推。

  ``` javascript
  const currentDate = new Date()
  
  const currentDayOfMonth = currentDate.getDate()
  const currentMonth = currentDate.getMonth()
  const currentYear = currentDate.getFullYear()
  
  const dateString = currentDayOfMonth + '-' + (currentMonth + 1) + '-' + currentYear; // '4-7-2023'
  
  // 方法名 +UTC 可获取 UTC 时间
  const UTCYear = currentDate.getUTCFullYear() // 返回 UTC 日期的 4 位数年
  
  ```

- **获取当前时间戳**

  可以创建一个新的 Date 对象并使用 `getTime()` 方法来获取当前时间戳。

  在 JS 中，时间戳是**自 1970 年 1 月 1 日以来经过的毫秒数**。如果不需要支持 `<IE8` ，可以使用 `Date.now()` 直接获取时间戳，而无需创建新的 Date 对象。

  ``` javascript
  const currentDate = new Date()
  const timestamp = currentDate.getTime()
  const timestamp2 = Date.now()
  
  ```

- **获取本地时区**

  ``` javascript
  const offset = -(getTimezoneOffset() / 60) // -(-480 / 60) = 8
  const timezone = `UTC${offset >= 0 ? '+' : ''}${Math.abs(offset)}`
  
  function getTimezoneOffset(): number {
    const currentYear = new Date().getFullYear()
    // The timezone offset may change over time due to daylight saving time (DST) shifts.
    // 由于夏令时 (DST) 变化，时区偏移量可能会随着时间的推移而变化。
    // The non-DST timezone offset is used as the result timezone offset.
    // 非 DST 时区偏移量用作结果时区偏移量。
    // Since the DST season differs in the northern and the southern hemispheres,
    // both January and July timezones offsets are considered.
    // 由于北半球和南半球的 DST 季节不同，考虑一月和七月时区偏移。
    return Math.max(
      // `getTimezoneOffset` returns a number as a string in some unidentified cases
      parseFloat(new Date(currentYear, 0, 1).getTimezoneOffset()),
      parseFloat(new Date(currentYear, 6, 1).getTimezoneOffset()),
    )
  }
  
  ```

- **解析日期**

  可以通过不同的方式将字符串转换为 JS 日期对象。Date 对象的构造函数接受多种日期格式。

  字符串不需要包含星期几，因为 JS 可以确定任何日期是星期几。

  ``` javascript
  // 传入年、月、日、小时、分钟和秒作为单独的参数
  let date = new Date(2016, 6, 27, 13, 30, 0)
  
  // 使用 ISO 日期格式
  date = new Date('2016-07-27T07:45:00Z')
  
  ```

  但是如果不明确提供时区，就会有问题：

  ``` javascript
  // 这两个都会展示当地时间 2016 年 7 月 25 日 00:00:00，但是两者是不相等的。
  const date1 = new Date('25 July 2016')
  const date2 = new Date('July 25, 2016')
  date1 === date2;   // false
  
  // 使用 ISO 格式，即使只提供日期而不提供时间和时区，它也会自动接受时区为 UTC 。
  new Date('25 July 2016').getTime() !== new Date('2016-07-25').getTime()
  new Date('2016-07-25').getTime() === new Date('2016-07-25T00:00:00Z').getTime()
  
  ```

  如果向日期构造函数传递了正确的 ISO 日期格式 `YYYY-MM-DD` 的字符串，则它会自动采用 UTC 。

- **设置日期格式**

  现代 JS 在标准命名空间中内置了一些方便的国际化函数 `Intl`，使日期格式化变得简单。

  ``` javascript
  const firstValentineOfTheDecade = new Date(2020, 1, 14)
  
  // 只需将不同的区域性代码传递给 DateTimeFormat 构造函数即可。
  // 假设使用美国 (M/D/YYYY) 格式：
  const enUSFormatter = new Intl.DateTimeFormat('en-US')
  console.log(enUSFormatter.format(firstValentineOfTheDecade)) // 2/14/2020
  
  // 或者美国格式的较长形式，并拼写出月份名称
  const longEnUSFormatter = new Intl.DateTimeFormat('en-US', {
      year:  'numeric',
      month: 'long',
      day:   'numeric',
  })
  console.log(longEnUSFormatter.format(firstValentineOfTheDecade)) // February 14, 2020
  
  ```

- **更改日期格式**

  ``` javascript
  const myDate = new Date('Jul 21, 2013')
  const dayOfMonth = myDate.getDate()
  const month = myDate.getMonth()
  const year = myDate.getFullYear()
  
  function pad(n) {
      return n < 10 ? '0' + n : n
  }
  
  const ddmmyyyy = pad(dayOfMonth) + "-" + pad(month + 1) + "-" + year // "21-07-2013"
  
  ```

- **本地格式化日期**

  使用 `Date` 对象的 `toLocaleDateString()` 方法。

  ``` javascript
  let today = new Date().toLocaleDateString('en-GB', {  
      day:   'numeric',
      month: 'short',
      year:  'numeric',
  })
  console.log(today) // '4 Jul 2023'
  
  // 如果想显示日期的数字版本
  today = new Date().toLocaleDateString(undefined, {
      day:   'numeric',
      month: 'numeric',
      year:  'numeric',
  })
  console.log(today) //  7/26/2016
  
  // 想确保月份和日期是两位数
  today = new Date().toLocaleDateString(undefined, {
      day:   '2-digit',
      month: '2-digit',
      year:  'numeric',
  })
  console.log(today) //  07/26/2016
  
  ```

- **计算相对日期和时间**

  使用 Date 对象 `set` 相关的函数设置相对日期和时间。

  ``` javascript
  // setDate
  const myDate = new Date("July 20, 2016 15:00:00")
  const nextDayOfMonth = myDate.getDate() + 20
  // 原始的日期对象现在表示7月20日之后20天的日期
  myDate.setDate(nextDayOfMonth)
  const newDate = myDate.toLocaleString
  
  // setTime
  // 计算相对时间戳，并得到更精确的差异。
  const msSinceEpoch = (new Date()).getTime()
  // 距离现在17小时后的时间。
  const seventeenHoursLater = new Date(msSinceEpoch + 17 * 60 * 60 * 1000)
  
  ```

- **比较时间**

  比较时间时，首先需要创建日期对象，<、>、<= 和 >= 都可以工作。

  ``` javascript
  const date1 = new Date("July 19, 2014")
  const date2 = new Date("July 28, 2014")
  
  if(date1 > date2) {
      console.log(date1)
  } else {
      console.log(date2)
  }
  
  ```

  检查相等性比较棘手，因为表示同一日期的两个日期对象，仍然是两个不同的日期对象并且不相等。

  可以通过比较日期的**时间戳**来解决。

  ``` javascript
  const date1 = new Date("June 10, 2003")
  const date2 = new Date(date1)
  
  const equalOrNot = date1 == date2 ? '相等' : '不等'
  console.log(equalOrNot) // 不等
  
  // 比较时间戳
  date1.getTime() == date2.getTime()
  ```

  在创建日期对象时将忽略用户的时区并使用 UTC。有两种方法可以做到这一点：

  - 根据用户输入日期创建 ISO 格式的日期字符串，并使用它创建 Date 对象。 使用有效的 ISO 日期格式创建 Date 对象，同时明确指定使用的是 UTC 而不是本地时区。

    ``` javascript
    const userEnteredDate = '12/20/1989'
    const parts = userEnteredDate.split('/')
    
    const userEnteredDateISO = parts[2] + '-' + parts[0] + '-' + parts[1]
    
    const userEnteredDateObj = new Date(userEnteredDateISO + 'T00:00:00Z')
    const dateFromAPI = new Date("1989-12-20T00:00:00Z");
    
    const result = userEnteredDateObj.getTime() == dateFromAPI.getTime() // true
    
    ```

  - JS 提供了一个简洁的 `Date.UTC()` 函数，可以使用它来获取日期的 UTC 时间戳。从日期中提取组件并将它们传递给函数。

    ``` javascript
    const userEnteredDate = new Date('12/20/1989')
    const userEnteredDateTimeStamp = Date.UTC(userEnteredDate.getFullYear(), userEnteredDate.getMonth(), userEnteredDate.getDate(), 0, 0, 0)
    
    const dateFromAPI = new Date('1989-12-20T00:00:00Z')
    const result = userEnteredDateTimeStamp == dateFromAPI.getTime() // true
    
    ```




### 最佳实践

**从用户获取日期和时间**

为了消除任何混淆，建议使用 `new Date(year, month, day, hours, minutes, seconds, milliseconds)` 格式来创建日期，这是使用 `Date` 构造函数时能够做到的最明确的方式。

可以使用允许省略最后四个参数的变体，如果它们为零；例如，`new Date(2012, 10, 12)` 与 `new Date(2012, 10, 12, 0, 0, 0, 0)` 是相同的，因为未指定的参数默认为零。

注意，**尽量避免从字符串创建日期，除非它是 ISO 日期格式**。

``` javascript
const datePickerDate = '2012-10-12'
const timePickerTime = '12:30'

const [year, month, day] = datePickerDate.split('-').map(Number)
const [hours, minutes] = timePickerTime.split(':').map(Number)

const dateTime = new Date(year, month - 1, day, hours, minutes)

console.log(dateTime) // Fri Oct 12 2012 12:30:00 GMT+0800 (中国标准时间)

```



**仅获取日期**

如果只获取日期（例如用户的生日），最好将格式转换为有效的 ISO 日期格式，以消除任何可能导致日期在转换为 UTC 时向前或向后移动的时区信息。

``` javascript
const dateFromPicker = '12/20/2012'
const dateParts = dateFromPicker.split('/')
const ISODate = dateParts[2] + '-' + dateParts[0] + '-' + dateParts[1]
const birthDate = new Date(ISODate).toISOString() // 2012-20-12T00:00:00.000Z

```



**存储日期**

始终以 UTC 格式存储日期时间，始终将 ISO 日期字符串或时间戳保存到数据库。实践证明，在后端存储本地时间是一个坏主意，最好让浏览器在前端处理到本地时间的转换。

可以使用 Date 对象的 `toISOString()` 或 `toJSON()` 方法将本地时间转换为 UTC。

``` javascript
// local -> UTC
const dateFromUI = '12-13-2012'
const timeFromUI = '10:20'
const dateParts = dateFromUI.split('-')
const timeParts = timeFromUI.split(':')

const date = new Date(dateParts[2], dateParts[0]-1, dateParts[1], timeParts[0], timeParts[1]) // Thu Dec 13 2012 10:20:00 GMT+0800 (中国标准时间)

const dateISO = date.toISOString() // 2012-12-13T02:20:00.000Z

```



## 加解密

[crypto-js](https://github.com/brix/crypto-js)

**安装使用：**

```bash
npm install crypto-js --save

```

``` typescript
import CryptoJs from 'crypto-js'

```

- **MD5 加密**

  ```javascript
  CryptoJs.MD5('待加密字符串').toString()
  
  ```

- **SHA256 加密**

  ```javascript
  CryptoJs.SHA256('待加密字符串').toString()
  
  ```

- **base64 加解密**

  ```javascript
  // 加密
  CryptoJs.enc.Base64.stringify(CryptoJs.enc.Utf8.parse('待加密字符串'))
  
  // 解密
  CryptoJs.enc.base64.parse('待解密字符串').toString(CryptoJs.enc.Urf8)
  
  ```

- **AES**

  **简单加解密**

  ```javascript
  // 加密
  CryptoJs.AES.encrypt('待加密字符串', '密钥').toString()
  
  // 解密
  CryptoJs.AES.decrypt('待加密字符串', '密钥').toString(CryptoJs.enc.Utf8)
  
  ```

  **自定义 AES 加解密函数**

  - **AES 属于块加密**

    不难理解，对越长的字符串进行加密，代价越大，所以通常对明文进行分段，然后对每段明文进行加密，最后再拼成一个字符串。块加密的一个要面临的问题就是如何填满最后一块？所以这就是 PADDING 的作用，使用各种方式填满最后一块字符串，所以对于解密端，也需要用同样的 PADDING 来找到最后一块中的真实数据的长度。

  - **加密模式**

    AES 分为几种模式，比如 `ECB`，`CBC`，`CFB` 等等，这些模式除了 **ECB 由于没有使用 IV 而不太安全**，其他模式差别并没有太明显，大部分的区别在 IV 和 KEY 来计算密文的方法略有区别。具体可参考 [WIKI 的说明](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)。
    另外，AES 分为 `AES128`，`AES256` 等，表示期待秘钥的长度，比如 AES256 秘钥的长度应该是 256/8 的 32 字节，一些语言的库会进行自动截取，让人以为任何长度的秘钥都可以，而这其实是有区别的。

  - **IV 的作用**

    IV 称为初始向量，不同的 IV 加密后的字符串是不同的，加密和解密需要相同的 IV，既然 IV 看起来和 key 一样，却还要多一个 IV 的目的，对于每个块来说，key 是不变的，但是**只有第一个块的 IV 是用户提供的，其他块 IV 都是自动生成**。
    IV 的长度为 **16** 字节。**超过或者不足，可能实现的库都会进行补齐或截断**。但是由于块的长度是16 字节，所以一般可以认为需要的 IV 是16 字节。

  - **PADDING**

    AES 块加密说过，**PADDING 是用来填充最后一块使得变成一整块**，所以对于加密解密两端需要使用同一的 PADDING 模式，大部分 PADDING 模式为 `PKCS5`, `PKCS7`, `NOPADDING`。

    > PKCS5, PKCS7 效果一样。

  - **加密解密端**

    所以，在设计 AES 加密的时候，
    \- 对于加密端，应该包括：加密秘钥长度，秘钥，IV值，加密模式，PADDING 方式。
    \- 对于解密端，应该包括：解密秘钥长度，秘钥，IV值，解密模式，PADDING 方式。

  >  **总结**
  >
  > 加密 IV 和解密 IV 不同的时候，并不影响解密是否成功，但是解密的结果有差别。
  >
  > 修改 padding，加密解密的 padding 换成 NoPadding，发现解密之后生成 utf8 字符串出错。
  >
  > 加密为 Pkcs7 和 ZeroPadding 时，加密后的字符串变化显著，这时解密用任何 padding 模式，都可以成功解密。
  >
  > AES 加密解密的秘钥有一对，一个是 IV 一个是 KEY ，并且他们的长度都有严格要求。
  >
  > Padding 的作用似乎不只是补齐最后，如果自己什么都对，但是加密失败，可以尝试不同 Padding。

  

  **实现**

  ``` javascript
  const key = CryptoJS.enc.Utf8.parse("秘钥");  //十六位十六进制数作为密钥
  const iv = CryptoJS.enc.Utf8.parse('偏移量');   //十六位十六进制数作为密钥偏移量
  
  //加密方法
  function Encrypt(word) {
   	return CryptoJs.AES.encrypt(word, key, {
      iv,
      mode: CryptoJs.mode.CBC,
      padding: CryptoJs.pad.Pkcs7
    }).toString()
  }
  
  //解密方法
  function Decrypt(word) {
    return CryptoJs.AES.decrypt(word, key, {
      iv,
      mode: CryptoJs.mode.CBC,
      padding: CryptoJs.pad.Pkcs7
    }).toString(CryptoJs.enc.Utf8)
  }
  
  ```



### 长文本分段加密解密

[EncryptLong](https://www.npmjs.com/package/encryptlong)

**基于 jsencrypt 扩展长文本分段加解密功能。**

**安装使用：**

```bash
npm install encryptlong -S

```

- **RSA 加密**

  对极大整数做因数分解的难度决定了 RSA 算法的可靠性。换言之，对一极大整数做因数分解愈困难，RSA 算法愈可靠。假如有人找到一种快速因数分解的算法的话，那么用 RSA 加密的信息的可靠性就肯定会极度下降。但找到这样的算法的可能性是非常小的。今天只有短的 RSA 钥匙才可能被强力方式解破。到2016年为止，世界上还没有任何可靠的攻击 RSA 算法的方式。只要其钥匙的长度足够长，用 RSA 加密的信息实际上是不能被解破的。

  ``` javascript
  import EncryptLong from 'EncryptLong'
  
  getRSAEncript (key, text) {
    let enc = new EncryptLong()
    enc.setPublicKey(key)
    return enc.encryptLong(text)
  }
  
  ```

  

## 复制剪切板

[copy-text-to-clipboard](https://www.npmjs.com/package/copy-text-to-clipboard)

**Copy text to the clipboard in modern browsers.**

``` bash
npm install copy-text-to-clipboard --save

```

``` typescript
import copy from 'copy-text-to-clipboard';

button.addEventListener('click', () => {
	copy('🦄🌈')
})

```



### 适配低版本浏览器

`navigator.clipboard` 兼容性不是很好，低版本浏览器不支持。

``` javascript
const copyText = (text: string) => {
  return new Promise(resolve => {
    if (navigator.clipboard?.writeText) {
      return resolve(navigator.clipboard.writeText(text))
    }
    // 创建输入框
    const textarea = document.createElement('textarea')
    document.body.appendChild(textarea)
    // 隐藏此输入框
    textarea.style.position = 'absolute'
    textarea.style.clip = 'rect(0 0 0 0)'
    // 赋值
    textarea.value = text
    // 选中
    textarea.select()
    // 复制
    document.execCommand('copy', true)
    textarea.remove()
    return resolve(true)
  })
}
```



## 二维码

[node-qrcode](https://www.npmjs.com/package/qrcode)

``` vue
<template>
	<canvas ref="qrCodeContainer"></canvas>
</template>

<script setup lang="ts">
import { ref, useTemplate, watch } from 'vue'
import { useMedia } from '@/common/hooks'
import QRCode from 'qrcode'

const qrCode = ref('your qr code')
const container = useTemplate<HTMLCanvasElement>('qrCodeContainer')
const isPc = useMedia() // Determine whether the screen is PC size
function createQrCode() {
  QRCode.toCanvas(container.value, qrCode.value, {
    width: isPc.value ? 224 : 240
  })
}
watch(container, (refEl) => refEl && createQrCode())
watch(isPc, () => createQrCode())
</script>

```



### 添加 logo

[qr-code-styling](https://www.npmjs.com/package/qr-code-styling#api-documentation)

**生成二维码并且添加 logo 图片。**

`Vue3` ：

``` vue
<template>
  <div class="hello">
    <h1>QR code styling for Vue</h1>
    <div ref="qrCodeRef"></div>
    <label>
      <input v-model="options.data" placeholder="Add data" />
      <select v-model="extension">
        <option value="svg">SVG</option>
        <option value="png">PNG</option>
        <option value="jpeg">JPEG</option>
        <option value="webp">WEBP</option>
      </select>
      <button @click="download">Download</button>
    </label>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref, useTemplateRef, onMounted, watch } from 'vue'
import QRCodeStyling from 'qr-code-styling'

import type {
  DrawType,
  TypeNumber,
  Mode,
  ErrorCorrectionLevel,
  DotType,
  CornerSquareType,
  CornerDotType,
  FileExtension
} from 'qr-code-styling'

const options = reactive({
  width: 220,
  height: 220,
  type: 'svg' as DrawType, // 或者 cavas ，但是 cavas 没有 svg 这么清晰
  data: 'http://qr-code-styling.com',
  // shape: 'square', // 或者 'circle' ，二维码形状，默认方型
  image: '/favicon.ico',
  // margin: 10, // type 为 cavas 时的边距
  // qrOptions: {
    // typeNumber: 0 as TypeNumber,
    // mode: 'Byte' as Mode,
    // errorCorrectionLevel: 'Q' as ErrorCorrectionLevel
  // },
  imageOptions: {
    // hideBackgroundDots: true, // 这个是默认值
    imageSize: 0.3, // 最好不要超过 0.4
    // margin: 20,
    // crossOrigin: 'anonymous' // 如果图片不是同源下的链接需要使用
  },
  dotsOptions: {
    color: '#41b583',
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 0,
    //   colorStops: [{ offset: 0, color: '#8688B2' }, { offset: 1, color: '#77779C' }]
    // },
    type: 'rounded' as DotType
  },
  // backgroundOptions: {
    // color: '#ffffff'
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 0,
    //   colorStops: [{ offset: 0, color: '#ededff' }, { offset: 1, color: '#e6e7ff' }]
    // },
  // },
  // cornersSquareOptions: {
    // color: '#35495E',
    // type: 'extra-rounded' as CornerSquareType
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 180,
    //   colorStops: [{ offset: 0, color: '#25456e' }, { offset: 1, color: '#4267b2' }]
    // },
  // },
  // cornersDotOptions: {
    // color: '#35495E',
    // type: 'dot' as CornerDotType
    // gradient: {
    //   type: 'linear', // 'radial'
    //   rotation: 180,
    //   colorStops: [{ offset: 0, color: '#00266e' }, { offset: 1, color: '#4060b3' }]
    // },
  // }
})
let qrCode: QRCodeStyling | null

const qrCodeContent = useTemplateRef<HTMLDivElement>('qrCodeRef')
onMounted(() => {
  if (!qrCodeContent.value) return

  qrCode = new QRCodeStyling(options)
  qrCode.append(qrCodeContent.value)
})

watch(
  () => options.data,
  () => {
    qrCode?.update(options)
  }
)

// For download
const extension = ref<FileExtension>('svg')
function download() {
  qrCode?.download({ extension: extension.value })
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  padding: 0;
  list-style-type: none;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>

```



## 条形码

[jsbarcode](https://www.npmjs.com/package/jsbarcode)

``` vue
<template>
	<svg
 		:id="BARCODE_ID"
    ref="barcodeImg"
  ></svg>
</template>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'
import JsBarcode from 'jsbarcode'

const barcode = ref('33292928100020000000001118960729964900286435')
const barcodeImg = ref<HTMLImageElement>()
const BARCODE_ID = 'boleto-barcode'

watchEffect(() => {
  if (barcodeImg.value && barcode.value) {
    JsBarcode(`#${BARCODE_ID}`, barcode.value, {
      format: 'ITF',
      width: 0.9,
      height: 83,
      fontSize: 13,
      margin: 5,
      fontOptions: 'lighter',
      textMargin: 0
    })
  }
})
</script>

```



## 页面浏览导航

[Driver.js](https://driverjs.com/)



## 拖拽

[@formkit/drag-and-drop](https://drag-and-drop.formkit.com/)



## 流程图

[LogicFlow](https://docs.logic-flow.cn/docs/#/zh/guide/start)



## 打字机效果

[Typed.js](https://www.npmjs.com/package/typed.js)



## 日历

[FullCalendar](https://fullcalendar.io/) ：日历 UI 和交互；

[lunar](https://www.npmjs.com/package/lunar-typescript) ：支持获取各类日历数据，例如农历、佛历等。



### 踩坑

- 没办法直接通过修改 `visibleRange` 的方式，直接将日历的时间范围重置到不包含日历 `current date` 的范围，会报错。

  ``` javascript
  // 日历的 current date 可以使用 `calendar.getDate()` 获取
  const currentDate = calendar.getDate()
  
  // 该范围需要包含 currentDate ，否则报错。
  calendar.setOption('visibleRange', {
    start: '2017-04-01',
    end: '2017-04-05'
  })
  
  // 使用 `calendar.destory` 卸载，再使用 `calendar.render` 重新渲染的方式也无法解决。
  
  ```

  **解决方案**：不使用手动设置 `setOption` 的方式去修改 `visibleRange` ，而是创建 `calendar` 的时候给 `visibleRange` 传入回调函数，函数会在 `current date` 改变时触发，这样就可以通过 `calendar.gotoDate()` 改变 `current date` 来修改 `visibleRange` 。

  ``` javascript
  const calendar = new Calendar(calendarEl, {
    initialView: 'multiMonth',
    visibleRange: function(currentDate) {
      // 当前需求：用户选中时间范围，然后日历展示该时间范围所涉及的所有月份
      // calendarMonthRange: 用户选中时间范围
      const { calendarMonthRange: range } = this
      const visibleRange = {
        start: range[0],
        end: range[1]
      }
      
      // 将日历开始日期设置成起始月份第一天
      // Keep year and month, change the date to '01'
      // e.g. 2024-01-26 => 2024-01-01
      let start = range[0].split('-')
      start[2] = '01'
      visibleRange.start = start.join('-')
  
      // 因为 calendar 的时间范围参数都是包含起始时间，不包含结束时间
      // 所以将日历结束日期设置成结束月份的下一个月份第一天
      // Change the time to next month's first date
      // e.g. 2024-03-03 => 2024-04-01
      let end = range[1].split('-').map((val) => Number(val))
      end[0] = end[1] === 12 ? end[0] + 1 : end[0] // If month is 12, year add 1
      end[1] = end[1] === 12 ? 1 : end[1] + 1 // Change to 1 if month is 12, else add 1
      end[2] = 1
      visibleRange.end = end
        .map((val, index) => (index > 0 ? String(val).padStart(2, '0') : String(val)))
        .join('-')
  
      return visibleRange
    }
  })
  
  // 需要修改日历时间范围时
  // 1. 修改 `calendarMonthRange`
  // 2. 通过 `calendar.gotoDate` 修改 calendar 的 `current date` 触发 `visibleRange` 回调函数重新计算日历范围
  calendar.gotoDate(this.calendarMonthRange[0])
  
  ```



## 富文本编辑器

- [jodit](https://www.npmjs.com/package/jodit)

  Jodit是一款使用纯TypeScript编写的（无需使用其他库），美观实用的所见即所得（WYSIWYG）开源富文本编辑器，支持中文，超强自定义。

- [TinyMCE](https://www.npmjs.com/package/@tinymce/tinymce-vue)

  TinyMCE是一个轻量级的基于浏览器的所见即所得编辑器，由JavaScript写成。它对IE6+和Firefox1.5+都有着非常良好的支持。

  功能齐全，界面美观，就是文档是英文的，对开发人员英文水平有一定要求。



## 图片预览

[viewerjs](https://www.npmjs.com/package/viewerjs)



## CSS Loading

[CSS Loaders](https://css-loaders.com/) ：600 多种单 element loaders



##  EventBus

[Mitt](https://www.npmjs.com/package/mitt)



## Validator

### PhoneNumber

[google-libphonenumber](https://www.npmjs.com/package/google-libphonenumber) ：格式化和验证手机号码。

``` javascript
import { PhoneNumberUtil } from 'google-libphonenumber'

/**
  * format phone
  * 格式化手机
  *
  * @param {string} phone backend returned
  * @returns formatted phone and country code
  */
formatPhone(phone) {
  const params = {
    phone: '',
    code: ''
  }
  if (!phone) return params
  try {
    const phoneUtil = PhoneNumberUtil.getInstance()
    const number = phoneUtil.parseAndKeepRawInput(phone)
    params.code = phoneUtil.getRegionCodeForNumber(number)?.toLowerCase() ?? ''
    params.phone = number.getNationalNumber() ? String(number.getNationalNumber()) : ''
  } catch (err) {
    // console.log(err)
  }
  return params
}

```



### Email

[email-validator](https://www.npmjs.com/package/email-validator) ：校验 Email 。

``` javascript
import * as EmailValidator from 'email-validator'

EmailValidator.validate('test@email.com')

```



## uuid

[uuid](https://www.npmjs.com/package/uuid)

简单的实现方式：

``` javascript
const UUIDGeneratorBrowser = () =>
  ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, (c) =>
    (
      c ^
      (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (c / 4)))
    ).toString(16)
  )

UUIDGeneratorBrowser() // '7982fcfe-5721-4632-bede-6000885be57d'
```



## 大屏适配方案

**三大常用方式：**

- **vw/vh**：按照设计稿尺寸，将 `px` 按比例计算转为 `vw/vh` 。

  **优点：**可以动态计算图表的宽高，字体等，灵活性较高，当屏幕比例跟 ui 稿不一致时，不会出现两边留白情况

  **缺点：**每个图表都需要单独做字体、间距、位移的适配，比较麻烦

  

- **scale**：目前**效果最好**的方案，整个页面按比例缩放。

  **优点：**代码量少，适配简单 、一次处理后不需要在各个图表中再去单独适配

  **缺点：**留白，有事件热区偏移

  **解决留白问题：**假设当前 width 缩放 scale = 0.8 铺满，height 缩放后有白边。

  解决思路就是设置 height 的高度，使其缩放后等于页面高度 clientHeight 。

  1. 假设 height 为 h ，可以得到公式：`h * scale = clientHeight` 
  2. 将 height 设置成 `h = clientHeight / scale`  再缩放即可

  如果是 width 留白也同理，使用 `clientWidth` 进行计算即可。

  ``` javascript
  function keepFit(designWidth, designHeight, renderDom) {
    let clientHeight = document.documentElement.clientHeight
    let clientWidth = document.documentElement.clientWidth
    let scale = 1
    
    if (clientWidth / clientHeight < designWidth / designHeight) {
      // 高度有白边
      scale = (clientWidth / designWidth)
      document.querySelector(renderDom).style.height = `${clientHeight / scale}px`
    } else {
      // 宽度有白边
      scale = (clientHeight / designHeight)
      document.querySelector(renderDom).style.width = `${clientWidth / scale}px`;
    }
    
    document.querySelector(renderDom).style.transform = `scale(${scale})`;
  }
  
  ```

  

- **rem + vw/vh**：动态计算 `html 根 font-size` ，页面中使用 `rem` 单位，图表中通过 `vw/vh` 动态计算字体、间距和位移等。

  **优点：**布局的自适应代码量少，适配简单

  **缺点：**留白，有时图表需要单独适配字体



> 留白：页面的内容区域始终和设计稿是同个比例，所以当屏幕比例和设计稿不同时，屏幕超出的部分就变成空白区域。



## 获取 Route params

``` javascript
const getURLParameters = (url) =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => (
      (a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a
    ),
    {}
  )

getURLParameters('google.com') // {}
getURLParameters('http://url.com/page?name=Adam&surname=Smith') // {name: 'Adam', surname: 'Smith'}

```



## 部署优化

### 静态资源优化（[Rails](https://rubyonrails.org/)）

使用 `Rails` 的 `assets pipeline` 可以完成对静态资源的管理优化。

- **配置超长时间的本地缓存**；

  强制浏览器使用本地缓存（cache-control/expires），不要和服务器通信。

- **节省带宽，提高性能采用内容摘要作为缓存更新依据**；

  解决上一步的**缓存更新**问题。
  css 等静态资源打包后的 url 携带内容摘要 `?v=f02bc2` 。文件内容更新时，内容摘要会发生改变，相应的 url 也更着改变，强迫浏览器更新缓存。

  ``` html
  ...
  <link rel="stylesheet" href="a.css?v=f02bc2" />
  
  ```

- **精确的缓存控制静态资源 CDN 部署**；

  页面和静态资源分开部署，静态资源部署到 CDN 节点上。

- **优化网络请求和资源发布路径实现非覆盖式分布**。
  上一步会出现页面和静态资源更新不同的问题，因为分开部署一定会有前有后。
  实现非覆盖式分布，资源路径都携带内容摘要，那么新的资源就一定不会覆盖旧的资源，如果浏览器读取了缓存也没有问题。
  然后页面使用灰度部署，通过灰度系统，使流量分为多部分，一部分流向旧代码，一部分流向新代码。
  灰度系统可以设置比例，可以慢慢提高流向新代码的比例，直到确认没问题后达 100% 的全量。



