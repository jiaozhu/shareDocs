# SSL证书配置笔记

> 关于在Tomcat/WebLogic中配置SSL的过程

## 理论知识

### 私钥、数字证书和可信证书颁发机构

- 私钥、数字证书和可信证书颁发机构可建立和验证服务器标识和信任。
- SSL 使用公钥加密技术进行身份验证。使用公钥加密时，将为一台服务器生成一个公钥和一个私钥。对于用公钥加密的数据，只能用相应的私钥解密；对于用私钥加密的数据，只能用相应的公钥解密。私钥受到妥善的保护，确保只有私钥的所有者才能解密用公钥加密的消息
- 公钥嵌入一个数字证书中，同时嵌入的还有描述公钥所有者的其他信息，如姓名、街道地址和电子邮件地址。私钥和数字证书可为服务器提供标识。 
- 嵌入数字证书中的数据由证书颁发机构进行验证，并由证书颁发机构的数字证书进行数字签名。

### 配置标识和信任主要步骤

1. 从 CertGen 实用工具、Sun Microsystem 的 keytool 实用工具，或著名的供应商如 Entrust 或 Verisign 处获取数字证书、私钥和可信 CA 证书。也可使用由 WebLogic Server 工具包提供的数字证书、私钥和可信 CA 证书。只应将演示数字证书、私钥和可信 CA 证书用在开发环境中
2. 存储私钥、数字证书和可信 CA 证书。私钥和可信 CA 证书存储在密钥库中。
3. 在 WebLogic Server 管理控制台中配置 WebLogic Server 的标识和信任密钥库。

### 标识和信任的受支持格式

- 保密邮件（Privacy Enhanced Mail，简称 PEM）格式是私钥、数字证书和可信证书颁发机构（Certificate Authorities，简称 CA）的首选格式。首选的密钥库格式为 Java 密钥库（Java KeyStore，简称 JKS）格式。
- .pem 格式的文件可支持多种数字证书（如可包含证书链）。证书在文件中的顺序非常重要。服务器的数字证书应为文件中的第一个数字证书，其后是发行方证书，等等。链中的每个证书后都跟有其发行方证书。如果链中最后一个证书为链的自签名（自发行）根证书，则将此链视为完整的链。请注意，链并不一定是完整的。
- 在使用不赞成的基于文件的私钥、数字证书和可信 CA 时，WebLogic Server 可使用 PEM 或识别编码规则（Distinguished Encoding Rules，简称 DER）格式的数字证书。
- .der 格式的文件包含了单个证书的二进制数据。因此，一个 .der 文件仅可用于单个证书，而一个 .pem 文件可用于多个证书。
- Microsoft 是常用的证书颁发机构。Microsoft 发行的可信 CA 证书采用 p7b 格式，必须将其转换为 PEM 格式才能在 WebLogic Server 中使用。有关详细信息，请参阅将 Microsoft p7b 格式转换为 PEM 格式。

## 配置过程

### 针对WebLogic Server

- 生成密钥库文件

```
keytool -genkey -alias server -keyalg RSA -keysize 2048 -keystore keystore.jks -storepass password -keypass password
```
> 在提示的信息中，主要是FIRST NAME,LAST NAME的配置，这里填写申请证书的域名地址，比如 _www.jiaozhu.me_ , 或者 _*.jiaozhu.me_ 以上命令中。server为私钥别名(-alias)，生成的keystore.jks文件默认放在命令行当前路径下。

- 生成证书请求文件(CSR) 

```
keytool -certreq -alias server -sigalg SHA1withRSA -file certreq.csr -keystore keystore.jks -keypass password -storepass password
```
> 备份密钥库文件keystore.jks，并稍后提交证书请求文件certreq.csr，等待证书签发。密钥库文件keystore.jks丢失将导致证书不可用

- 获取服务器证书中级CA证书，并导入

```
keytool -import -alias intermediate1 -keystore keystore.jks -trustcacerts -storepass password -file intermediate1.cer 
keytool -import -alias intermediate2 -keystore keystore.jks -trustcacerts -storepass password -file intermediate2.cer
```

- 导入服务器证书

```
keytool -import -alias server -keystore keystore.jks -trustcacerts -storepass password -file server.cer
```
> 导入服务器证书时，服务器证书的别名必须和私钥别名一致。请留意导入中级CA证书和导入服务器证书时的提示信息，如果您在导入服务器证书时使用的别名与私钥别名不一致，将提示“认证已添加至keystore中”而不是应有的“认证回复已安装在keystore中”。

- 查看keystore文件内容

```
keytool -list -keystore keystore.jks -storepass password
```
- 配置WebLogic Server

### 针对Tomcat

- 生成密钥库文件

```
keytool -genkey -alias server -keyalg RSA -keysize 2048 -keystore keystore.jks -storepass password -keypass password
```
> 以上命令中，server为私钥别名(-alias)，生成的keystore.jks文件默认放在命令行当前路径下。

- 生成证书请求文件(CSR)

```
keytool -certreq -alias server -sigalg SHA1withRSA -file certreq.csr -keystore keystore.jks -keypass password -storepass password
```
> 备份密钥库文件keystore.jks，并稍后提交证书请求文件certreq.csr，等待证书签发。密钥库文件keystore.jks丢失将导致证书不可用。

- 获取服务器证书中级CA证书，并导入

```
keytool -import -alias intermediate1 -keystore keystore.jks -trustcacerts -storepass password -file intermediate1.cer 
keytool -import -alias intermediate2 -keystore keystore.jks -trustcacerts -storepass password -file intermediate2.cer
```

- 导入服务器证书

```
keytool -import -alias server -keystore keystore.jks -trustcacerts -storepass password -file server.cer
```
> 导入服务器证书时，服务器证书的别名必须和私钥别名一致。请留意导入中级CA证书和导入服务器证书时的提示信息，如果您在导入服务器证书时使用的别名与私钥别名不一致，将提示“认证已添加至keystore中”而不是应有的“认证回复已安装在keystore中”。

- 安装服务器证书

```
<Connector port="443" protocol="org.apache.coyote.http11.Http11Protocol" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               keystoreFile="conf\keystore.jks" keystorePass="password"
               clientAuth="false" sslProtocol="TLS" />
```

```
<Connector port="443" protocol="org.apache.coyote.http11.Http11Protocol" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               keystoreFile="conf\keystore.jks" keystorePass="password"
               truststoreFile="conf\keystore.jks" truststorePass="password"
               clientAuth="true" sslProtocol="TLS" />
```











