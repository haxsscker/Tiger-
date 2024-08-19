# 前世今生

现在大多数的漏扫工具都是一股脑蜂拥而上，管你什么CMS，所有的POC都会尝试一遍。在我看来这种漏扫方式有很大的缺点：

```
1、耗时比较长。若前提我们知道目标的指纹是泛微OA，按理来说，应该只需验证泛微OA的POC即可，但所有POC都验证浪费很多时间。
2、很容易被蓝队人员发现。大批量的扫描非常容易被发现，若只是几个POC或十几个，相对几百上千个POC更不容易发觉。
```

在此背景下，Tiger应运而生，减少红队人员的时间成本和被发现的可能性。命名Tiger的原因是老虎会精准的追踪猎物、扑食猎物。

# 介绍

Tiger是一款对资产重点系统指纹识别、精准漏扫的工具。第一环节：从大量的资产中提取有用的系统(如OA、VPN、Weblogic...)；第二环节：针对收集到的资产实施精准漏扫，Tiger的漏洞库目前包含历年HW公开的POC和高危的POC。Tiger旨在帮助红队人员在信息收集期间能够快速从C段、大量杂乱的资产中精准定位到易被攻击的系统，并自动化实施精准漏扫。

![image](https://github.com/user-attachments/assets/3df0b843-69f9-4135-95b3-0dfd8ae9086c)

# 目前功能

#### 重点资产识别，内置指纹库2W+

#### 针对重点资产进行精准漏扫，内置POC库100+

#### 目录扫描

# 未来计划

#### 添加更多的POC

#### 添加FUZZ，在没有找到Nday时，启用FUZZ模式，包括主动FUZZ和被动FUZZ

```
1、主动FUZZ使用爬虫爬取，使用内置的payload测试SQL注入、XSS、文件上传、RCE等。
2、被动模式是监听burpsuite的流量，使用内置的payload测试SQL注入、XSS、文件上传、RCE等。
```

#### 朝着渗透自动化方向，只需输入单位名称就可自动化完成整个渗透过程

# 参数介绍

#### tiger.exe -h

```
Usage:
  tiger [flags]
  tiger [command]

Available Commands:
  finger      tiger的指纹识别、漏洞扫描、目录扫描模块
  fofaext     tiger的fofa提取模块
  help        Help about any command

Flags:
      --config string   config file (default is $HOME/.tiger.yaml)
  -h, --help            help for tiger
  -t, --toggle          Help message for toggle
  -v, --version         Show version information

Use "tiger [command] --help" for more information about a command
```

![image](https://github.com/user-attachments/assets/d538afd0-bf2e-4f89-94a7-5c9830ae888f)

#### tiger.exe finger -h

```
从fofa或者本地文件获取资产进行指纹识别，支持单条url识别。

Usage:
  tiger finger [flags]

Flags:
  -d, --dir             目录扫描，默认为false不开启，若为true则开启
  -f, --fip string      从fofa提取资产，进行指纹识别，仅仅支持ip或者ip段，例如：192.168.1.1 | 192.168.1.0/24
  -s, --fofa string     从fofa提取资产，进行指纹识别，支持fofa所有语法
  -h, --help            help for finger
  -a, --hip string      从hunter提取资产，进行指纹识别，仅仅支持ip或者ip段，例如：192.168.1.1 | 192.168.1.0/24
  -b, --hunter string   从hunter提取资产，进行指纹识别，支持hunter所有语法
  -l, --local string    从本地文件读取资产，进行指纹识别，支持无协议，列如：192.168.1.1:9090 | http://192.168.1.1:9090
  -o, --output string   输出所有结果，当前仅支持json和xlsx后缀的文件。
  -v, --poc             精准漏扫，默认false不开启，只进行重点资产收集；若为true则开启
  -p, --proxy string    指定访问目标时的代理，支持http代理和socks5，例如：http://127.0.0.1:8080、socks5://127.0.0.1:8080
  -t, --thread int      指纹识别线程大小。 (default 100)
  -u, --url string      识别单个目标。

Global Flags:
      --config string   config file (default is $HOME/.tiger.yaml)
```

![image](https://github.com/user-attachments/assets/39a04ae2-dd2f-4f72-b31c-4b79c2fdc6ef)

# 使用

#### 重点资产识别

1、若需要对FAFO或HUNTER的资产做指纹识别，需在config.ini中配置FAFO和HUNTER的KEY

![d5d2ff038adbc81c69dc731ba8e782d](https://github.com/user-attachments/assets/3aee75a1-daa7-444e-b55f-9c65be810ed1)

2、对FOFA收集的资产做指纹识别：

```
.\tiger.exe finger -s 'domain="hnys.edu.cn" || cname_domain="hnys.edu.cn" || cname="hnys.edu.cn" ||cert="hnys.edu.cn" || cert.subject="河南艺术职业学院" || cert.subject.org="河南艺术职业学院" ||cert.subject.cn="hnys.edu.cn" || icp="豫ICP备14014451号"'
```

![23fc95932f9299d453082b631092fb2](https://github.com/user-attachments/assets/9bf64144-f6c8-44c8-923c-08668611c6f1)


3、对本地的资产做指纹识别：

```
.\tiger.exe finger -l .\target.txt
```

![image](https://github.com/user-attachments/assets/be91fa13-8cb6-477b-a690-ef191a34d39b)

#### 精准漏扫

1、精准漏扫，默认false，需要使用时指定：`-v true` 对目标识别出来的指纹做对应的漏扫，举例若目标是泛微OA，只会使用泛微OA的POC做漏扫，减少被发现的可能性；命令：

```
.\tiger.exe finger -l .\target.txt -v true
```

![eb28798cf27a589cdfbc6f9613d5d27](https://github.com/user-attachments/assets/8ca4d123-5340-4111-adff-5001c49e8036)

#### 目录扫描

1、内置150个目录，包括常用的未授权、备份文件、sql文件等，默认为false，需要使用时指定：`-d true`，当目标是域名时，工具会自动获取"."前后的域名字符串作为目录字典，例如：https://www.baidu.com/， 工具会自动获取www.zip、www.7z、www.bak、www.tar.gz、www.rar、baidu.zip、baidu.7z、baidu.bak、baidu.tar.gz、baidu.rar；命令：

```
.\tiger.exe finger -l .\dir.txt -d true
```

![image](https://github.com/user-attachments/assets/c3dbbbc1-e6f1-4a27-a851-9062b61a760c)

#### 输出保存格式

1、保存json格式，指定参数`-o`，输出文件名`xxx.json`

```
.\tiger.exe finger -s 'domain="hnys.edu.cn" || cname_domain="hnys.edu.cn" || cname="hnys.edu.cn" ||cert="hnys.edu.cn" || cert.subject="河南艺术职业学院" || cert.subject.org="河南艺术职业学院" ||cert.subject.cn="hnys.edu.cn" || icp="豫ICP备14014451号"' -o test.json
```

![e92117723bab3e6e5e348e988bf3e3b](https://github.com/user-attachments/assets/88846d8f-b226-4b25-a8e6-060396a09819)


2、保存xlsx格式，指定参数`-o`，输出文件名`xxx.xlsx`

```
.\tiger.exe finger -s 'domain="hnys.edu.cn" || cname_domain="hnys.edu.cn" || cname="hnys.edu.cn" ||cert="hnys.edu.cn" || cert.subject="河南艺术职业学院" || cert.subject.org="河南艺术职业学院" ||cert.subject.cn="hnys.edu.cn" || icp="豫ICP备14014451号"' -o test.xlsx
```

![938a0f6f97b8a0d92b6abdea672c3af](https://github.com/user-attachments/assets/57bb453a-ae86-4dea-a115-fc4f4814b635)


# POC支持清单

| 分类       | 应用                     | 漏洞名称                                                     |
| ---------- | ------------------------ | ------------------------------------------------------------ |
| Framework  | Laravel                  | CVE-2017-16894                                               |
|            |                          | CVE-2021-3129                                                |
|            |                          | CVE-2022-40734                                               |
|            | PHP                      | PHP 8.1.0-dev 开发版本后门                                   |
|            |                          | PHP phpinfo() 信息泄露                                       |
|            |                          | PHP文件包含漏洞(利用phpinfo)                                 |
|            | Shiro                    | 默认key                                                      |
|            | Spring                   | CVE-2018-1273                                                |
|            |                          | CVE-2020-5410                                                |
|            |                          | CVE-2024-40348                                               |
|            | Thinkphp                 | CNVD-2018-24942                                              |
|            |                          | thinkphp2.x rce                                              |
|            |                          | thinkphp3.2.x rce                                            |
|            |                          | thinkphp_index_showid_rce                                    |
|            |                          | ThinkPHP5 SQL Injection Vulnerability                        |
|            |                          | thinkphp_pay_orderid_sqli                                    |
|            |                          | thinkphp_multi_sql_leak                                      |
|            |                          | thinkphp_driver_display_rce                                  |
|            |                          | thinkphp_invoke_func_code_exec                               |
|            | nodejs                   | CVE-2021-21315                                               |
|            |                          | CVE-2017-14849                                               |
| middleware | nginx                    | nginxWebUI远程命令执行                                       |
|            |                          | Nginx 解析漏洞                                               |
|            | Tomcat                   | CVE-2017-12615                                               |
|            |                          | CVE-2019-0232                                                |
|            |                          | CVE-2024-21733                                               |
|            |                          | Apache Tomcat 弱口令                                         |
|            | Weblogic                 | CVE-2014-4210                                                |
| redteam    | 用友畅捷通               | 用友畅捷通 SQL注入（site_id延时注入）                        |
|            |                          | 畅捷通T+ DownloadProxy.aspx 任意文件读取漏洞                 |
|            |                          | 用友畅捷通T+GetStoreWarehouseByStore RCE                     |
|            |                          | 畅捷通T+ RecoverPassword.aspx 管理员密码修改漏洞             |
|            | dahua                    | 大华智慧园区综合管理平台devicePoint_addImgIco任意文件上传漏洞 |
|            |                          | 大华DSS itcBulletin SQL 注入漏洞                             |
|            |                          | 大华智慧园区综合管理平台 任意文件读取漏洞                    |
|            |                          | 大华智慧园区任意密码读取漏洞                                 |
|            |                          | 大华智慧园区综合管理平台 video 任意文件上传漏洞              |
|            | druid                    | Alibaba Druid Monitor 弱口令                                 |
|            |                          | Alibaba Druid Monitor 未授权访问                             |
|            |                          | Apache Druid 未授权访问                                      |
|            | finereport               | CNVD-2018-04757                                              |
|            | H3C                      | H3C IMC dynamiccontent.properties.xhtm 远程命令执行          |
|            |                          | H3C多系列路由器前台RCE漏洞                                   |
|            | HIKVISION                | HIKVISION 综合安防管理平台env信息泄露                        |
|            |                          | 海康威视isecure center 综合安防管理平台存在任意文件上传漏洞  |
|            |                          | HIKVISION视频编码设备任意文件下载                            |
|            | 宏景eHR                  | 宏景eHR SQL注入                                              |
|            |                          | 宏景eHR文件上传                                              |
|            | 金和OA                   | 金和OA C6 UploadFileEditor.aspx存在sql注入漏洞               |
|            | 金蝶云星空               | 金蝶云星空 CommonFileserver 任意文件读取                     |
|            |                          | 金蝶云星空Kingdee- erp-Unserialize-RCE漏洞                   |
|            | 蓝凌OA                   | 蓝凌OA custom.jsp 任意文件读取漏洞                           |
|            |                          | 蓝凌OA treexml.tmpl命令执行                                  |
|            | Nacos                    | Nacos 弱密码                                                 |
|            |                          | 开启授权后identity硬编码绕过                                 |
|            |                          | jwt secret key 硬编码绕过                                    |
|            |                          | Nacos 未授权访问                                             |
|            |                          | nacos-Sync 未授权                                            |
|            | 企望制造ERP              | 企望制造ERP_comboxstore.action远程命令执行漏洞               |
|            | Hytec Inter HWL-2511-SS  | Hytec Inter HWL-2511-SS popen.cgi命令注入                    |
|            | 致远OA                   | 致远OA A6 sql注入漏洞                                        |
|            |                          | 致远OA A8 htmlofficeservlet 任意文件上传漏洞                 |
|            |                          | 致远OA getSessionList.jsp Session泄漏漏洞(后台可getshell)    |
|            |                          | 致远OA Session泄露(thirdpartyController.do)漏洞              |
|            | 深信服                   | 深信服SG上网管理系统任意文件读取                             |
|            |                          | 深信服应用交付报表系统 文件读取                              |
|            |                          | 深信服应用交付管理系统 RCE                                   |
|            | 360天擎                  | 数据库信息泄露漏洞                                           |
|            |                          | 360天擎终端安全管理系统前台SQL注入                           |
|            |                          | 天擎 rptsvr 任意文件上传漏洞                                 |
|            |                          | 360新天擎终端安全管理系统信息泄露                            |
|            | 通达OA                   | 通达OA sql注入(/general/reportshop/utils/get_datas.php)      |
|            |                          | 通达OA v11.6 insert SQL注入漏洞                              |
|            |                          | 通达OA v11.9 getdata 任意命令执行漏洞                        |
|            |                          | 通达OA v2017 video_file.php 任意文件下载漏洞                 |
|            | 泛微                     | 泛微Weaver E-Office9前台文件包含                             |
|            |                          | 泛微E-Office9文件上传漏洞                                    |
|            |                          | 泛微E-Cology9 WorkPlanService 前台SQL注入漏洞(XVE-2024-18112) |
|            |                          | 泛微运维平台存在任意管理员用户创建漏洞                       |
|            | 用友NC                   | 用友nc-cloud RCE                                             |
|            |                          | 用友NC-Cloud 远程命令执行                                    |
|            |                          | 用友GRP-U8存在信息泄露                                       |
|            |                          | 用友时空 KSOA servletimagefield 文件 sKeyvalue 参数SQL 注入  |
|            |                          | 用友 NC Cloud jsinvoke 任意文件上传                          |
|            |                          | 用友NCfileupload命令执行漏洞                                 |
|            |                          | 用友 NC NCFindWeb 任意文件读取漏洞                           |
|            |                          | NC bsh.servlet.BshServlet 远程命令执行漏洞                   |
|            | 禅道                     | 禅道16.5 SQL注入(CNVD-2022-42853)                            |
|            |                          | 禅道11.6版本任意文件读取漏洞                                 |
| web        | Chamilo                  | Chamilo additional_webservices.php RCE                       |
|            | Eramba                   | Eramba任意代码执行                                           |
|            | 红海 EHR                 | 红海 EHR 系统pc.mob sql 注入漏洞                             |
|            | LiveBOS                  | LiveBOS ShowImage.do 任意文件读取漏洞                        |
|            | Metabase                 | Metabase远程命令执行漏洞 (CVE-2023-38646)                    |
|            | Apache OFBiz             | Apache OFBiz代码执行(CVE-2024-38856) RCE                     |
|            | 1Panel                   | 1Panel loadfile后台文件读取漏洞                              |
|            | 契约锁电子签章平台       | 契约锁电子签章平台add远程命令执行漏洞                        |
|            |                          | 契约锁电子签章平台ukeysign存在远程命令执行漏洞               |
|            | Smartbi                  | Smartbi 默认用户登陆绕过漏洞                                 |
|            | 智慧校园安校易管理系统   | 智慧校园安校易管理系统FileUpAd任意文件上传漏洞               |
|            | 云时空-社会化商业ERP系统 | 云时空-社会化商业ERP系统session泄露 接管后台                 |



# 免责声明：

本软件/工具仅供教育和研究目的使用。未经授权用于非法或恶意活动的行为是严格禁止的。开发者对任何由于使用本工具而引发的误用或法律后果概不负责。使用本工具即表示您同意遵守所有适用的法律法规。请负责任和合乎道德地使用本工具。
