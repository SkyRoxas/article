# JavaScript 何謂物件原型 (prototype)

## 何謂物件原型 (prototype)

每個 JavaScript 物件都會有 **第二個** JavaScript 的物件與之關聯，而這個第二個的 JavaScript 物件就被稱呼為 **"物件原型"**

> 而第一個物件就是從這個原型來繼承特性

### 參考方式：

JavaScript 中我們使用下列方式來參考原型物件

```language{javascript}
Object.prototype
```

且像是 ECMADscript 定義的 Native(原生)物件：Object() 、 Date() 、 Array() ... 也都具有著 prototype 的原型物件

而當我們使用 **new** 與 **建構式（constructor）** 所產生的新物件：

```language{javascript}
new Date();
```

其原型就為 **建構式（constructor）** 的 prototype 特性，也就是：

```language{javascript}
Date.prototype
```

### 另外一提：

Object.prototype 是 Javascript 中唯一沒有原型的物件，Object.prototype沒有繼承任何特性，其餘的物件則都有繼承 Object.prototype的屬性：

#### 我們以 Date()物件為例：

```language{javascript}
object.prototype

//  ⇩⇩ 給予繼承

date.prototype

//  ⇩⇩ 給予繼承

new date()
```

#### 如上所示：

我們使用 new date() 產生出的 date物件同時繼承了 date.prototype 物件與 object.prototype 物件的特性，這些串成衣系列的原型物件則稱之為 **"原型鏈（prototype chain) "**
