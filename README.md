# 反警方取证的 Win11 安全加固策略
 
**实现目标**：

- 锁屏密码未输入前，电脑将完全加密，无法被破解取证。


**配置包含**：

 - 硬盘加密 + 内存加固 + 启动过程加固 + 美区微软账号 + 其他安全设置


**完成效果**：
![此锁屏界面，直接卡死100%的取证机！！](images/Bitlocker_Preboot_Pin_UI.png)


.

.


## 步骤：（总计十步）

.

.

---

### 【第1步】开启 内存加固

---



路径：**Windows安全中心 -> 设备安全性 -> 内核隔离**

- **内存完整性** = 开启  
- **内核模式硬件强制堆栈保护** = 开启  
  （作用：避免被DMA攻击）

⚠️ *虚拟机内的Win11，无法启用上述两选项。所以宿主机一定要启用上述选项。*

.

---

### 【第2步】设置 启动过程加固（编辑组策略）

---

路径：**本地计算机策略 -> 计算机配置 -> 管理模板 -> Windows组件 -> Bitlocker驱动器加密**

- **此计算机锁定时禁止新的DMA设备** = 启用  
  （作用：避免被DMA攻击）

路径：**本地计算机策略 -> 计算机配置 -> 管理模板 -> Windows组件 -> Bitlocker驱动器加密 -> 操作系统驱动器**

- **允许对完整性验证使用安全引导** = 启用  
- **启动时需要附加身份验证** = 启用  
  （作用：启动时，必须先输入Preboot Pin）

路径：**本地计算机策略 -> 计算机配置 -> Windows设置 -> 安全设置 -> 本地策略 -> 安全选项**

- **账户：管理员账户状态** = 关闭  
- **账户：来宾账户状态** = 关闭  
- **交互式登录：计算机帐户锁定阀值** = 4次无效登录  
  （作用：Preboot Pin，输入4次错误后，强制要求输入48位Bitlocker密钥）

路径：**本地计算机策略 -> 计算机配置 -> Windows设置 -> 安全设置 -> 账户策略 -> 账户锁定策略**

- **重置账户锁定计数器** = 99999分钟后  
- **账户锁定时间** = 99999分钟后  
- **账户锁定阈值** = 15次  
  （作用：输入15次错误后，机器被锁定70天，然后才能继续输入解锁密码）

⚠️ 注意：未来，如果支持更多锁定天数（或支持输错10次就直接销毁），则要更新此配置，以便加强安全性。


.


---

### 【第3步】开启Bitlocker全盘加密 + 设置 冷启动密码（设置 Bitlocker PreBoot Pin）

---


路径：**控制面板 -> 系统和安全 -> BitLocker 驱动器加密**

- **开启**：系统盘bitlocker（建议：Bitlocker密钥存储在《美区》icloud，并且icloud开启《高级数据保护》）  
- **设置**：Bitlocker PreBoot Pin  
  （作用：开启Bitlocker，设置Preboot Pin）

⚠️⚠️ 注意：  

1. Bitlocker密钥，一定，一定，一定，不要上传至微软账号的云空间！！！  
2. 请复查微软网站，看密钥有没有被上传，并手动关闭win11的bitlocker密钥备份功能！！！  
   （作用：防止通过微软账号找回Bitlocker密钥）

⚠️⚠️⚠️ 必须启用 **Bitlocker PreBoot Pin**，这是保证冷启动安全的核心！


.


---

### 【第4步】开启内存加密

---



目前Win11没有提供此功能（未来提供后可开启）。

PS：可能需要等待支持“存算一体”的内存硬件。


.


---

### 【第5步】设置 快速锁屏 快速关机

---

- 避免数据在内存中驻留
  - **关闭**：睡眠模式  
  - **开启**：休眠模式  


- 设置：快速进入锁屏状态或关机状态
  - **关闭**：人脸解锁和指纹解锁 (注意，仅指锁屏界面的解锁)  
  - **设置**：屏幕锁定时间 = 3分钟  
  - **设置**：一键锁屏  
  - **设置**：一键关机  

- 设置：手机离开电脑后，自动锁屏
  - **开启**：动态锁（需要蓝牙配对手机）  



.


---

### 【第6步】微软账号 搬离中国区

---


- **迁移**：微软账号，转移到 “美区” （或中国区以外）  
  （作用：将OneDrive云端存储空间迁移到“美国”）

⚠️⚠️ 注意：

1. 微软账号密码建议只保存在iPhone或MacBook中，不推荐使用国产手机。  
2. Bitlocker密钥不要同步到微软云空间（如果已上传，务必删除）


.


---

### 【第7步】加密 - 备份盘/云数据

---



- **如果 备份到本地**：本地备份盘 必须加密
  - 数据只能备份到，使用了Bitlocker加密硬盘（或虚拟磁盘）  

- **如果 备份到云端**：云端备份盘 上传前数据必须多层加密  
  1. 使用Cryptomator创建“加密的虚拟磁盘”，再将虚拟磁盘同步到云端。  
  2. 使用Syncthing（SyncTrayzor）的“加密同步” 功能，将本地数据（可以是Cryptomator的加密虚拟磁盘），进行二次加密上传到云端。

⚠️⚠️ 注意：Cryptomator的虚拟磁盘，原生支持按 “文件级的加密“ ，非常适合数据的碎片化更新到云端

.


---

### 【第8步】清理有可能泄密的“所有数据空间”

---


- 密码：建议仅使用Bitwarden（或苹果设备）保存密码。
  - 删除：本机内，Chrome和Edge内密码保存的各个网站的密码 ‼️‼️ （一定要删除，并清理回收站）
  - 删除：云端上，谷歌账号、微软账号保存的各个网站的密码 ‼️‼️（一定要删除，并清理回收站）

- 删除：本机的苹果软件，避免Win被攻破时连带苹果账号被攻破。  

- 删除：OneDrive和OneNote中的敏感数据。  

- 删除：旧邮箱、网盘、NAS中相关敏感数据。  （Nas中的数据要覆盖删除！！！！）


.


---

### 【第9步】软件安全

---



- **杀毒软件**：建议Avira小红伞（注意，不推荐卡巴斯基，其为俄罗斯软件）。 

- **清理软件**：CCleaner  

- **卸载软件**：Uninstall Tool、Revo Uninstaller Pro  

⚠️⚠️ 注意：不要安装国产安全类软件，或外国软件的中国区特供版本！


.


---

### 【第10步】远程控制软件

---

- 建议使用开源远程控制软件（如RustDesk）。  

- 设置：Rustdesk断开连接后，自动锁屏。  
 
 
.

.

.



---

## ⚠️⚠️⚠️ 最后，给几点忠告：

---




### 首先，Win、Linux、Mac，在安全性和易用性上哪个更好？


- 结论必然是，最新款的MacBook（仅指苹果M系列处理器）。
	-  Linux对普通用户易用性太差，Windows在安全方面的易用性，也非常令人堪忧，上述操作太繁琐。
	-  但如果是MacBook，你压根不用上述这些繁琐操作，就直接一步到位，默认配置就能拿到上面配置后才能达到的效果。

---

### 反取证的最佳电脑配置方案是什么？


- 如果要防止警方暴力非法取证，目前的最优方案，就是加钱‼️买最新款MacBook‼️ （并保持满足以下特性）
  - 使用最新的M系列处理器
  - OS版本更新到最新
  - 保证SIP设置和文件保险箱为默认配置
  - 设置独立锁屏密码
  - 美区苹果账号（美区iCloud）
- 记住，最新款的苹果设备（MacBook和iPhone），永远是反各国警方取证的利器，没有之一。

---

### 如果要使用Win的软件怎么办？

- 如果需要使用Win，可以在MacOS中使用Vmware Fusion虚拟机（官方正版已永久免费），并在虚拟机内的Win11上启用BitLocker加密和BitLocker PreBoot Pin。 
- 如果在Mac中使用PD虚拟机运行Win11，请严格限则PD虚拟可以访问的Mac磁盘权限（严禁无缝访问MAC磁盘下所有的文件夹），如不能做到此限制，则不推荐PD虚拟机。
- 注意，如果要在Mac里面开虚拟机（分配16GB以上内存给Win），建议MacBook选配内存最好64GB以上。但最新款的MacBook价格会超过30000人民币。如果遇到意外事件，3万元都无法解决问题，那这笔钱绝对不能省。

---

### 如果想玩Steam游戏怎么办？

- 对于非要玩Win游戏，又需要安全性做足的人来说，建议将游戏电脑和私人电脑分开。最好通过局域网用Parsec远程从游戏PC投屏到本地Mac。

---

### 如果想使用Android怎么办？

- **强烈不推荐使用！！！**  
- 如果非要用，呐安卓手机中，安全性最高的，必然是最新款的谷歌Pixel（但仍旧不推荐）。其安全性仍旧比Windows还差。建议安卓手机仅仅用来玩游戏。

.

.


---

### 最后的最后，要特别说明：

- ‼️**重要1**： 冷启动的安全性是最高的。
	- 也就是说，对于反取证来说，无论是（设置了BitLocker PreBoot Pin后的）Win11，还是（默认配置的）MacOS，在这些电脑重启后的首次启动时，其安全等级都是相同的！！！即，在用户未输入解锁Pin前，任何数据都是未解密状态。所以，遇到紧急情况，**电脑立刻断电！！！！！！！**是对抗“非法取证”的最安全选项。

- ‼️**重要2**：锁屏密码要独立于任何密码，
	- 至少12位以上，并尽量包含特殊符号（如`*!`等符号）。以防止被暴力破解

- ‼️**重要3**：关闭生物特征识别解锁。
	-  锁屏阶段的解锁，⚠️⚠️⚠️ 一定 一定 一定不要使用指纹（或摄像头方案如FaceID）等生物信息，一定只能记在脑子里。

- ‼️**重要4**：MacBook不要修改默认安全配置。即：
	- 不要修改“SIP”的任意设置 和 不要关闭文件保险箱（这两项功能，用于全盘加密）
	- 更不要修改“允许配件连接”的设置（用于防范DMA攻击）


.

.


---

### 特别提示：  

- 为什么强烈不推荐的Win10 ？
	- Win10已经停止安全性更新
  - 防DMA攻击能力弱于Win11
	- 不过，在冷启动（PreBoot Pin）的反取证效果上，Win10与Win11相同

- 为什么不推荐 《虚拟机 + VeraCrypt》的方案，而推荐macbook作为反取证的首选方案？
  - 首选，VeraCrypt硬盘加密，其对性能有损耗，不如Mac的硬件加密方案（其是性能无损的）。
  - 其次，虚拟机内运行操作系统，也对性能优损耗，无法释放100%性能。加上VeraCrypt就是双重性能损耗。远远不如Macbook。
  - 再次，如果虚拟机内外，安装的都是Linux，则更过于复杂，小白用户非常不友好，不如Macbook
  - 再次，Linux并非原生支持加密，必须配合各种第三方软件（虚拟机和Veracrypt）才能实现反取证。这比Win11配置反取证还复杂，而且对性能损耗过大。
  - 最后，Macbook可以与iPhone，进行端对端加密的传输数据，无需像Win11或Linux那样，需要第三方软件才能数据传输（太不省心了，而且增加数据泄漏风险，总之能花钱解决就别自己搞）
  - 总之，有钱直接上最新型号Macbook，省心+0配置，一次性解决所有反取证问题。
