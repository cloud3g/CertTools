一：为服务器生成证书
1. 找到jdk安装目录，运行控制台，切换到该目录：

2. 使用keytool为tomcat生成证书：tomcat.keystore;
keytool -genkey -v -alias tomcat -keyalg RSA -keystore tomcat.keystore -validity 36500


二：为客户端生成证书
1. 生成客户端证书：client.p12
keytool -genkey -v -alias client -keyalg RSA -storetype PKCS12 -keystore client.p12 -validity 36500


三：让服务器信任客户端证书
1. 将client.p12转换成client.cer；
keytool -export -alias client -keystore client.p12 -storetype PKCS12 -rfc -file client.cer

2. 将client.cer导入到tomcat的信任证书链中
keytool -import -alias client-v -file client.cer -keystore tomcat.keystore


四：让客户端信任服务器证书
1. 把tomcat证书导出为tomcat.cer文件（导出根证书）
keytool -export -v -alias tomcat -file tomcat.cer -keystore tomcat.keystore

2. 把tomcat.cer文件转换成tomcat.pem文件，并上传到IoT平台的应用(需要安装openssl)
openssl x509 -inform der -in tomcat.cer -out tomcat.pem


五：将华为提供的证书导入tomcat的信任证书链中

1. 先将化为提供的outgoing.CertwithKey.pkcs12转换成outgoing.CertwithKey.pem证书
  openssl pkcs12 -in outgoing.CertwithKey.pkcs12 -out outgoing.CertwithKey.pem

  EnterImport Password为：IoM@1234，下面两个输入密码的地方可自行设置

  再把outgoing.CertwithKey.pem导入tomcat的信任证书链
  keytool -import -v -file outgoing.CertwithKey.pem -alias huawei_out -keystore tomcat.keystore

2.先将ca.jks转换成ca.p12证书
  keytool -importkeystore -srckeystore ca.jks -destkeystore ca.p12 -srcstoretype jks -deststoretype pkcs12
  
  上面的两个输入密码的地方可自行设置， 源密钥库口令为：Huawei@123


  再把ca.p12转换成ca.pem证书
  openssl pkcs12 -in ca.p12 -out ca.pem
  
  密码为上面自己设置的目标密钥库口令

  最后把ca.pem导入tomcat的信任证书链
  keytool -import -v -file ca.pem -alias huawei_ca -keystore tomcat.keystore
  
  密码为生成服务器证书时设置的密码。

六：配置Tomcat服务器

属性说明：
clientAuth:设置是否双向验证，默认为false，设置为true代表双向验证
keystoreFile:服务器证书文件路径
keystorePass:服务器证书密码
truststoreFile:用来验证客户端证书的根证书，此例中就是服务器证书
truststorePass:根证书密码

七：测试
1. 启动tomcat服务器
找到tomcat安装目录，运行控制台，切换到该目录的bin文件夹下
执行shutdown.bat和startup.bat命令开启tomcat服务器。

2.在浏览器输入https://localhost:8443
没有安装证书的时候页面显示如下：

3.将outgoing.CertwithKey.pkcs12证书导入到个人

4.将tomcat.cer证书导入到受信任的根证书颁发机构

5.重启浏览器再次输入https://localhost:8443显示如下则证书配置成功






#### #### #### ####
流程
1 生成tomcat证书 tomcat.keystore = tomcat信任链
keytool -genkey -v -alias tomcat -keyalg RSA -keystore tomcat.keystore -validity 36500

2 生成客户端证书 client.p12 = IOT应用
keytool -genkey -v -alias client -keyalg RSA -storetype PKCS12 -keystore client.p12 -validity 36500

3 让服务器信任客户端证书    1@client.p12-client.cer--tomcat.keystore
  将client.p12转换成client.cer
  keytool -export -alias client -keystore client.p12 -storetype PKCS12 -rfc -file client.cer

  将client.cer导入到tomcat的信任证书链中
  keytool -import -alias client-v -file client.cer -keystore tomcat.keystore

  客户端证书绑定到IOT应用

4 让客户端信任服务器证书   2@tomcat.keystore--tomcat.cer-tomcat.pem
  tomcat.keystore导出tomcat.cer = 把tomcat证书导出为tomcat.cer文件
  keytool -export -v -alias tomcat -file tomcat.cer -keystore tomcat.keystore

  tomcat.cer 转tomcat.pem     = 把tomcat.cer文件转换成tomcat.pem文件，并上传到IoT平台的应用
  openssl x509 -inform der -in tomcat.cer -out tomcat.pem

  tomcat.pem上传到IoT平台 = 让IOT应用信任IOT平台


5 华为证书导入tomcat的信任证书链中 

  #1 outgoing@p12-pem-keystore
  outgoing.CertwithKey.pkcs12转换成outgoing.CertwithKey.pem
  openssl pkcs12 -in outgoing.CertwithKey.pkcs12 -out outgoing.CertwithKey.pem

  把outgoing.CertwithKey.pem导入tomcat的信任证书链 IoM@1234
  keytool -import -v -file outgoing.CertwithKey.pem -alias huawei_out -keystore tomcat.keystore

  =让IOT平台信任IOT应用


  #2 ca@jks-p12-pem-keystore
  先将ca.jks转换成ca.p12证书 Huawei@123 
  keytool -importkeystore -srckeystore ca.jks -destkeystore ca.p12 -srcstoretype jks -deststoretype pkcs12

  再把ca.p12转换成ca.pem证书
  openssl pkcs12 -in ca.p12 -out ca.pem

  最后把ca.pem导入tomcat的信任证书链
  keytool -import -v -file ca.pem -alias huawei_ca -keystore tomcat.keystore

  其中ca.jks是在客户端配置的证书，outgoing.CertwithKey.pkcs12是在服务端配置的证书
#### #### #### ####