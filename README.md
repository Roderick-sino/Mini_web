# Mini-web
1. 为非项目文件，包含数据库，演示文件
2. 此项目为Mini-web项目，利用python，mysql，pycharm做的web框架的3个页面
3. 此项目用的路由为带参数的装饰器路由，通常用于flask框架
4. 路由列表被注释，通常用于django
5. gg
##Mini-Web框架

web框架：为web服务器提供服务的应用程序

![Xnip2019-04-18_14-56-18](E:\百度云盘\0-课件\阶段2-python和Linux 高级编程、阶段3-前端开发\代码以及其他\02-第14天{mini-Web框架}\02-其它资料\Xnip2019-04-18_14-56-18.png)

- web服务器接受浏览器发送的请求，如果是动态资源请求找web框架来处理
- web框架负责处理浏览器的动态资源请求，按处理结果发给服务器
- web服务器把web框架处理的结果封装成http响应报文发送给浏览器

WSGI协议规定web服务器吧动态资源的请求信息传给web框架处理，web框架把处理好的结果返回给web服务器

###1.框架程序开发

###2.模板功能的替换

请求index时，打开index模板文件，从数据库在查找数据塞入网页

```python
"""web框架的职责专门处理动态资源请求"""
import time


# 获取首页数据
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求
    if request_path == "/index.html":
        # 获取首页数据
        result = index()
        # 把处理后的结果返回给web服务器使用，让web服务器拼接响应报文时使用
        return result
    elif request_path == "/center.html":
        # 获取个人中心数据
        result = center()
        return result
    else:
        # 没有动态资源数据，返回404状态信息
        result = not_found()
        return result
```

###3.路由列表功能

django开发是使用



==路由==什么是路由？路由就是请求的URL到处理函数的映射，也就是说提前把请求的URL和处理函数关联好。

==路由列表==：这么多路由怎么管理，可以使用路由列表进行管理，通过路由列表保存每一个路由

| 请求路径     | 处理函数   |
| ------------ | ---------- |
| /login.html  | login函数  |
| /index.html  | index函数  |
| /center.html | center函数 |

没一条都是一个路由



```python
 """web框架的职责专门处理动态资源请求"""
import time


# 获取首页数据
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 路由列表，列表里面的每一条记录都是一个路由
route_list = [
    ("/index.html", index),
    ("/center.html", center)
]


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求
    
    # 遍历路由列表，匹配请求的URL
    for path,func in route_list:
        if request_path==path:
            #找到路由，执行对应的处理函数
            result = func()
            return result
        
        else:
            # 没有动态资源数据，返回404状态信息
            result = not_found()
            return result
    
    # if request_path == "/index.html":
    #     # 获取首页数据
    #     result = index()
    #     # 把处理后的结果返回给web服务器使用，让web服务器拼接响应报文时使用
    #     return result
    # elif request_path == "/center.html":
    #     # 获取个人中心数据
    #     result = center()
    #     return result
    # else:
    #     # 没有动态资源数据，返回404状态信息
    #     result = not_found()
    #     return result
```

###4.装饰器的方式添加路由

####1.使用带有参数的装饰器添加路由

前面是实现了路由列表，但是每次添加路由都需要手动添加来完成，接下来我们都想要完成路由的自动添加，可以通过装饰器来实现，在使用装饰器对处理函数进行装饰的时候我们需要知道装饰的函数和那个请求路径进行关联，也就是说装饰器需要接受一个url参数，这样的装饰器称为带有参数的装饰器。

```python
"""web框架的职责专门处理动态资源请求"""
import time

# 路由列表，列表里面的每一条记录都是一个路由
route_list = [
    # ("/index.html", index),
    # ("/center.html", center)
]


# 带有参数的装饰器
def route(path):
    # 装饰器
    def decorator(func):
        # 当执行装饰器的时候就需要把路由添加到路由列表内
        # 当装饰函数的时候只添加一次路由即可
        route_list.append((path, func))

        def inner():
            result = func()
            return result

        return inner

    # 返回一个装饰器
    return decorator


# 获取首页数据
"/index.html"


@route("/index.html")  # => @decorator =>index=decorator(index)
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 获取个人中心数据
@route("/center.html")
def center():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/center.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    data = time.ctime()

    response_body = file_data.replace("{%content%}", data)

    # 这里返回的是一个元组
    return status, response_header, response_body


# 处理没有找到的动态资源
def not_found():
    # 状态信息、
    status = "404 Not Found"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]

    # web处理后的数据
    data = "not found"

    # 这里返回的是一个元组
    return status, response_header, data


# 处理动态资源请求
def handle_request(env):
    # 获取动态的请求资源路径
    request_path = env["request_path"]
    print("动态资源请求的地址是:" + request_path)
    # 判断请求的动态资源路径，选择指定的函数处理对应的动态资源请求

    for path, func in route_list:
        if request_path == path:
            # 找到路由，执行对应的处理函数
            result = func()
            return result

        else:
            # 没有动态资源数据，返回404状态信息
            result = not_found()
            return result


if __name__ == '__main__':
    print(route_list)    # [('/index.html', <function index at 0x000002A5F65D4E18>), ('/center.html', <function center at 0x000002A5F65EA268>)]
```

###5.显示股票信息页面开发

#### 1.数据准备

stock_db

```mysql
-- MySQL dump 10.13  Distrib 5.7.17, for Linux (x86_64)
--
-- Host: localhost    Database: stock_db
-- ------------------------------------------------------
-- Server version	5.7.13-0ubuntu0.16.04.2

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `focus`
--

DROP TABLE IF EXISTS `focus`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `focus` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `note_info` varchar(200) DEFAULT '',
  `info_id` int(10) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `info_id` (`info_id`),
  CONSTRAINT `focus_ibfk_1` FOREIGN KEY (`info_id`) REFERENCES `info` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `focus`
--

LOCK TABLES `focus` WRITE;
/*!40000 ALTER TABLE `focus` DISABLE KEYS */;
INSERT INTO `focus` VALUES (2,'你确定要买这个？',36),(3,'利好',37),(9,'',88),(10,'',89),(13,'',1);
/*!40000 ALTER TABLE `focus` ENABLE KEYS */;
UNLOCK TABLES;

--
-- Table structure for table `info`
--

DROP TABLE IF EXISTS `info`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `info` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `code` varchar(6) NOT NULL COMMENT '股票代码',
  `short` varchar(10) NOT NULL COMMENT '股票简称',
  `chg` varchar(10) NOT NULL COMMENT '涨跌幅',
  `turnover` varchar(255) NOT NULL COMMENT '换手率',
  `price` decimal(10,2) NOT NULL COMMENT '最新价',
  `highs` decimal(10,2) NOT NULL COMMENT '前期高点',
  `time` date DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=95 DEFAULT CHARSET=utf8;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `info`
--

LOCK TABLES `info` WRITE;
/*!40000 ALTER TABLE `info` DISABLE KEYS */;
INSERT INTO `info` VALUES (1,'000007','全新好','10.01%','4.40%',16.05,14.60,'2017-07-18'),(2,'000036','华联控股','10.04%','10.80%',11.29,10.26,'2017-07-20'),(3,'000039','中集集团','1.35%','1.78%',18.07,18.06,'2017-06-28'),(4,'000050','深天马A','4.38%','4.65%',22.86,22.02,'2017-07-19'),(5,'000056','皇庭国际','0.39%','0.65%',12.96,12.91,'2017-07-20'),(6,'000059','华锦股份','3.37%','7.16%',12.26,12.24,'2017-04-11'),(7,'000060','中金岭南','1.34%','3.39%',12.08,11.92,'2017-07-20'),(8,'000426','兴业矿业','0.41%','2.17%',9.71,9.67,'2017-07-20'),(9,'000488','晨鸣纸业','6.30%','5.50%',16.37,15.59,'2017-07-10'),(10,'000528','柳工','1.84%','3.03%',9.42,9.33,'2017-07-19'),(11,'000540','中天金融','0.37%','5.46%',8.11,8.08,'2017-07-20'),(12,'000581','威孚高科','3.49%','3.72%',27.00,26.86,'2017-06-26'),(13,'000627','天茂集团','5.81%','12.51%',10.93,10.33,'2017-07-20'),(14,'000683','远兴能源','6.42%','21.27%',3.48,3.29,'2017-07-19'),(15,'000703','恒逸石化','0.24%','1.65%',16.92,16.88,'2017-07-20'),(16,'000822','山东海化','6.60%','8.54%',9.05,8.75,'2017-07-06'),(17,'000830','鲁西化工','1.38%','4.80%',7.36,7.26,'2017-07-20'),(18,'000878','云南铜业','1.26%','3.23%',14.50,14.47,'2017-07-19'),(19,'000905','厦门港务','5.44%','10.85%',15.90,15.60,'2017-04-20'),(20,'000990','诚志股份','0.53%','1.00%',16.99,16.90,'2017-07-20'),(21,'002019','亿帆医药','1.19%','2.81%',17.05,16.85,'2017-07-20'),(22,'002078','太阳纸业','2.05%','1.90%',8.45,8.29,'2017-07-19'),(23,'002092','中泰化学','7.25%','6.20%',15.53,14.48,'2017-07-20'),(24,'002145','中核钛白','2.43%','7.68%',6.75,6.61,'2017-07-19'),(25,'002285','世联行','8.59%','5.66%',9.23,8.50,'2017-07-20'),(26,'002311','海大集团','1.13%','0.24%',18.81,18.63,'2017-07-19'),(27,'002460','赣锋锂业','9.41%','9.00%',63.70,58.22,'2017-07-20'),(28,'002466','天齐锂业','3.62%','3.66%',68.44,66.05,'2017-07-20'),(29,'002470','金正大','2.30%','0.99%',8.00,7.82,'2017-07-20'),(30,'002496','辉丰股份','3.15%','4.29%',5.24,5.08,'2017-04-10'),(31,'002497','雅化集团','0.38%','12.36%',13.10,13.05,'2017-07-20'),(32,'002500','山西证券','0.44%','3.70%',11.49,11.44,'2017-07-20'),(33,'002636','金安国纪','2.70%','11.59%',19.80,19.42,'2017-07-19'),(34,'300032','金龙机电','0.66%','0.72%',15.28,15.18,'2017-07-20'),(35,'300115','长盈精密','0.60%','0.59%',33.50,33.41,'2017-07-19'),(36,'300268','万福生科','-10.00%','0.27%',31.77,13.57,'2017-04-10'),(37,'300280','南通锻压','3.31%','0.66%',32.20,32.00,'2017-04-11'),(38,'300320','海达股份','0.28%','0.82%',18.26,18.21,'2017-07-20'),(39,'300408','三环集团','1.69%','0.81%',23.42,23.17,'2017-07-19'),(40,'300477','合纵科技','2.84%','5.12%',22.10,22.00,'2017-07-12'),(41,'600020','中原高速','5.46%','4.48%',5.60,5.31,'2017-07-20'),(42,'600033','福建高速','1.01%','1.77%',4.00,3.99,'2017-06-26'),(43,'600066','宇通客车','4.15%','1.49%',23.08,23.05,'2017-06-13'),(44,'600067','冠城大通','0.40%','2.97%',7.56,7.53,'2017-07-20'),(45,'600110','诺德股份','2.08%','4.26%',16.16,15.83,'2017-07-20'),(46,'600133','东湖高新','9.65%','21.74%',13.64,12.44,'2017-07-20'),(47,'600153','建发股份','3.65%','2.03%',13.35,13.21,'2017-07-10'),(48,'600180','瑞茂通','2.20%','1.07%',14.86,14.54,'2017-07-20'),(49,'600183','生益科技','6.94%','4.06%',14.94,14.12,'2017-07-19'),(50,'600188','兖州煤业','1.53%','0.99%',14.56,14.43,'2017-07-19'),(51,'600191','华资实业','10.03%','11.72%',15.80,14.36,'2017-07-20'),(52,'600210','紫江企业','6.03%','10.90%',6.68,6.30,'2017-07-20'),(53,'600212','江泉实业','1.39%','1.78%',10.20,10.15,'2017-07-19'),(54,'600225','*ST松江','4.96%','2.47%',5.71,5.61,'2017-04-13'),(55,'600230','沧州大化','5.74%','13.54%',43.26,40.91,'2017-07-20'),(56,'600231','凌钢股份','2.79%','3.77%',3.68,3.60,'2017-07-19'),(57,'600291','西水股份','10.02%','9.23%',34.71,31.55,'2017-07-20'),(58,'600295','鄂尔多斯','4.96%','12.62%',16.51,15.73,'2017-07-20'),(59,'600303','曙光股份','8.37%','14.53%',11.53,10.64,'2017-07-20'),(60,'600308','华泰股份','1.12%','2.66%',6.30,6.26,'2017-07-19'),(61,'600309','万华化学','0.03%','1.78%',31.81,31.80,'2017-07-20'),(62,'600352','浙江龙盛','0.39%','1.85%',10.32,10.28,'2017-07-20'),(63,'600354','敦煌种业','7.89%','18.74%',9.44,8.75,'2017-07-20'),(64,'600408','安泰集团','1.98%','3.38%',4.13,4.12,'2017-04-13'),(65,'600409','三友化工','0.62%','3.78%',11.36,11.29,'2017-07-20'),(66,'600499','科达洁能','0.46%','3.94%',8.84,8.80,'2017-07-20'),(67,'600508','上海能源','3.26%','2.99%',13.32,13.01,'2017-07-19'),(68,'600563','法拉电子','0.32%','1.36%',53.67,53.50,'2017-07-20'),(69,'600567','山鹰纸业','0.76%','2.85%',3.98,3.96,'2017-07-19'),(70,'600585','海螺水泥','0.45%','0.61%',24.51,24.44,'2017-07-19'),(71,'600668','尖峰集团','4.35%','6.43%',18.70,18.36,'2017-04-13'),(72,'600688','上海石化','2.72%','0.91%',6.80,6.74,'2017-06-01'),(73,'600729','重庆百货','5.70%','3.34%',27.45,27.13,'2017-06-29'),(74,'600739','辽宁成大','3.30%','3.50%',19.74,19.11,'2017-07-20'),(75,'600779','水井坊','3.85%','2.77%',29.39,28.30,'2017-07-20'),(76,'600781','辅仁药业','8.61%','4.16%',23.46,21.89,'2017-05-02'),(77,'600801','华新水泥','4.00%','10.15%',12.99,12.49,'2017-07-20'),(78,'600846','同济科技','2.06%','17.41%',9.39,9.26,'2017-04-13'),(79,'600884','杉杉股份','1.08%','3.53%',20.67,20.45,'2017-07-20'),(80,'600966','博汇纸业','2.89%','5.54%',6.41,6.28,'2017-07-19'),(81,'600971','恒源煤电','2.36%','8.81%',12.16,11.88,'2017-07-20'),(82,'601012','隆基股份','0.76%','1.30%',19.93,19.78,'2017-07-20'),(83,'601100','恒立液压','4.78%','0.92%',19.31,18.97,'2017-07-13'),(84,'601101','昊华能源','4.03%','6.06%',11.10,10.80,'2017-07-19'),(85,'601216','君正集团','2.16%','2.26%',5.20,5.10,'2017-04-17'),(86,'601666','平煤股份','2.81%','6.14%',6.96,6.77,'2017-07-20'),(87,'601668','中国建筑','2.39%','1.42%',10.70,10.45,'2017-07-20'),(88,'601678','滨化股份','0.13%','2.47%',7.92,7.91,'2017-07-20'),(89,'601918','新集能源','1.23%','3.11%',4.93,4.92,'2017-07-19'),(90,'603167','渤海轮渡','2.77%','3.34%',11.87,11.61,'2017-04-13'),(91,'603369','今世缘','3.34%','2.13%',14.24,13.78,'2017-07-20'),(92,'603589','口子窖','3.99%','1.84%',39.37,39.04,'2017-06-26'),(93,'603799','华友钴业','2.38%','7.19%',67.46,65.89,'2017-07-20'),(94,'603993','洛阳钼业','2.94%','2.50%',7.36,7.16,'2017-07-19');
/*!40000 ALTER TABLE `info` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2017-08-18 20:53:58

```

create database stock_db charset=utf8;

```
create database stock_db charset=utf8;
quite;
#转至文件目录
cd /d %userprofile%\desktop
mysql -u root -p
use stock_db;

#执行这个文件
source stock_db.sql

#显示一下是否成功
show tables;
```

```python
@route("/index.html")  # => @decorator =>index=decorator(index)
def index():
    # 状态信息、
    status = "200 OK"
    # 响应头信息
    response_header = [("Server", "PWS/1.1")]
    # 1. 打开指定的模板文件，读取模板文件重的数据
    with open("template/index.html", "r") as file:
        file_data = file.read()
    # 2.查询数据库，把模板立的模板变量（{%content}）替换成从数据库中查询的数据
        # 创建连接对象
        conn = pymysql.connect(host="localhost",
                               port=3306,
                               user="root",
                               password="Password",
                               database="stock_db",
                               charset="utf8",
                               )
        # 获取游标
        cursor = conn.cursor()

        # 准备sql
        sql = 'select * from info;'

        # 执行sql
        cursor.execute(sql)
        # 获取查询结果
        result = cursor.fetchall()
        print(result)
        # 关闭游标
        cursor.close()
        # 关闭连接
        conn.close()

    # web处理后的数据
    # 获取当前时间,模拟数据库内容
    # 遍历每一条数据，完成数据的tr标签的封装
    data = ""
    for row in result:
        data += """<tr>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td>%s</td>
                    <td><input type="button" value="添加" id="toAdd" name="toAdd" systemidvaule="000007"></td>
                   </tr>""" % row

    response_body = file_data.replace("{%content%}", data)
   

    # 这里返回的是一个元组
    return status, response_header, response_body

```

### 6.个人中心数据接口的开发

```python
# 个人中心数据接口
@route("/center_data.html")
def center_data():
    # 从数据库把数据查询出来，然后把查询出来的数据转成json数据
    # 创建连接对象
    conn = pymysql.connect(host="localhost",
                           port=3306,
                           user="root",
                           password="Password",
                           database="stock_db",
                           charset="utf8",
                           )
    # 获取游标
    cursor = conn.cursor()
    # 执行sql
    sql = '''select i.code,i.short,i.chg,i.turnover,i.price,i.highs,f.note_info 
            from info i inner join focus f 
            on i.id=f.info_id;
          '''
    # 执行sql
    cursor.execute(sql)
    # 获取查询结果
    result = cursor.fetchall()
    print(result)
    # 把元组转成列表字典
    center_data_list = [{
        "code": row[0],
        "short": row[1],
        "chg": row[2],
        "turnover": row[3],
        "price": str(row[4]),
        "highs": str(row[5]),
        "note_info": row[6],
    } for row in result]
    print(center_data_list)
    # 把列表转成json字符串数据
    # ensure_ascii=False在控制台能显示中文
    json_str = json.dumps(center_data_list, ensure_ascii=False)
    print(json_str)
    print(type(json_str))
    # 关闭游标
    cursor.close()
    # 关闭连接
    conn.close()
    # 状态信息
    status = "200 OK"
    # 响应头信息
    response_header = [
        ("Server", "PWS/1.1"),
        # 指定编码格式，因为没有模板文件，可以通过响应头指定编码格式
        ("Content-Type"," text/html;charset=utf-8")
                       ]
    return status, response_header, json_str
```

### 7.ajax请求数据渲染个人中心页面

```html
$(document).ready(function(){
    // 发送ajax请求，获取个人中心数据
    $.get("center_data.html",function (data) {
        // ajax 成功回调函数
        // 获取table标签
        var $table = $(".table");
        // 如果指定了返回数据的解析方式是json，那么data就是一个js对象
        for(var i = 0; i < data.length; i++){
            // 根据下标获取每一个个人中心数据js对象
            var oCenterData = data[i];

            // 封装后的每一个tr标签
            var oTr = '<tr>' +
                        '<td>'+ oCenterData.code +'</td>' +
                        '<td>'+ oCenterData.short +'</td>' +
                        '<td>'+ oCenterData.chg +'</td>' +
                        '<td>'+ oCenterData.turnover +'</td>' +
                        '<td>'+ oCenterData.price +'</td>' +
                        '<td>'+ oCenterData.highs +'</td>' +
                        '<td>'+ oCenterData.note_info +'</td>' +
                        '<td><a type="button" class="btn btn-default btn-xs" href="/update/000007.html"> <span class="glyphicon glyphicon-star" aria-hidden="true"></span> 修改 </a></td>' +
                        '<td><input type="button" value="删除" id="toDel" name="toDel" systemidvaule="000007"></td>' +
                        '</tr>'
            // 给table标签追加每一行tr标签
            $table.append(oTr)

        }

    }, "json");
```

###8.logging日志

用于记录程序中的日志信息

使用logging这个包来完成

####记录日志的目的：

1. 可以很方便的了解程序的运行情况
2. 可以分析用户的操作行为、喜好等信息
3. 方便开发人员检查bug

####日志级别介绍：

日志等级可以分为5个，从低刀高分为

1. DEBUG
2. INFO
3. WARNING
4. ERROR
5. CRITICAL(critical)

####日志等级说明：

- DEBUG:程序调试bug时使用
- INFO：程序正常运行时使用
- WARNING：程序未按照预期运行时使用，但并不是错误，如：用户登录的密码错误
- ERROR：程序出错时候使用，
- CRITICAL：特别严重的问题，导致程序不能再继续运行的时候使用，如：磁盘空间为空，一般很少使用
- 默认的是WARNING等级，当在WARNING或者以上时候菜进行记录
- 日志等级由低到高为：debug<info<warning<error<critical

####logging日志的使用

在logging包中记录日志的方式有2种：

1. 输出到控制台
2. 保存到日志文件

####日志信息输出到控制台的示例：

```python
# logging日志用来记录程序运行使用的日志信息
import logging

logging.debug("i'm debug")
logging.info("i'm info")
# 默认是warning级别
logging.warning("i'm warning")
logging.error("i'm error")
logging.critical("i'm critical")
```

控制台输出结果：

```
WARNING:root:i'm warning
ERROR:root:i'm error
CRITICAL:root:i'm critical

```

####日志等级和输出格式的设置：

```python
# logging日志用来记录程序运行使用的日志信息
import logging

# 设置logging日志的配置信息
# level 表示设置级别
# %(asctime)s 表示当前时间
# %(filename)s 文件名
# %(lineno)d 表示日志行数
# %(levelname)s 表示日志等级
# %(message)s 表示日志内容

# filename输出日志的文件名
# filemode打开文件后的操作方式-详见python文件，w表示每次清空重新写入，a表示每次在已有记录后继续写入

logging.basicConfig(level=logging.DEBUG,
                    format="%(asctime)s-%(filename)s[lineno:%(lineno)d]-%(levelname)s-%(message)s",
                    filename="log.txt",
                    filemode="w"
                    )
```

日志文件：

``` 
2021-02-22 21:16:39,027-01.py[lineno:22]-DEBUG-i'm debug
2021-02-22 21:16:39,027-01.py[lineno:23]-INFO-i'm info
2021-02-22 21:16:39,035-01.py[lineno:25]-WARNING-i'm warning
2021-02-22 21:16:39,035-01.py[lineno:26]-ERROR-i'm error
2021-02-22 21:16:39,035-01.py[lineno:27]-CRITICAL-i'm critical

```

#### 项目中的应用

```python
# 在程序入口模块，设置logging日志的配置信息，只配置一次，整个程序都可以使用，好比单例
logging.basicConfig(level=logging.DEBUG,
                    format="%(asctime)s-%(filename)s[lineno:%(lineno)d]-%(levelname)s-%(message)s",
                    filename="log.txt",
                    filemode="a")
```

### 其他

上个项目在windows上面会遇到3个问题

1. log.txt中乱码

   windows默认保存txt是acsll模式，print传的是gbk模式，整个项目是utf-8模式需要调一下

2. 读文件时候需要额外添加encoding

   ![image-20210223190202347](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210223190202347.png)

3. 循环有问题

   ![image-20210223190241521](C:\Users\yyf\AppData\Roaming\Typora\typora-user-images\image-20210223190241521.png)

   前面代码的循环只能显示index界面，此处怀疑是python版本和系统问题。就是显示path=index后直接匹配不对就已经输出not found了 不会在for循环一下一个路由匹配
