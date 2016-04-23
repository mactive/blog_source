title: iOS[s01]
date: 2012-10-06 20:07:44
tags:
---
####几点纪录的

##私钥导出[多人共用一个 个人帐户开发]
1. keychain 左侧 Keys 选择导出 
2. 导出格式 *.p12  文件
3. 然后谁开发谁就导入这个 p12 就好了

![](http://img.hb.aicdn.com/67ca9af093637be97764e7b1914b6b28cdee9d9520f32-hpaZKN_fw554)


##  Request From
1. Request a Certificate From a Certificate Authority
2. 记住email 填写 appleid email
3. save to disk

![](http://img.hb.aicdn.com/6c852872c7ea901e592080972e4501c0f98286d98022-Blo4PL_fw554)


##development push

1. Provisioning Portal Appid 下 enable Push Notification:
2. 上传本地 Request From的证书
3. 下载生成的证书 cer
4. // 生成 pem 记得 生成 p12 时候记得输入密码 要不不work
// 生成完push证书之后 要renew develop 和 distribution 证书
5. openssl pkcs12 -in apns-dev-cert.p12 -out apns-dev-cert.pem -nodes -clcerts
6. // 验证 pem
7. openssl s_client -connect gateway.sandbox.push.apple.com:2195 -cert apns-dev-cert.pem -debug -showcerts -CAfile apns-dev-cert.pem
