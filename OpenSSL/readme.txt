#######################################################################
首先自己创建根证书root
openssl genrsa -des3 -out root.key
openssl req -new -key root.key -out root.csr
openssl x509 -req -days 3650 -sha1 -extensions v3_ca -signkey root.key -in root.csr -out root.crt

####

openssl genrsa -des3 -out server.key 2048
openssl req -new -key server.key -out server.req
openssl x509 -req -days 730 -sha1 -extensions v3_req -CA root.crt -CAkey root.key -CAserial root.srl -CAcreateserial -in server.csr -out server.crt
openssl pkcs12 -export -in server.crt -inkey server.key -out server.pfx



#######################################################################
####1、生成根证书及自签名证书

1.创建根证私钥
openssl genrsa -out root-key.key 1024

2.创建根证书请求文件
openssl req -new -out root-req.csr -key root-key.key -keyform PEM

3.自签根证书
demo
    openssl x509 -req -extfile /etc/pki/tls/openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
V3
    openssl x509 -req -extfile D:\server\service\OpenSSL\bin\cnf\openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
    openssl x509 -req -extfile openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
V1
    openssl x509 -req -extensions v3_ca  -in root-req.csr -out root-cerY.cer -signkey root-key.key -days 3650 -sha1

重要说明 -extfile /etc/pki/tls/openssl.cnf -extensions v3_req 参数是生成 X509 V3 版本的证书的必要条件 /etc/pki/tls/openssl.cnf 是系统自带的OpenSSL配置文件 该配置文件默认开启 X509 V3 格式。下同

4.导出p12格式根证书
openssl pkcs12 -export -clcerts -in root-cert.cer -inkey root-key.key -out root.p12


####2、使用根证书签发客户端证书

1.生成客户端key
openssl genrsa -out client-key.key 1024

2.生成客户端请求文件
openssl req -new -out client-req.csr -key client-key.key

3.生成客户端证书,使用根证书签名
openssl x509 -req -extfile /etc/pki/tls/openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650
openssl x509 -req -extfile D:\server\service\OpenSSL\bin\cnf\openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650
openssl x509 -req -extfile openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650


4.生成客户端p12格式根证书
openssl pkcs12 -export -clcerts -in client-cert.cer -inkey client-key.key -out client.p12


cer转pfx
openssl pkcs12 -export -out certificate.pfx -inkey client-key.key -in client-cert.cer -certfile root-cert.cer
openssl pkcs12 -export -out iot.pfx -inkey client-key.key -in client-cert.cer


####openssl.cnf
[req]  
distinguished_name = req_distinguished_name  
req_extensions = v3_req  

[req_distinguished_name]  
countryName = CN  
countryName_default = CN  
stateOrProvinceName = GuangDong
stateOrProvinceName_default = GD  
localityName = ShenZhen  
localityName_default = SZ
organizationalUnitName  = localhost
organizationalUnitName_default  = localhost
commonName = localhost
commonName_max  = 64  

[ v3_req ]  
# Extensions to add to a certificate request  
basicConstraints = CA:FALSE  
keyUsage = nonRepudiation, digitalSignature, keyEncipherment  
subjectAltName = @alt_names  

[alt_names]  
#注意这个IP.1的设置，IP地址需要和你的服务器的监听地址一样 DNS为server网址
IP.1 = 127.0.0.1
DNS.1 = localhost
#######################################################################


root-key.key
root-key.key => root-req.csr
root-key.key + root-req.csr => root-cert.cer #
root-key.key + root-cert.cer => root.p12     #

client-key.key
client-key.key => client-req.csr
client-key.key + client-req.csr +  root-cert.cer + root-key.key => client-cert.cer #
client-key.key + client-cert.cer => client.p12


crt=cer

openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt
openssl pkcs12 -export -out server.pfx      -inkey server.key     -in server.crt

#######################################################################


commonName=qs.888.qq.com 
organizationalUnitName=R&D 
organizationName=Shenzhen Tencent Computer Systems Company Limited 
localityName=SHENZHEN 
stateOrProvinceName=GUANGDONG 
countryName=CN




















#######################################################################
1.1 生成CA私匙
openssl genrsa -out icatchtek.key 2048

1.2 生成CA证书请求
openssl req -new -key icatchtek.key -out icatchtek.csr
过程中需要你输入该根证书相关信息 
PS:证书请求是对签名的请求，需要使用私钥进行签名

1.3 生成CA根证书
openssl x509 -req -in icatchtek.csr -extensions v3_ca -signkey icatchtek.key -out icatchtek.crt

####
自建server端证书
2.1 生成server私匙
openssl genrsa -out smarthome_server.key 2048

2.2 生成server证书请求
openssl req -new -key smarthome_server.key -out smarthome_server.csr

2.3 生成server证书

openssl.cnf

[req]  
distinguished_name = req_distinguished_name  
req_extensions = v3_req  

[req_distinguished_name]  
countryName = Country Name (2 letter code)  
countryName_default = CN  
stateOrProvinceName = State or Province Name (full name)  
stateOrProvinceName_default = SiChuan  
localityName = ChengDu (eg, city)  
localityName_default = xx.xx
organizationalUnitName  = xxxxxx
organizationalUnitName_default  = Domain Control Validated  
commonName = Internet Widgits Ltd  
commonName_max  = 64  

[ v3_req ]  
# Extensions to add to a certificate request  
basicConstraints = CA:FALSE  
keyUsage = nonRepudiation, digitalSignature, keyEncipherment  
subjectAltName = @alt_names  

[alt_names]  
#注意这个IP.1的设置，IP地址需要和你的服务器的监听地址一样 DNS为server网址
IP.1 = 127.0.0.1
DNS.1 = localhost


openssl x509 -days 365 -req -in smarthome_server.csr -extensions  v3_req -CAkey icatchtek.key -CA icatchtek.crt -CAcreateserial -out smarthome_server.crt  -extfile openssl.cnf

#######################################################################