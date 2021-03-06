# <a name="toc">目录</a>

[测试工具](#testtool)

[日志文件目录](#dir)

[日志文件格式](#format)

[logback.xml配置文件](#logback)

[框架使用到的错误码](#error)

# <a name="testtool">测试工具</a>

## 使用service runscala

	输入./service runscala  进入控制台，可直接输入代码测试类，用来测试辅助类特别方便, 如

	scala>import jvmdbbroker.core._
	scala>import jvmdbbroker.flow._
    scala>new java.util.ArrayList()
    scala>Thread.sleep(50)
    scala>jvmdbbroker.flow.Utils.checkInclude(s,"123") 测试自己写的辅助类object Utils里的checkInclude方法

## 使用service runtest

    输入./service runtest testcasefile 可自动将 testcasefile 文件中定义的test case发给scalabpe
    testcasefile 不传则默认为 testcase/default.txt文件

    runtest 工具默认将请求发给本机，端口取自 config.xml中的<SapPort>节点;
	如config.xml中配置了<TestServerAddr>host:port</TestServerAddr>, runtest工具会读此配置并发送到该地址

    testcasefile 文件格式定义:

        * 一个文件里可保存所有测试用例
        * 每行一个test case, 空行忽略
        * #开头的行为注释，忽略
        * serviceId,msgId,json串   json串对应该消息的参数
        * json串中，如果包含 x_repeat 属性，可用来指定该请求要重复发多少次，不指定则为1

## 对于使用了http server插件的服务

    可以直接通过命令行curl或浏览器来访问url

[返回](#toc)

# <a name="dir">日志文件目录</a>

## 目录结构

	/opt/logs/xxx/   	xxx为项目名称，脚本会自动取当前目录名作为项目名称
				 auditlog/
				 	request_audit.log 	对外接口的请求响应日志文件
				 	csos_audit.log 		流程的内部处理流程日志文件
                    request_stat.log  对外接口汇总日志
                    sos_stat.log   内部处理接口汇总日志
                    request_summary.log 对外请求/连接情况汇总日志
				 	http/
				 		request_audit.log 	http服务的请求响应日志文件
				 		access.log 	http服务的访问日志文件,类似nginx日志
				 log/
				 	console.log 启动信息，启动停止脚本会检查此文件的内容
				 	all.log   	框架或业务流程输出的日志
				 	root.log  	引用的其它第三方jar包输出的日志
				 	soc.log 	avenue客户端连接日志	

## 说明

    * linux下脚本默认直接在/opt/logs下建立日志目录
    * windows下/opt/目录会自动在项目所在盘符的根目录下创建
    * 在流程中可以直接使用println(..)输出信息到all.log来分析问题

[返回](#toc)

# <a name="format">日志文件格式</a>

## auditlog/request_audit.log

    该文件分割符为: 一个逗号2个空格

    示例：
        2015-11-05 00:00:03.144,
        10.129.20.129,
        43939,
        10.129.20.129,
        43939,
        -10086,
        -10087,
        10.129.20.126:38700,
        020128144665280011510000,
        1,
        55611,
        2,
        10.129.20.128,
        2015-11-05 00:00:00.115,
        3,
        ,
        ,
        ,
        ,
        ,
        appId:991001023^_^username:991001023-0-769431828^_^socId:180.112.238.183:40462^_^platform:1,
        failReason:,
        0

    格式：
        TIMESTAMP  输出日志的时间戳
        PROXY_IP 代理IP
        PROXY_PORT 代理端口
        CLIENT_IP 客户端IP
        CLIENT_PORT 客户端端口
        APPID  扩展包头的APPID
        AREAID  扩展包头的AREAID
        SOCID  客户端标识
        REQUESTID 请求ID
        DUMMY 无意义
        SERVICEID  服务号
        MSGID  消息号
        SERVER_IP 服务端IP
        RECEIVE_TIME 收到请求的的时间
        ELAPSE_TIME, 耗时，等于 TIMESTAMP - RECEIVE_TIME
        DUMMY 无意义
        DUMMY 无意义
        IDX1  索引字段1
        IDX2  索引字段2
        IDX3  索引字段3
        REQUEST_BODY 请求参数，参数之间分割符为 ^_^
        RESPONSE_BODY 响应参数，参数之间分割符为 ^_^
        RESPONSE_CODE 错误码

## auditlog/csos_audit.log 

    该文件分割符为: 一个逗号2个空格

    示例：
        2016-04-22 00:00:00.002,
        020021146125439999210000:4,
        302,
        101,
        312,
        1,
        10.129.20.22:12052,
        ,
        ,
        miscservice.phoneuserinfocontrol,
        user_client_ip:127.0.0.1^_^src_code:10^_^type:1^_^phone:^_^xid:1003260494^_^mid:1003260494^_^nickname:^_^image_id:^_^u_sex:^_^u_career:^_^u_phone:^_^u_qq:^_^u_email:^_^u_city:^_^u_birthday:^_^mail_address:^_^b_uid:^_^other_flag:,
        fail_reason:^_^mid:2165378584^_^phone:18689939034^_^nickname:�����������^_^image_id:http://gmm.sdo.com/gmm/img/8.jpg^_^u_sex:-1^_^u_career:^_^u_phone:18689939034^_^u_qq:350127202^_^u_email:^_^u_city:^_^u_birthday:1971-01-01^_^create_time:2015-12-28 21:33:09^_^homepage_header_url:^_^credit_info:{"s_sharing_hours":24,"s_fee_discount":100,"b_eval_total":0,"s_judge_hours":72,"s_credit_value":5,"b_credit_lvl":0,"s_eval_total":0,"b_eval_good":0,"s_credit_lvl":0,"s_eval_good":0,"b_wait_verify_hours":24,"b_credit_value":5}^_^b_uid:1003258903^_^is_complete:0^_^mail_address:,
        4,
        0

    格式：
        TIMESTAMP 输出日志的时间戳
        REQUESTID 请求ID
        OUTSERVICEID 外部服务号
        OUTMSGID  外部消息号
        INSERVICEID 内部服务号
        INMSGID 内部消息号
        SOS_ADDR 被调用服务的地址
        DUMMY 无意义
        DUMMY 无意义
        SERVICE_NAME 服务名.消息名
        REQUEST_BODY 请求参数，参数之间分割符为 ^_^
        RESPONSE_BODY 响应参数，参数之间分割符为 ^_^
        ELAPSE_TIME 耗时
        RESPONSE_CODE 错误码

    csos_stat.log和request_audit.log文件通过REQUESTID关联
    根据request_audit.log里的REQUESTID查找csos_stat.log就能查到该请求的所有处理过程

## auditlog/http/request_audit.log 

    该文件分割符为: 一个逗号2个空格

    示例：
        2016-06-24 09:04:04 475,   
        10.152.21.16:63292,   
        /ticketpay/listcard,   
        266,   
        9,   
        -1,   
        -1,   
        ,   
        ,   
        ,   
        channelAmount=0&rmbAmount=0&DeviceId=358239056710181&LogId=e78238ef-2c83-42b3-a6d6-713935daec5b&jsessionId=f4afd085ea2a456c90c8cdac68d81191,   
        10.152.21.55:8861,   
        13,   
        0,   
        {"data":{"listIsNotNull":0,"find":0},"return_code":0,"return_message":"success"},   
        tmstamp:0,   
        sigstat:0,   
        3e86956d61084893bb69728c74ef4797,   
        fh.sdo.com

    格式：
        TIMESTAMP  输出日志的时间戳
        CLIENT_IP 客户端IP:PORT
        HTTP_URI 路径
        SERVICEID  服务号
        MSGID  消息号
        DUMMY 无意义        
        DUMMY 无意义        
        DUMMY 无意义        
        DUMMY 无意义        
        DUMMY 无意义        
        REQUEST_BODY 请求参数
        SERVER_IPPORT 服务端IP:PORT
        ELAPSE_TIME  耗时
        RESPONSE_CODE 错误码
        CONTENT 响应内容
        DUMMY 无意义        
        DUMMY 无意义   
        REQUESTID 请求ID
        HTTP_HOST 域名                

## auditlog/http/access.log 

    该文件分割符为: tab字符

    示例：
        2016-06-24 09:03:00.018 53      
        10.152.21.16:63164      
        10.152.21.16:63164      
        GET     
        fh.sdo.com      
        /fh/pay/entrance        
        token=T6DE8D398FFCF4C6690A8A051BB0FD117&sign= ...       
        302     
        0  
        Mozilla/5.0 (Linux; Android 5.1.1; Nexus 5 Build ...

    格式：
        TIMESTAMP  输出日志的时间戳
        CLIENT_IP 客户端IP:PORT
        PROXY_IP 代理IP:PORT
        HTTP_METHOD 方法
        HTTP_HOST  域名
        HTTP_URI  路径
        PARAMETERS 参数
        HTTP CODE  HTTP状态码
        CONTENT_LENGTH  输出长度
        USER_AGENT 用户代理

## auditlog/request_stat.log

    示例：2014-11-24 00:00:00,  301,  55,  127.0.0.1:10070,  1,  0,  0,  1,  0,  0,  0,  0
    格式：时间（1分钟输出一次，无数据不输出），服务号，消息号，客户端连接标识，成功数，失败数，10ms数，50ms数， 250ms数， 1秒数，3秒数，其它数

## auditlog/sos_stat.log

    示例：2014-11-24 00:02:00,  350,  2,  12,  0,  0,  12,  0,  0,  0,  0,  0
    格式：时间（1分钟输出一次，无数据不输出），服务号，消息号，成功数，失败数，10ms数，50ms数， 150ms数，250ms数， 1秒数，其它数, 超时数(-10242504数量)

## auditlog/request_summary.log

    示例：2014-11-24 00:00:00,  request[1/1/0],  client[0/0/17]
    格式：时间（1分钟输出一次，无数据不输出），request[总数/成功数/失败数] 客户端连接[新建连接数/断开连接数/当前连接数]

[返回](#toc)

# <a name="logback">logback.xml配置文件</a>

## 请求响应日志文件 request_audit.log 输出配置

	默认日志以info级别输出，如果要关闭，可以设置为warn
    <logger name="jvmdbbroker.RequestLog" level="info" additivity="false"><appender-ref ref="REQUESTLOG" /></logger>

	如果要关闭某个服务号的日志输出，如108服务号, 可如下配置
    <logger name="jvmdbbroker.RequestLog.108" level="warn" additivity="false"><appender-ref ref="REQUESTLOG" /></logger>

	如果要关闭某个服务号下某个特定消息的日志输出，如108服务号201消息号，可如下配置
    <logger name="jvmdbbroker.RequestLog.108.201" level="warn" additivity="false"><appender-ref ref="REQUESTLOG" /></logger>

## 流程内部调用日志文件 csos_audit.log 输出配置
	
	默认日志以info级别输出，如果要关闭，可以设置为warn
    <logger name="jvmdbbroker.CsosLog" level="info" additivity="false"><appender-ref ref="CSOSLOG" /></logger>

    如果要关闭某个服务号的日志输出，如231服务号, 可如下配置
    <logger name="jvmdbbroker.CsosLog.231" level="info" additivity="false"><appender-ref ref="CSOSLOG" /></logger>

    如果要关闭某个服务号下某个特定消息的日志输出，如231服务号12消息号，可如下配置
    <logger name="jvmdbbroker.CsosLog.231.12" level="info" additivity="false"><appender-ref ref="CSOSLOG" /></logger>

## HTTP SERVER请求响应日志文件 http/request_audit.log 输出配置

	默认日志以info级别输出，如果要关闭，可以设置为warn
    <logger name="jvmdbbroker.HttpRequestLog" level="info" additivity="false"><appender-ref ref="HTTPREQUESTLOG" /></logger>

	如果要关闭某个服务号的日志输出，如108服务号, 可如下配置
    <logger name="jvmdbbroker.HttpRequestLog.108" level="warn" additivity="false"><appender-ref ref="HTTPREQUESTLOG" /></logger>

	如果要关闭某个服务号下某个特定消息的日志输出，如108服务号201消息号，可如下配置
    <logger name="jvmdbbroker.HttpRequestLog.108.201" level="warn" additivity="false"><appender-ref ref="HTTPREQUESTLOG" /></logger>

## 插件的日志输出

	框架源码中很多插件的日志是以debug级别输出, 由于log/all.log默认是info级别，
    日志实际并不会输出，若要看到这些日志需调整为debug级别

    查看DB请求的SQL和实际参数，需设置<logger name="jvmdbbroker.plugin.DbClient" level="debug" ...

    查看MemCache实际的KEY,VALUE内容，需设置<logger name="jvmdbbroker.plugin.MemCacheClient" level="debug" ...

    查看REDIS实际的KEY,VALUE内容，需设置<logger name="jvmdbbroker.plugin.RedisClient" level="debug" ...

    查看AHT请求和响应原始内容，需设置<logger name="jvmdbbroker.plugin.http.HttpClientImpl" level="debug" ...

## logVar
    
    request_audit.log日志文件中只有入参出参，如果希望将流程变量或中间变量也加入该日志，
    可使用logVar(key,value)方法

[返回](#toc)

# <a name="error">框架使用到的错误码</a>

## 错误码分类

    编解码错误 -10242400
    网络错误  -10242404
    服务或消息未找到 -10242405
    队列满或队列阻塞引起的超时 -10242488
    内部错误 -10242500
    超时  -10242504

    缓存中数据未找到 -10245404

## 说明

    * 所有的错误码都在 src/core.scala/ResultCodes 类里进行定义
    * 框架里将所有出现的错误码都归类到上述错误码之一
    * 对于数据库查询未找到匹配数据的情况，不会返回错误 -10245404，而是返回0； 
      流程中应根据rowcount是否为0来判断是否有匹配数据

[返回](#toc)