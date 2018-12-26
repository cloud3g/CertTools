#######################################################################
�����Լ�������֤��root
openssl genrsa -des3 -out root.key
openssl req -new -key root.key -out root.csr
openssl x509 -req -days 3650 -sha1 -extensions v3_ca -signkey root.key -in root.csr -out root.crt

####

openssl genrsa -des3 -out server.key 2048
openssl req -new -key server.key -out server.req
openssl x509 -req -days 730 -sha1 -extensions v3_req -CA root.crt -CAkey root.key -CAserial root.srl -CAcreateserial -in server.csr -out server.crt
openssl pkcs12 -export -in server.crt -inkey server.key -out server.pfx



#######################################################################
####1�����ɸ�֤�鼰��ǩ��֤��

1.������֤˽Կ
openssl genrsa -out root-key.key 1024

2.������֤�������ļ�
openssl req -new -out root-req.csr -key root-key.key -keyform PEM

3.��ǩ��֤��
demo
    openssl x509 -req -extfile /etc/pki/tls/openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
V3
    openssl x509 -req -extfile D:\server\service\OpenSSL\bin\cnf\openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
    openssl x509 -req -extfile openssl.cnf -extensions v3_req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
V1
    openssl x509 -req -extensions v3_ca  -in root-req.csr -out root-cerY.cer -signkey root-key.key -days 3650 -sha1

��Ҫ˵�� -extfile /etc/pki/tls/openssl.cnf -extensions v3_req ���������� X509 V3 �汾��֤��ı�Ҫ���� /etc/pki/tls/openssl.cnf ��ϵͳ�Դ���OpenSSL�����ļ� �������ļ�Ĭ�Ͽ��� X509 V3 ��ʽ����ͬ

4.����p12��ʽ��֤��
openssl pkcs12 -export -clcerts -in root-cert.cer -inkey root-key.key -out root.p12


####2��ʹ�ø�֤��ǩ���ͻ���֤��

1.���ɿͻ���key
openssl genrsa -out client-key.key 1024

2.���ɿͻ��������ļ�
openssl req -new -out client-req.csr -key client-key.key

3.���ɿͻ���֤��,ʹ�ø�֤��ǩ��
openssl x509 -req -extfile /etc/pki/tls/openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650
openssl x509 -req -extfile D:\server\service\OpenSSL\bin\cnf\openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650
openssl x509 -req -extfile openssl.cnf -extensions v3_req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer -CAkey root-key.key -CAcreateserial -days 3650


4.���ɿͻ���p12��ʽ��֤��
openssl pkcs12 -export -clcerts -in client-cert.cer -inkey client-key.key -out client.p12


cerתpfx
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
#ע�����IP.1�����ã�IP��ַ��Ҫ����ķ������ļ�����ַһ�� DNSΪserver��ַ
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
1.1 ����CA˽��
openssl genrsa -out icatchtek.key 2048

1.2 ����CA֤������
openssl req -new -key icatchtek.key -out icatchtek.csr
��������Ҫ������ø�֤�������Ϣ 
PS:֤�������Ƕ�ǩ����������Ҫʹ��˽Կ����ǩ��

1.3 ����CA��֤��
openssl x509 -req -in icatchtek.csr -extensions v3_ca -signkey icatchtek.key -out icatchtek.crt

####
�Խ�server��֤��
2.1 ����server˽��
openssl genrsa -out smarthome_server.key 2048

2.2 ����server֤������
openssl req -new -key smarthome_server.key -out smarthome_server.csr

2.3 ����server֤��

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
#ע�����IP.1�����ã�IP��ַ��Ҫ����ķ������ļ�����ַһ�� DNSΪserver��ַ
IP.1 = 127.0.0.1
DNS.1 = localhost


openssl x509 -days 365 -req -in smarthome_server.csr -extensions  v3_req -CAkey icatchtek.key -CA icatchtek.crt -CAcreateserial -out smarthome_server.crt  -extfile openssl.cnf

#######################################################################