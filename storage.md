# 本地存储统一管理

1. 自动序列化，存储的时候转字符串，取的时候再转回来；
2. 类型自动推断，在实例化的时候传入类型，在设置和取值的时候都会自动类型推断；
3. 可以统一管理，把本地存储都放在一个文件里面，避免后期本地存储混乱不好维护的问题；
4. 处理部分浏览器无痕模式下无法使用 Storage 的问题。
5. 抹平平台差异，web ，小程序，移动端，桌面端都适合。



## 封装

``` typescript
/**
 * @description Custom storage api
 * Solve the problem of being unable to use storage in private browsing mode.
 */

/**
 * Use mock storage when web storage is unavailable. T is the saved value type.
 * @class CustomStorage
 * @param type the used storage type: 'localStorage' | 'sessionStorage'
 * @param key the saved key
 * @param defaultValue the saved default value
 */
export default class CustomStorage<T> implements IStorage<T> {
  usedStorage: Storage

  constructor(
    public type: StorageType,
    public key: string,
    public defaultValue: T
  ) {
    this.usedStorage = this.isSupport() ? window[this.type] : new MockStorage()
  }

  // Check if the web storage is available
  private isSupport() {
    try {
      const text = 'isSupportStorage'
      window[this.type].setItem(text, text)
      const value = window[this.type].getItem(text)
      window[this.type].removeItem(text)
      return value == text
    } catch (e) {
      return false
    }
  }

  setItem(value: T) {
    this.usedStorage.setItem(this.key, JSON.stringify(value))
  }

  getItem() {
    const value = this.usedStorage.getItem(this.key)
    if (value === undefined) return this.defaultValue

    let temp = this.defaultValue
    if (value && value !== 'null' && value !== 'undefined') {
      try {
        temp = JSON.parse(value) as T
      } catch (error) {
        temp = value as unknown as T
      }
    }
    return temp
  }

  removeItem() {
    this.usedStorage.removeItem(this.key)
  }
}

class MockStorage implements Storage {
  constructor(
    private tempStorage: {
      [propName: string]: string
    } = {}
  ) {}

  get length() {
    return Object.keys(this.tempStorage).length
  }

  setItem(key: string, value: string) {
    this.tempStorage[key] = value
  }

  getItem(key: string) {
    return this.tempStorage[key]
  }

  removeItem(key: string) {
    delete this.tempStorage[key]
  }

  key(nth: number) {
    return Object.keys(this.tempStorage)[nth]
  }

  clear() {
    this.tempStorage = {}
  }
}

type StorageType = 'localStorage' | 'sessionStorage'

interface IStorage<T> {
  type: StorageType
  key: string
  defaultValue: T
  usedStorage: Storage
}

```



## 使用

``` typescript
// common/storage.ts
import MyStorage from '@/utils/storage.ts'

export const tokenStorage = new MyStorage<string>('localStorage', 'token', '')
```

``` typescript
// views/index.ts
import { tokenStorage } from '@/common/storage.ts'

tokenStorage.setItem('myToken')
tokenStorage.getItem()
```

