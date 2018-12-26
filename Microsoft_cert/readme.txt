################
makecert -n "CN=Root" -r -sv cert/root.pvk cert/root.cer

makecert -n "CN=www.ssl.com" -b 06/22/2018 -e 06/23/2019 -eku 1.3.6.1.5.5.7.3.1 -ss my -sr localmachine -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -$ commercial -cy authority -iv cert/root.pvk -ic cert/root.cer -sv cert/server.pvk cert/server.cer


#生成server.spc spc意思是软件发布者证书
#iis中导入证书必须为.pfx格式，所以我们需要将之前生成的server.pvk和server.cer
cert2spc cert/server.cer cert/server.spc


#将公钥证书和私钥合并成一个PFX格式的证书文件
pvk2pfx -pvk cert/server.pvk -spc cert/server.spc -pfx cert/server.pfx
################







################
#生成证书
MakeCert -n "CN=WebAPi CA" -b 09/20/2016 -e 12/31/2050 -sv d:DevelopmentCA.pvk d:DevelopmentCA.cer -r


#合并生成PFX
Pvk2pfx -pvk DevelopmentCA.pvk -spc DevelopmentCA.cer -pfx DevelopmentSSL.pfx -po password
################