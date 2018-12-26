################
makecert -n "CN=Root" -r -sv cert/root.pvk cert/root.cer

makecert -n "CN=www.ssl.com" -b 06/22/2018 -e 06/23/2019 -eku 1.3.6.1.5.5.7.3.1 -ss my -sr localmachine -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -$ commercial -cy authority -iv cert/root.pvk -ic cert/root.cer -sv cert/server.pvk cert/server.cer


#����server.spc spc��˼�����������֤��
#iis�е���֤�����Ϊ.pfx��ʽ������������Ҫ��֮ǰ���ɵ�server.pvk��server.cer
cert2spc cert/server.cer cert/server.spc


#����Կ֤���˽Կ�ϲ���һ��PFX��ʽ��֤���ļ�
pvk2pfx -pvk cert/server.pvk -spc cert/server.spc -pfx cert/server.pfx
################







################
#����֤��
MakeCert -n "CN=WebAPi CA" -b 09/20/2016 -e 12/31/2050 -sv d:DevelopmentCA.pvk d:DevelopmentCA.cer -r


#�ϲ�����PFX
Pvk2pfx -pvk DevelopmentCA.pvk -spc DevelopmentCA.cer -pfx DevelopmentSSL.pfx -po password
################