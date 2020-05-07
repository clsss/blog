---
title: "v-distpicker城市插件"
date: "2017-06-25T23:12:11+08:00"
publishdate: "2017-06-25T23:54:11+08:00"
lastmod: "2017-06-25T23:54:11+08:00"
draft: false
tags: ["web", "vue", "插件"]
series: ["vue"]
categories: ["插件"]
img: "images/blog/img.jpg"
toc: true
summary: "This is an example of module"
---

## v-distpicker城市插件

[官网](https://distpicker.uine.org/)

#### 安装

```bash
npm install v-distpicker --save
或者
yarn add v-distpicker --save
```

#### 使用方式

```bash
import VDistpicker from 'v-distpicker'
Vue.component('v-distpicker', VDistpicker)
```

> 千万注意: :province='==province==',中绑定的省, 不能通过@province="==selectProvince=="方法直接修改(市和区同理).
用于显示与用于绑定的值是2个不同的值

#### Demo
```
方式1：无默认选中的城市
<!-- html -->
<v-distpicker @province="selectProvince" @city="selectCity" @area="selectArea"></v-distpicker>
<pre><code>{{ select }}</code></pre>

<!-- js --> 
export default {
  components: { VDistpicker },
  data() {
    return {
      showCode: false,
      select: { province: '', city: '', area: '' },
    }
  },
  methods: {
    selectProvince(value) {
      this.select.province = value
      console.log(value);
    },
    selectCity(value) {
      this.select.city = value
      console.log(value);
    },
    selectArea(value) {
      this.select.area = value
      console.log(value);
    },
  },
}

方式2：有默认选中的城市
<!-- html -->
<v-distpicker :province="$region.getRegName(formData.province)" :city="$region.getRegName(formData.province)" hide-area @selected='onSelected'>
</v-distpicker>

<!-- js -->
data() {
    return {
      formData：{
        province: '', // 省
        city: '', // 市
      }
    }
}

/* 选择城市 */
methods: {
    <!-- 也可以单个的绑定 -->
    onSelected(data) {
      console.log('城市:', data)
      this.province = data.province.code
      this.city = data.city.code
    },
}
提交的时候使用的是: this.province, this.city
```

#### 补充一下，$region方法
```bash
// 城市
const REGION = [{
  'code': '110000',
  'sheng': '11',
  'di': '00',
  'xian': '00',
  'name': '北京市',
  'level': 1
}, {
  'code': '110100',
  'sheng': '11',
  'di': '01',
  'xian': '00',
  'name': '北京城区',
  'level': 2
}]

export const getRegName = (code) => {
  if (!code) {
    return
  }
  try {
    const name = REGION.find(item => item.code === code).name
    return name
  } catch (err) {
    console.log(`code:${code} is not find name!`, err)
    return '-'
  }
}
```
