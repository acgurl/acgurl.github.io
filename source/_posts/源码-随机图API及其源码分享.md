---
title: '[源码]随机图API及其源码分享'
date: 2024-10-19 14:52:44
tags:
---
<h2 style="" id="%E4%B8%80-%E6%A6%82%E8%BF%B0">一		概述</h2><p style="">这是一个简单的随机图API</p><ul><li><p style="">目前收图 910 (最近更新2024.5.19)</p></li><li><p style=""><code>原神</code>、<code>崩坏:星穹铁道</code>主题</p></li><li><p style="">上传至B站公共静态资源库</p></li><li><p style="">MySQL数据库存储图片链接</p></li><li><p style="">自动识别图片格式为<code>jpg</code>或<code>png</code></p></li><li><p style="">自适应识别用户端UA</p></li><li><p style="">多种返回方式<code>301跳转</code>、<code>不跳转直接返回图片</code>、<code>json</code></p></li></ul><h2 style="" id="%E4%BA%8C-%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95">二		使用方法</h2><p style=""><strong>仅支持 </strong><code>GET</code><strong> 方法</strong></p><p style="">下列参数均为可选项</p><blockquote><p style="">style</p></blockquote><ul><li><p style="">见图片样式</p></li></ul><blockquote><p style=""><strong>return</strong></p></blockquote><ul><li><p style="">json: 返回json</p></li><li><p style=""><s>img: 不跳转直接返回图片（已作废）</s></p></li><li><p style="">url: 301跳转至图片链接</p></li></ul><blockquote><p style="">JSON数据</p></blockquote><pre><code>{
"url":"https://i0.hdslb.com/bfs/article/3538b2f3b8899b8f95161f4f0a6807433493083985480649.jpg",
#图片地址
"width": 6160,
#图片宽
"height": 3800,
#图片高
"code":200,
#是否可以正常访问
}</code></pre><h2 style="" id="%E4%B8%89-%E5%9B%BE%E7%89%87%E6%A0%B7%E5%BC%8F">三		图片样式</h2><p style=""><code>格式：(图像原链接)@(\d+[whsepqoc]_?)*(.(|webp|gif|png|jpg|jpeg))?</code><span style="font-size: 16px; color: rgb(230, 237, 243)">$</span></p><div style="overflow-x: auto; overflow-y: hidden;"><table style="width: 291px"><colgroup><col style="width: 144px"><col style="width: 147px"></colgroup><tbody><tr style="box-sizing: border-box; background-color: var(--bgColor-default, var(--color-canvas-default)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><th colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; font-weight: var(--base-text-weight-semibold, 600); border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">Type</p></th><th colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; font-weight: var(--base-text-weight-semibold, 600); border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">Url</p></th></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-default, var(--color-canvas-default)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">原图</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/1.jpg</p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-muted, var(--color-canvas-subtle)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">原分辨率，质量压缩</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@1e_1c.jpg</u></p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-default, var(--color-canvas-default)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">规定宽，高度自适应，质量压缩</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@104w_1e_1c.jpg</u></p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-muted, var(--color-canvas-subtle)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">规定高，宽度自适应，质量压缩</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@104h_1e_1c.jpg</u></p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-default, var(--color-canvas-default)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">规定高宽，质量压缩</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@104w_104h_1e_1c.jpg</u></p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-muted, var(--color-canvas-subtle)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">原分辨率，webp格式(占用最小)</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@104w_104h_1e_1c.webp</u></p></td></tr><tr style="box-sizing: border-box; background-color: var(--bgColor-default, var(--color-canvas-default)); border-top: 1px solid var(--borderColor-muted, var(--color-border-muted));"><td colspan="1" rowspan="1" colwidth="144" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">规定高度，webp格式(占用最小)</p></td><td colspan="1" rowspan="1" colwidth="147" style="box-sizing: border-box; padding: 6px 13px; border: 1px solid var(--borderColor-default, var(--color-border-default));"><p style="">baseURL/<u>1.jpg@104w_104h_1e_1c.webp</u></p></td></tr></tbody></table></div><ul><li><p style="">w:[1, 9223372036854775807] (width，图像宽度)</p></li><li><p style="">h:[1, 9223372036854775807] (height，图像高度)</p></li><li><p style="">s:[1, 9223372036854775807] (作用未知)</p></li><li><p style="">e:[0,2] (resize，0:保留比例取其小，1:保留比例取其大，2:不保留原比例，不与c混用)</p></li><li><p style="">p:[1,1000] (默认100，放大倍数，不与c混用)</p></li><li><p style="">q:[1,100] (quality，默认75，图像质量)</p></li><li><p style="">o:[0,1] (作用未知)</p></li><li><p style="">c:[0,1] (clip，0:默认，1:裁剪)</p></li><li><p style="">webp,png,jpeg,gif(不加则保留原格式)</p></li><li><p style="">不区分大小写，相同的参数后面覆盖前面</p></li><li><p style="">计算后的实际w<em>h不能大于原w</em>h，否则wh参数失效</p></li></ul><h2 style="" id="%E5%9B%9B-%E5%88%86%E7%B1%BB%E9%93%BE%E6%8E%A5">四		分类链接</h2><p style="">原神自适应</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/ys.php" target="_blank">https://api.acgurl.link/ys.php</a></p></blockquote><p style="">原神竖屏</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/yss.php" target="_blank">https://api.acgurl.link/yss.php</a></p></blockquote><p style="">原神横屏</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/ysh.php" target="_blank">https://api.acgurl.link/ysh.php</a></p></blockquote><p style="">崩坏:星穹铁道自适应</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/xq.php" target="_blank">https://api.acgurl.link/xq.php</a></p></blockquote><p style="">崩坏:星穹铁道竖屏</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/xqs.php" target="_blank">https://api.acgurl.link/xqs.php</a></p></blockquote><p style="">崩坏:星穹铁道横屏</p><blockquote><p style=""><a rel="noopener noreferrer nofollow" href="https://api.acgurl.link/xqh.php" target="_blank">https://api.acgurl.link/xqh.php</a></p></blockquote><h2 style="" id="%E4%BA%94-%E6%BA%90%E7%A0%81">五		源码</h2><h3 style="" id="php%E5%90%8E%E7%AB%AF%E4%BB%A3%E7%A0%81">PHP后端代码</h3><pre><code class="language-php">&lt;?php
// 建立与MySQL数据库的连接
$host = '主机地址';
$user = '用户名';
$pass = '密码';
$dbName = '数据库名';
$conn = new mysqli($host, $user, $pass, $dbName);

// 检查数据库连接是否成功
if ($conn-&gt;connect_error) {
    die("数据库连接失败: " . $conn-&gt;connect_error);
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
$result = $conn-&gt;query($sql);

// 检查查询结果是否有效
if ($result-&gt;num_rows &gt; 0) {
    // 获取随机图片链接
    $row = $result-&gt;fetch_assoc();
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
                'url' =&gt; $randomUrl,
                'width' =&gt; $imageSize[0],
                'height' =&gt; $imageSize[1],
                'code' =&gt; $statusCode
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
$conn-&gt;close();
?&gt;</code></pre><h3 style="" id="%E9%99%84%E5%8A%A0">附加</h3><p style="">使用<code>Python</code>从<code>TXT</code>中导入链接到<code>MYSQL</code>数据库中</p><p style=""><mark data-color="#ef4444" style="background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; display: inline-block;">注意：</mark><strong><mark data-color="#ef4444" style="background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; background-color: #ef4444; color: inherit; display: inline-block;">需要安装pymysql模块</mark></strong></p><pre><code>import pymysql

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
# 如果连接已建立，则关闭它</code></pre><h3 style="" id="%E5%AE%8C%E6%95%B4%E4%BB%A3%E7%A0%81-%2B-%E6%95%B0%E6%8D%AE%E5%BA%93">完整代码 + 数据库</h3><p style="">请至下方链接下载</p><p style=""><a href="/upload/api_0.2.7z">api_0.2.7z</a>（2024.5.19更新）</p><p style=""><a rel="noopener noreferrer nofollow" href="/upload/api.zip" target="_blank">api.zip</a>（老版本）</p><p style=""><a rel="noopener noreferrer nofollow" href="https://pan.acgurl.link/doc/%E6%BA%90%E7%A0%81%E6%95%B4%E7%90%86/%E9%9A%8F%E6%9C%BA%E5%9B%BEAPI" target="_blank"><s>随机图API | ACGURL </s></a><s>(暂时失效)</s></p><p style="">新建一个数据库 然后导入<code>api.sql</code>即可</p><p style="text-align: start; "><strong>记得修改其他php文件里面的数据库连接账户密码和数据库名哦</strong></p><h2 style="" id="%E5%85%AD-%E9%B8%A3%E8%B0%A2">六		鸣谢</h2><p style="">部分代码参考以下</p><ul><li><p style=""><a rel="noopener noreferrer nofollow" href="https://www.toubiec.cn/99.html" target="_blank">[源码分享]随机二次元接口源码-双版本 - 苏晓晴 - 我的生活记录 (</a><a rel="noopener noreferrer nofollow" href="http://toubiec.cn" target="_blank">toubiec.cn</a><a rel="noopener noreferrer nofollow" href="https://www.toubiec.cn/99.html" target="_blank">)</a></p></li><li><p style=""><a rel="noopener noreferrer nofollow" href="https://www.toubiec.cn/99.html" target="_blank">2374579255/acgAPI: 随机二次元API (github.com)</a></p></li></ul><p style="">我只是稍微优化了以下逻辑，并添加了其他图片</p><p style="">上传图床来自这个项目</p><ul><li><p style=""><a href="https://github.com/xlzy520/picgo-plugin-bilibili">xlzy520/picgo-plugin-bilibili: 为 PicGo 开发的一款插件，新增了B站图床 图床。 使用用户动态的图片上传API。填写SESSDATA即可，获取方式在下面。 (github.com)</a></p></li></ul><p style=""></p>