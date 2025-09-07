---
title: '[源码]随机图API及其源码分享'
date: 2024-10-19 14:52:44
tags:
- 源码
- php
categories:
- 源码
---
## 一 概述

这是一个简单的随机图API

-   目前收图 910 (最近更新2024.5.19)
    
-   `原神`、`崩坏:星穹铁道`主题
    
-   上传至B站公共静态资源库
    
-   MySQL数据库存储图片链接
    
-   自动识别图片格式为`jpg`或`png`
    
-   自适应识别用户端UA
    
-   多种返回方式`301跳转`、`不跳转直接返回图片`、`json`
    

## 二 使用方法

**仅支持** `GET` **方法**

下列参数均为可选项

> style

-   见图片样式
    

> **return**

-   json: 返回json
    
-   img: 不跳转直接返回图片（已作废）
    
-   url: 301跳转至图片链接
    

> JSON数据

    {
    "url":"https://i0.hdslb.com/bfs/article/3538b2f3b8899b8f95161f4f0a6807433493083985480649.jpg",
    #图片地址
    "width": 6160,
    #图片宽
    "height": 3800,
    #图片高
    "code":200,
    #是否可以正常访问
    }

## 三 图片样式

`格式：(图像原链接)@(\d+[whsepqoc]_?)*(.(|webp|gif|png|jpg|jpeg))?`$

Type

Url

原图

baseURL/1.jpg

原分辨率，质量压缩

baseURL/1.jpg@1e\_1c.jpg

规定宽，高度自适应，质量压缩

baseURL/1.jpg@104w\_1e\_1c.jpg

规定高，宽度自适应，质量压缩

baseURL/1.jpg@104h\_1e\_1c.jpg

规定高宽，质量压缩

baseURL/1.jpg@104w\_104h\_1e\_1c.jpg

原分辨率，webp格式(占用最小)

baseURL/1.jpg@104w\_104h\_1e\_1c.webp

规定高度，webp格式(占用最小)

baseURL/1.jpg@104w\_104h\_1e\_1c.webp

-   w:\[1, 9223372036854775807\] (width，图像宽度)
    
-   h:\[1, 9223372036854775807\] (height，图像高度)
    
-   s:\[1, 9223372036854775807\] (作用未知)
    
-   e:\[0,2\] (resize，0:保留比例取其小，1:保留比例取其大，2:不保留原比例，不与c混用)
    
-   p:\[1,1000\] (默认100，放大倍数，不与c混用)
    
-   q:\[1,100\] (quality，默认75，图像质量)
    
-   o:\[0,1\] (作用未知)
    
-   c:\[0,1\] (clip，0:默认，1:裁剪)
    
-   webp,png,jpeg,gif(不加则保留原格式)
    
-   不区分大小写，相同的参数后面覆盖前面
    
-   计算后的实际w_h不能大于原w_h，否则wh参数失效
    

## 四 分类链接

原神自适应

> [https://api.acgurl.link/ys.php](https://api.acgurl.link/ys.php)

原神竖屏

> [https://api.acgurl.link/yss.php](https://api.acgurl.link/yss.php)

原神横屏

> [https://api.acgurl.link/ysh.php](https://api.acgurl.link/ysh.php)

崩坏:星穹铁道自适应

> [https://api.acgurl.link/xq.php](https://api.acgurl.link/xq.php)

崩坏:星穹铁道竖屏

> [https://api.acgurl.link/xqs.php](https://api.acgurl.link/xqs.php)

崩坏:星穹铁道横屏

> [https://api.acgurl.link/xqh.php](https://api.acgurl.link/xqh.php)

## 五 源码

### PHP后端代码

    <?php
    // 建立与MySQL数据库的连接
    $host = '主机地址';
    $user = '用户名';
    $pass = '密码';
    $dbName = '数据库名';
    $conn = new mysqli($host, $user, $pass, $dbName);
    
    // 检查数据库连接是否成功
    if ($conn->connect_error) {
        die("数据库连接失败: " . $conn->connect_error);
    }
    
    // 判断用户通过电脑端还是手机端访问网站
    function isMobile(){
        $useragent = isset($_SERVER['HTTP_USER_AGENT']) ? $_SERVER['HTTP_USER_AGENT'] : '';
        $mobile_os_list = array('Google Wireless Transcoder','Windows CE','WindowsCE','Symbian','Android','armv6l','armv5','Mobile','CentOS','mowser','AvantGo','Opera Mobi','J2ME/MIDP','Smartphone','Go.Web','Palm','iPAQ');
        $mobile_token_list = array('Profile/MIDP','Configuration/CLDC-','160×160','176×220','240×240','240×320','320×240','UP.Browser','UP.Link','SymbianOS','PalmOS','PocketPC','SonyEricsson','Nokia','BlackBerry','Vodafone','BenQ','Novarra-Vision','Iris','NetFront','HTC_','Xda_','SAMSUNG-SGH','Wapaka','DoCoMo','iPhone','iPod');
    
        $found_mobile = false;
        foreach($mobile_os_list as $substr) {
            if (strpos($useragent, $substr) !== false) {
                $found_mobile = true;
                break;
            }
        }
    
        if (!$found_mobile) {
            foreach($mobile_token_list as $substr) {
                if (strpos($useragent, $substr) !== false) {
                    $found_mobile = true;
                    break;
                }
            }
        }
        return $found_mobile;
    }
    
    // 根据设备类型选择表名
    $tableName = isMobile() ? 'api_yss' : 'api_ysh';
    
    // 选择一个随机的图片链接
    $sql = "SELECT url FROM $tableName ORDER BY RAND() LIMIT 1";
    $result = $conn->query($sql);
    
    // 检查查询结果是否有效
    if ($result->num_rows > 0) {
        // 获取随机图片链接
        $row = $result->fetch_assoc();
        $randomUrl = $row['url'];
    
        // 获取style参数内容
        $style = $_GET['style'] ?? '';
    
        // 附加style参数内容于随机图片链接后
        $randomUrl .= $style;
    
        // 根据不同的返回类型处理
        switch ($_GET['return'] ?? 'url') {
            case 'json':
                $headers = get_headers($randomUrl);
                $statusCode = substr($headers[0], 9, 3);
                $imageSize = getimagesize($randomUrl);
                $response = array(
                    'url' => $randomUrl,
                    'width' => $imageSize[0],
                    'height' => $imageSize[1],
                    'code' => $statusCode
                );
                header('Content-Type: application/json');
                echo json_encode($response);
                break;
            default:
                header('Referrer-Policy: no-referrer');
                header('Cache-Control: no-cache');
                header('Access-Control-Allow-Origin: *');
                header("Location: $randomUrl");
                break;
        }
    } else {
        echo "没有找到图片链接";
    }
    
    // 关闭数据库连接
    $conn->close();
    ?>

### 附加

使用`Python`从`TXT`中导入链接到`MYSQL`数据库中

注意：**需要安装pymysql模块**

    import pymysql
    
    # 数据库配置
    db_config = {
        'user': 'usernmae',
        'password': 'password',
        'host': 'localhost_or_ip',  # 可以是 'localhost' 或 IP 地址
        'database': 'api',
        'charset': 'utf8mb4',
        'cursorclass': pymysql.cursors.DictCursor
    }
    
    # 文件路径
    file_path = 'path-to-the-txt'
    
    # 表名
    table_name = 'api_bm'
    
    # 连接到数据库
    conn = pymysql.connect(**db_config)
    
    # 如果表不存在，则创建表
    create_table_query = f"CREATE TABLE IF NOT EXISTS `{table_name}` " \
                         f"(id INT AUTO_INCREMENT PRIMARY KEY, link VARCHAR(255) UNIQUE)"
    with conn.cursor() as cursor:
        cursor.execute(create_table_query)
    
    # 打开文件并逐行读取链接
    with open(file_path, 'r') as file:
        urls = file.readlines()
    
    # 删除每行末尾的换行符
    urls = [url.strip() for url in urls]
    
    # 插入链接到数据库表中
    insert_url_query = f"INSERT INTO `{table_name}` (url) VALUES (%s)"
    with conn.cursor() as cursor:
        for url in urls:
            cursor.execute(insert_url_query, (url,))
        conn.commit()
    
    except pymysql.Error as e:  
        print(f"Error: {e}")  
    finally:  
        if conn:  
            conn.close()  
    # 如果连接已建立，则关闭它

### 完整代码 + 数据库

请至下方链接下载

[api\_0.2.7z](/upload/api_0.2.7z)（2024.5.19更新）

[api.zip](/upload/api.zip)（老版本）

[随机图API | ACGURL](https://pan.acgurl.link/doc/%E6%BA%90%E7%A0%81%E6%95%B4%E7%90%86/%E9%9A%8F%E6%9C%BA%E5%9B%BEAPI) (暂时失效)

新建一个数据库 然后导入`api.sql`即可

**记得修改其他php文件里面的数据库连接账户密码和数据库名哦**

## 六 鸣谢

部分代码参考以下

-   [\[源码分享\]随机二次元接口源码-双版本 - 苏晓晴 - 我的生活记录 (](https://www.toubiec.cn/99.html)[toubiec.cn](http://toubiec.cn)[)](https://www.toubiec.cn/99.html)
    
-   [2374579255/acgAPI: 随机二次元API (github.com)](https://www.toubiec.cn/99.html)
    

我只是稍微优化了以下逻辑，并添加了其他图片

上传图床来自这个项目

-   [xlzy520/picgo-plugin-bilibili: 为 PicGo 开发的一款插件，新增了B站图床 图床。 使用用户动态的图片上传API。填写SESSDATA即可，获取方式在下面。 (github.com)](https://github.com/xlzy520/picgo-plugin-bilibili)