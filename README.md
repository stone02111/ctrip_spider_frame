# 一个简单的分布式爬虫框架

>  主要为了爬取携程的航班信息，提供两个可以运行的示例爬虫



## 使用方法：

- 在`Spider work`里初始化你的爬虫，从`airline_q`中pop要处理的航班爬取任务。


- 目前提供两个示例爬虫：
  - SpiderWork.py 为一个高速爬虫，速度较快但不稳定，实测爬取约3s一次爬取，但时间长很容易被封，不太清楚被封的机制，目前是采用selenium模拟一个浏览器然后保持session并提供cookie，但是续命效果不是很好，能保持大概1000次以下的爬取，再多的话就有可能收到网络繁忙的返回结果
  - SpiderWorkS.py 是一个较为稳定的爬虫，大概10s～15s一次爬取，目前还没有出现被封的情况，但是稍微更占用系统资源和网络资源，建议在网络条件比较好的情况下运行
- 定制：修改`BaseManager`的地址使节点可以运行在远程服务器上。



##  爬取城市列表来源

- 包含了携程的城市机场列表信息，见`cities.json`


- 包含了为了处理方便而重新生成的机场列表信息，见`city_list.json`

  - 合并了某座城市有多个机场的情况，例如，在原始列表里，`SHA`(上海)的城市三字码对应两个元素：

    `SHA,SHA`(上海虹桥国际机场)和`SHA,PVG`(上海浦东国际机场)，现修改为一个城市对应其城市名称三字码只对应一个元素，即`SHA`仅对应`SHA`

    即

  - 将原始信息结构重新调整：

    | 列表名        | 含义                    |
    | ---------- | --------------------- |
    | “ABCDEF”   | 包含拼音首字母为列表名所示的城市信息的列表 |
    | “GHIJ”     | 包含拼音首字母为列表名所示的城市信息的列表 |
    | “KLMN”     | 包含拼音首字母为列表名所示的城市信息的列表 |
    | “PQRSTUVW” | 包含拼音首字母为列表名所示的城市信息的列表 |
    | “XYZ”      | 包含拼音首字母为列表名所示的城市信息的列表 |
    | “热门”       | 包含热门城市信息的列表           |

  - ```json
    {
    	"display": "武汉",   城市名
    	"code": "WUH",		三字码（并不是机场三字码）
    	"index": "477",		在所有城市列表里的索引
    	"pinyin": "Wuhan"	汉语拼音
    },
    ```

- 四个队列用于进程间的通信：

  ```python
  #airline_q  将要爬取的航班队列
  #result_q   爬虫返回的结果
  #conn_q     获取的最新的需要爬取的航班队列
  #store_q    将爬取的反馈信息进行存储
  ```


