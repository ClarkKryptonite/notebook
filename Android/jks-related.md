# jdk的keytool生成jks和获取jks的信息,公匙
1. 生成jks。执行命令：keytool -genkeypair -alias mytest -keyalg RSA -keypass mypass -keystore mytest.jks -storepass mypass -keystore jks文件保存路径

　生成的mytest.jks证书中包含我们的密钥 ：公钥和私钥。

2. 利用`keytool -list -v -keystore test.jks`查看JKS中生成的证书的详细信息

3. 如果要导出cer证书。则利用“keytool -alias test -exportcert -keystore mytest.jks -file test.cer”，导出证书，并可以双击打开证书查看证书信息。

4. 执行如下命令：`keytool -list -rfc -keystore mytest.jks -storepass` 你的密码 ,则可以将证书信息打印到屏幕上。

5. 如果需要导出公匙。则使用下面的命令：`keytool -list -rfc --keystore mytest.jks | openssl x509 -inform pem -pubkey`
