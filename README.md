# 数据可视化

给老婆使用的数据可视化分析项目，支持动态添加图标，后端请参阅响应的项目。该项目参考了
https://github.com/SimonZhangITer/DataVisualization

## Build Setup

``` bash
# install dependencies
cnpm install

# serve with hot reload at localhost:8000
npm run dev

# build for production with minification
npm run build
```

## 后端api相关参数
1. level1： 主类型的过滤条件
2. level2： 次类型的过滤条件
3. groupBy：分组统计字段
4. total：是否显示总计数量
5. startDate：数据过滤事件
6. endDate：数据过滤事件
7. lastYear：是否启用lastYear数据(lastYear数据可以通过data.data2获得)

## 请求返回值
1. axis：数据列名
2. data：当前数据
3. data2：历史数据

