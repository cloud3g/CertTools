һ��Ϊ����������֤��
1. �ҵ�jdk��װĿ¼�����п���̨���л�����Ŀ¼��

2. ʹ��keytoolΪtomcat����֤�飺tomcat.keystore;
keytool -genkey -v -alias tomcat -keyalg RSA -keystore tomcat.keystore -validity 36500


����Ϊ�ͻ�������֤��
1. ���ɿͻ���֤�飺client.p12
keytool -genkey -v -alias client -keyalg RSA -storetype PKCS12 -keystore client.p12 -validity 36500


�����÷��������οͻ���֤��
1. ��client.p12ת����client.cer��
keytool -export -alias client -keystore client.p12 -storetype PKCS12 -rfc -file client.cer

2. ��client.cer���뵽tomcat������֤������
keytool -import -alias client-v -file client.cer -keystore tomcat.keystore


�ģ��ÿͻ������η�����֤��
1. ��tomcat֤�鵼��Ϊtomcat.cer�ļ���������֤�飩
keytool -export -v -alias tomcat -file tomcat.cer -keystore tomcat.keystore

2. ��tomcat.cer�ļ�ת����tomcat.pem�ļ������ϴ���IoTƽ̨��Ӧ��(��Ҫ��װopenssl)
openssl x509 -inform der -in tomcat.cer -out tomcat.pem


�壺����Ϊ�ṩ��֤�鵼��tomcat������֤������

1. �Ƚ���Ϊ�ṩ��outgoing.CertwithKey.pkcs12ת����outgoing.CertwithKey.pem֤��
  openssl pkcs12 -in outgoing.CertwithKey.pkcs12 -out outgoing.CertwithKey.pem

  EnterImport PasswordΪ��IoM@1234������������������ĵط�����������

  �ٰ�outgoing.CertwithKey.pem����tomcat������֤����
  keytool -import -v -file outgoing.CertwithKey.pem -alias huawei_out -keystore tomcat.keystore

2.�Ƚ�ca.jksת����ca.p12֤��
  keytool -importkeystore -srckeystore ca.jks -destkeystore ca.p12 -srcstoretype jks -deststoretype pkcs12
  
  �����������������ĵط����������ã� Դ��Կ�����Ϊ��Huawei@123


  �ٰ�ca.p12ת����ca.pem֤��
  openssl pkcs12 -in ca.p12 -out ca.pem
  
  ����Ϊ�����Լ����õ�Ŀ����Կ�����

  ����ca.pem����tomcat������֤����
  keytool -import -v -file ca.pem -alias huawei_ca -keystore tomcat.keystore
  
  ����Ϊ���ɷ�����֤��ʱ���õ����롣

��������Tomcat������

����˵����
clientAuth:�����Ƿ�˫����֤��Ĭ��Ϊfalse������Ϊtrue����˫����֤
keystoreFile:������֤���ļ�·��
keystorePass:������֤������
truststoreFile:������֤�ͻ���֤��ĸ�֤�飬�����о��Ƿ�����֤��
truststorePass:��֤������

�ߣ�����
1. ����tomcat������
�ҵ�tomcat��װĿ¼�����п���̨���л�����Ŀ¼��bin�ļ�����
ִ��shutdown.bat��startup.bat�����tomcat��������

2.�����������https://localhost:8443
û�а�װ֤���ʱ��ҳ����ʾ���£�

3.��outgoing.CertwithKey.pkcs12֤�鵼�뵽����

4.��tomcat.cer֤�鵼�뵽�����εĸ�֤��䷢����

5.����������ٴ�����https://localhost:8443��ʾ������֤�����óɹ�






#### #### #### ####
����
1 ����tomcat֤�� tomcat.keystore = tomcat������
keytool -genkey -v -alias tomcat -keyalg RSA -keystore tomcat.keystore -validity 36500

2 ���ɿͻ���֤�� client.p12 = IOTӦ��
keytool -genkey -v -alias client -keyalg RSA -storetype PKCS12 -keystore client.p12 -validity 36500

3 �÷��������οͻ���֤��    1@client.p12-client.cer--tomcat.keystore
  ��client.p12ת����client.cer
  keytool -export -alias client -keystore client.p12 -storetype PKCS12 -rfc -file client.cer

  ��client.cer���뵽tomcat������֤������
  keytool -import -alias client-v -file client.cer -keystore tomcat.keystore

  �ͻ���֤��󶨵�IOTӦ��

4 �ÿͻ������η�����֤��   2@tomcat.keystore--tomcat.cer-tomcat.pem
  tomcat.keystore����tomcat.cer = ��tomcat֤�鵼��Ϊtomcat.cer�ļ�
  keytool -export -v -alias tomcat -file tomcat.cer -keystore tomcat.keystore

  tomcat.cer תtomcat.pem     = ��tomcat.cer�ļ�ת����tomcat.pem�ļ������ϴ���IoTƽ̨��Ӧ��
  openssl x509 -inform der -in tomcat.cer -out tomcat.pem

  tomcat.pem�ϴ���IoTƽ̨ = ��IOTӦ������IOTƽ̨


5 ��Ϊ֤�鵼��tomcat������֤������ 

  #1 outgoing@p12-pem-keystore
  outgoing.CertwithKey.pkcs12ת����outgoing.CertwithKey.pem
  openssl pkcs12 -in outgoing.CertwithKey.pkcs12 -out outgoing.CertwithKey.pem

  ��outgoing.CertwithKey.pem����tomcat������֤���� IoM@1234
  keytool -import -v -file outgoing.CertwithKey.pem -alias huawei_out -keystore tomcat.keystore

  =��IOTƽ̨����IOTӦ��


  #2 ca@jks-p12-pem-keystore
  �Ƚ�ca.jksת����ca.p12֤�� Huawei@123 
  keytool -importkeystore -srckeystore ca.jks -destkeystore ca.p12 -srcstoretype jks -deststoretype pkcs12

  �ٰ�ca.p12ת����ca.pem֤��
  openssl pkcs12 -in ca.p12 -out ca.pem

  ����ca.pem����tomcat������֤����
  keytool -import -v -file ca.pem -alias huawei_ca -keystore tomcat.keystore

  ����ca.jks���ڿͻ������õ�֤�飬outgoing.CertwithKey.pkcs12���ڷ�������õ�֤��
#### #### #### ####