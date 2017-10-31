
### 环境
安装Python3.5, django 1.9, 至少安装相关库jieba,geohash

其中需要更改Geohash的目录名为geohash,并且__init__.py的import geohash要改成.geohash

### 项目说明
1. 前期是用**Node.js**爬取58同城的租房信息,然后做成一个可以方便使用的租房信息搜索引擎
为了方便，仅仅是用来实践搜索引擎中的倒排索引,查询策略,BM25模型和geohash编码的,
没有直接利用高德API,自己做了数据处理
#### 2. **合作者@huangmxsysu**, 但是做了前端的模板后跑路了, 包括数据采集、处理、最后的高德API、分词、前端页面都没做, 最后只能靠自己一个人单挑

### 项目结构
  
│-app  
│  │-data_          // stop_words.txt  
│  │-migrations  
│  │  
│  │-se             // 后端数据处理,包含高德API,地理信息索引  
│  │  └─config  
│  │-templates      // 网页模板，包含高德API的js代码,用于地图标注和显示  
│  
│-sousuo            // django项目配置及路由设置  
│    
│-static  
    │-css  
    │  └─font-awesome-4.7.0  
    │      │-css  
    │      │-fonts  
    │      │-less  
    │      └─scss  
    │-images  
    └─scss  

### 数据处理
原始字段 => 根据addr调用API => 得到经纬度信息 => 添加到原数据的后面作为新字段 => 根据经纬度生成geohash 编码的数据
- **原始字段**  price addr description url img
- **调用高德API**
``` 
http://restapi.amap.com/v3/geocode/geo?address=___&output=___&key=你申请的key
``` 
#### Example
``` 
    查询参数:
        address=地址  有长度限制
        output=json/xml
        key=开发者密钥,
            <
            # 'aa4a48297242d22d2b3fd6eddfe62217'
            # '9a491a13997579f985c98165bb68d127'
            # '7ec25a9c6716bb26f0d25e9fdfa012b8'
            >
``` 
[测试 高德API](http://restapi.amap.com/v3/geocode/geo?address=%E6%9D%AD%E5%B7%9E0%7C%E6%9D%AD%E5%B7%9E1%7C%E6%9D%AD%E5%B7%9E2%7C%E6%9D%AD%E5%B7%9E3%7C%E6%9D%AD%E5%B7%9E4%7C%E6%9D%AD%E5%B7%9E5%7C%E6%9D%AD%E5%B7%9E6%7C%E6%9D%AD%E5%B7%9E7%7C%E6%9D%AD%E5%B7%9E8%7C%E6%9D%AD%E5%B7%9E9&output=json&batch=true&key=7ec25a9c6716bb26f0d25e9fdfa012b8)


### 运行

1.   cmd 运行 **manage.py runserver**
		访问 127.0.0.1:8000
2.    要想根据se/data_/all_geodata.json
产生geohash编码的数据,参考se/API.py

### 数据
- app/data_/stop_words.txt
- app/se/geoidx.db
- app/se/add_enc_geodata.json
- app/se/save.db

### 页面
![](static/images/layout.jpg)

### References
- 有好几个github项目都是类似的租房查询,由于直接使用高德API,确实能有更多的功能
- [Golang 写一个搜索引擎 （0x00）--- 从零开始](https://juejin.im/post/587c62f561ff4b0065018086)
- 高德API, 但是高德API的查询准确度还是不那么令人满意, batch=true 是否批量,批量最多10个,可能有字符长度限制, 每天限制2000次, 最多20000个, 而且返回数据