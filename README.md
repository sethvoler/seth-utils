# seth-utils

<details>

<summary style="font-weight: 900; font-size: 20px;">工具函数及需求解决的集合仓库</summary>

- [汉字全拼及中英文混合排序](#汉字全拼及中英文混合排序)

</details>

## 汉字全拼及中英文混合排序

### 需求描述

根据数组内某字段的中文全拼首字母和英文单个单子首字母进行混合排序

例子如下：
```js
// 原始数据
var test = [
  { BrandId: 7, Name: "樱木花道", Order: 0 },
  { BrandId: 3, Name: "碧海蓝天", Order: 0 },
  { BrandId: 4, Name: "Bay zhou", Order: 0 },
  { BrandId: 2, Name: "Babi Jay", Order: 0 },
  { BrandId: 1, Name: "Lucy Vikcy chen", Order: 0 },
];

// 符合需求的输出
var res = [ 
  { BrandId: 3, Name: '碧海蓝天', Order: 0 }, // BHLT
  { BrandId: 2, Name: 'Babi Jay', Order: 0 }, // BJ
  { BrandId: 4, Name: 'Bay zhou', Order: 0 }, // BZ
  { BrandId: 1, Name: 'Lucy Vikcy chen', Order: 0 }, // LVC
  { BrandId: 7, Name: '樱木花道', Order: 0 } // YMHD
];
```

### 解决思路

#### 1. 将中文转化为全拼首字母缩写并转为大写

引入对应的拼音匹配字典，进行正则替换，提取首字母，并转为大写
```js
// 关键代码
// 提取首字母，返回大写形式     
for (var i = 0, len = str.length; i < len; i++) {
  //获得unicode码
  var ch = str.charAt(i);
  //检查该unicode码是否在处理范围之内,在则返回该码对映汉字的拼音首字母,不在则调用其它函数处理
  chars.push(this._getChar(ch));
}

_getChar: function (ch) {
  var unicode = ch.charCodeAt(0);
  //如果不在汉字处理范围之内,返回原字符,也可以调用自己的处理函数
  if (unicode > 40869 || unicode < 19968)
    return ch; //dealWithOthers(ch);
  //检查是否是多音字,是按多音字处理,不是就直接在strChineseFirstPY字符串中找对应的首字母
  if (!this.options.checkPolyphone)
    return this.char_dict.charAt(unicode - 19968);
  return this.polyphone[unicode] ? this.polyphone[unicode] : this.char_dict.charAt(unicode - 19968);
},
```

#### 2. 将英文转化为首字母缩写并转为大写

因为单词间存在空格，先根据空格 split 分割成数组，每个元素取第一个字母转为大写后 join 合并
```js
item.split(" ").map(i => i[0].toUpperCase()).join("");
```

#### 3. 比较排序

数组 sort 排序，注意返回值显式转化，即 return a > b ? 1 : -1; 如果写成 return a > b; 隐式转换会导致排序错误。
