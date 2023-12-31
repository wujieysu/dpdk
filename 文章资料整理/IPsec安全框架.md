# IPsec安全框架

## 1.IPsec 网络安全体系

前言：
IPsec是网络层的安全框架，不同于应用层等框架，IPSec安全服务包括访问控制、数据源认证、无连 接数据完整性、抗重播、数据机密性和有限的通信流量机密性。

### 1.1网络安全体系

网络安全是通过多层次的保护实现的，衍生出来的常用方式如下：

1. 防火墙
2. 信息加密
3. 数字签名
4. PKI技术与认证
5. IPsec安全体系

这里只简单提一下各种方案，这其中每一项都复杂无比，然后重点说一下IPsec安全体系。

信息加密就是使用加密算法，重新组织信息，常用的加密算法分为两类：对称加密和非对称加密。区别是对称加密的加解密秘钥相同，非对称加密的加解密秘钥不同。

对称加密算法比较

| 名称 |      秘钥长度      | 运算速度 | 安全性 | 资源消耗 |
| :--: | :----------------: | :------: | :----: | :------: |
| DES  |        56位        |   较快   |   低   |    中    |
| 3DES |   112位或者168位   |    慢    |   中   |    高    |
| AES  | 128位,192位，256位 |    快    |   高   |    低    |

非对称加密算法比较

| 名称 | 成熟度 | 安全性 | 运算速度 |     资源消耗     |
| :--: | :----: | :----: | :------: | :--------------: |
| RSA  |   高   |   高   |    慢    |        高        |
| DSA  |   高   |   高   |    慢    | 只能用于数字签名 |
| ECC  |   低   |   高   |    快    |        低        |

对称与非对称加密的比较

|    名称    |               秘钥管理               | 安全性 |                           运算速度                           |
| :--------: | :----------------------------------: | :----: | :----------------------------------------------------------: |
|  对称算法  | 比较难,不适合互联网,一般用于内部系统 |   中   | 快好几个数量级(软件加解密速度至少快100倍,每秒可以加解密数M比特数据),适合大数据量的加解密处理 |
|            |                                      |        |                                                              |
| 非对称算法 |             密钥容易管理             |   高   |               慢,适合小数据量加解密或数据签名                |

数字签名是通过私钥加密要发送信息的摘要，来保证信息的完整性和没被篡改。

下面我们重点说一下IPsec安全体系。

### 1.2IPsec安全框架

IPsec既然是一个安全框架，自然就不是只有一个协议，IPsec包含三个部分的协议和算法：

1. 认证头协议（AH协议）
2. 封装安全负载协议（ESP协议）
3. 秘钥管理协议（IKE协议）
4. 各类加密和认证算法

> 认证头协议（AH）：IPsec 体系结构中的一种主要协议，它为IP 数据包提供无连接完整性与数据源认证，并提供保护以避免重播情况。AH 尽可能为IP头和上层协议数据提供足够多的认证。

> IPsec 封装安全负载（ESP）：IPsec 体系结构中的一种主要协议。ESP 加密需要保护的数据并且在IPsec ESP 的数据部分进行数据的完整性校验，以此来保证机密性和完整性。ESP 提供了与AH 相同的安全服务并提供了一种保密性（加密）服务，ESP 与AH 各自提供的认证根本区别在于它们的覆盖范围。

> 密钥管理协议（IKE）：一种混合型协议，由Internet 安全联盟（SA）和密钥管理协议（ISAKMP）这两种密钥交换协议组成。IKE 用于协商AH 和ESP所使用的密码算法，并将算法所需的必备密钥放到恰当位置。

IPsec支持两种模式：传输模式和隧道模式。借助于隧道模式，经常被用于做vpn。

#### 1.2.1传输模式与隧道模式

隧道（tunnel）模式：用户的整个IP数据包被用来计算AH或ESP头，AH或ESP头以及ESP加密的用户数据被封装在一个新的IP数据包中。通常，隧道模式应用在两个安全网关之间的通讯。

传输（transport）模式：只是传输层数据被用来计算AH或ESP头，AH或ESP头以及ESP加密的用户数据被放置在原IP包头后面。通常，传输模式应用在两台主机之间的通讯，或一台主机和一个安全网关之间的通讯。

传输模式与隧道模式的封装结构如下图：

![img](https://images2015.cnblogs.com/blog/614525/201704/614525-20170423183423038-1141211184.png)

#### 1.2.2认证与加密算法

（1）. 认证算法

认证算法的实现主要是通过杂凑函数。杂凑函数是一种能够接受任意长的消息输入，并产生固定长度输出的算法，该输出称为消息摘要。IPsec对等体计算摘要，如果两个摘要是相同的，则表示报文是完整未经篡改的。IPsec使用两种认证算法：

- MD5：MD5通过输入任意长度的消息，产生128bit的消息摘要。
- SHA-1：SHA-1通过输入长度小于2的64次方bit的消息，产生160bit的消息摘要。

MD5算法的计算速度比SHA-1算法快，而SHA-1算法的安全强度比MD5算法高。

（2）. 加密算法

加密算法实现主要通过对称密钥系统，它使用相同的密钥对数据进行加密和解密。目前设备的IPsec实现三种加密算法：

- DES（Data Encryption Standard）：使用56bit的密钥对一个64bit的明文块进行加密。
- 3DES（Triple DES）：使用三个56bit的DES密钥（共168bit密钥）对明文进行加密。
- AES（Advanced Encryption Standard）：使用128bit、192bit或256bit密钥长度的AES算法对明文进行加密。

这三个加密算法的安全性由高到低依次是：AES、3DES、DES，安全性高的加密算法实现机制复杂，运算速度慢。对于普通的安全要求，DES算法就可以满足需要。

#### 1.2.3IKE秘钥交换协议

一种混合型协议，由Internet 安全联盟（SA）和密钥管理协议（ISAKMP）这两种密钥交换协议组成。

IPSec 在两个端点之间提供安全通信，两个端点被称为IPSec ISAKMP 网关。安全联盟（简称为SA）是IPSec 的基础，也是IPSec 的本质。SA 是通信对等体间对某些要素的约定，例如使用哪种协议、协议的操作模式、加密算法（DES、3DES、AES-128、AES-192 和AES-256）、特定流中保护数据的共享密钥以及SA 的生存周期等。

建立安全联盟的方式有两种，一种是手工方式（Manual），一种是IKE 自动协商（ISAKMP）方式。

IKE协商AH 和ESP所使用的密码算法，并将算法所需的必备密钥放到恰当位置。IKE为IPsec提供了自动协商交换密钥、建立SA的服务，能够简化IPsec的使用和管理，大大简化IPsec的配置和维护工作。

SA的建立过程包括两个阶段：

第一阶段主要有这么几个工作要做：

- 参数的配置，如参数配置。包括：认证方法，选择预共享密钥或数字证书认证。
  Diffie-Hellman 组的选择
- 策略协商。包括：加密算法选择DES、3DES、AES-128、AES-192 或AES-256。hash 算法选择MD5 或SHA
- DH 交换。虽然名为“密钥交换”，但事实上在任何时候，两台通信主机之间都不会交换真正的密钥，它们之间交换的只是一些DH 算法生成共享密钥所需要的基本材料信息。DH 交换，可以是公开的，也可以受保护。在彼此交换过密钥生成“材料”后，两端主机可以各自生成出完全一样的共享“主密钥”，保护紧接其后的认证过程。
- 认证 。DH 交换需要得到进一步认证，如果认证不成功，通信将无法继续下去。“主密钥”结合在第一步中确定的协商算法，对通信实体和通信信道进行认证。在这一步中，整个待认证的实体载荷，包括实体类型、端口号和协议，均由前一步生成的“主密钥”提供机密性和完整性保证。

第二阶段主要的工作如下：

第二阶段为快速SA，为数据传输而建立的安全联盟。这一阶段协商建立IPsec SA，为数据交换提供IPSec 服务。第二阶段协商消息受第一阶段SA 保护，任何没有第一阶段SA 保护的消息将被拒收。

- 策略协商，双方交换保护需求：

  - 使用哪种IPSec 协议：AH 或ESP
  - 是否使用hash 算法：MD5、SHA 或NULL
  - 是否要求加密，若是，选择加密算法：DES 或3DES、AES-128、NULL、AES-192 或AES-256

  在上述三方面达成一致后，将建立起两个SA，分别用于入站和出站通信。

- 会话密钥“材料”刷新或交换。在这一步中，将通过DH 交换生成加密IP 数据包的“会话密钥”。

- 将SA 递交给IPSec 驱动程序。在第二阶段协商过程中，如果响应超时，则自动尝试重新进行第二阶段SA 协商。

#### 1.2.4小结

IPsec作为一个安全框架，提供了2种封装方式，传输模式和隧道模式，根据需要选择合适的模式进行保护。SA作为IPsec的基础，可以使用IKE的方式来管理交换秘钥，协商工作参数。对于IPsec在Linux的实现可以进一步了解。







转载自：  作者 [AISEED](https://home.cnblogs.com/u/yhp-smarthome/)   本文链接 https://www.cnblogs.com/yhp-smarthome/p/6753419.html