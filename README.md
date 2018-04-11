### 移动端的datetimepicker 时间选择插件

单文件组件

使用方法：

```javascript
<date-time-picker
    :isShow="isDateTimePickerShow" 
    v-model="value"
    v-on:dateTimeClick="isDateClick()"></date-time-picker>
```

options

|参数名|必选|类型|说明|
|:----|:---|:-----|:-----|
|value|是|string|时间值 格式 2018-04-11 14:58|
|isDateTimePickerShow|否|boolean|是否弹出选择组件|
|dateTimeClick|否|事件|date区域点击|