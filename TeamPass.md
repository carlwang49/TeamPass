# TeamPass

## 連結

* GitHub : [TeamPass GitHub](https://github.com/nilsteampassnet/TeamPass)
* Website : [TeamPass Website](https://teampass.net/)
* Official Document : [TeamPass Document](https://teampass.readthedocs.io/en/latest/)

## 下載與安裝

TeamPass運行在LAMP架構上，需要安裝Apache、MariaDB（或 mySQL）、PHP以及相關PHP套件。

User data ： 

```bash
#!/bin/bash
apt update && upgrade -y
# apache下載
apt install apache2 -y
systemctl enable apache2
# MariaDB下載
apt install mariadb-server -y
# 下載PHP以及相關套件
apt install php libapache2-mod-php php-mysql -y
apt install php-{curl,mbstring,bcmath,common,gd,xml,fpm} -y
# 資料庫設定
sudo mysql -e "SET PASSWORD FOR root@localhost = PASSWORD('PASSWORD');FLUSH PRIVILEGES;" 
printf "PASSWORD\n n\n n\n n\n y\n y\n y\n" | sudo mysql_secure_installation
mysql -u root -pPASSWORD -e "create database teampass character set utf8 collate utf8_bin;"
mysql -u root -pPASSWORD -e "grant all privileges on teampass.* to teampass_admin@localhost identified by 'PASSWORD';"
# 下載TeamPass
git clone https://github.com/nilsteampassnet/TeamPass.git
rm -rf /var/www/html/index.html
mv TeamPass/* /var/www/html
chown -R www-data.www-data /var/www/html/
# 修改php.ini文件
sed -i '380c max_execution_time = 60' /etc/php/7.2/apache2/php.ini
systemctl restart apache2 
```

> 可以將PASSWORD改成自己的密碼

此User data運行在 Ubuntu 18.04 上，並開啟 `80` port。

成功運行後，輸入瀏覽自己的IP即可看到安裝畫面，點按**NEXT**

![](./images/install/welcome.png)

點按**LAUNCH**，系統會確定是否各項都安裝使否完成並設定正確，完畢後點按**NEXT**

![](./images/install/server_checks.png)

> 若遇到只有Execution time limit沒有綠勾勾的情況，請不用理會，繼續前往下一步

若您按照上面提供的User data安裝，請輸入以下資訊：
* **Host** : localhost
* **DataBase name** : teampass
* **Login** : teampass_admin
* **Password** : PASSWORD
* **Port** : 3306
  
點按**LAUNCH**，確認出現**Connection is successful**後，即可點按**NEXT**。

![](./images/install/database_connection.png)


此處設定初始admin帳號的密碼即可，點按**LAUNCH**，確認出現**Information stored**後，即可點按**NEXT**。

![](./images/install/preparation.png)

點按**LAUNCH**，並稍等約10秒建立資料庫，完畢後點按**NEXT**

![](./images/install/tables_creation.png)

點按**LAUNCH**，確認三個項目都出現綠勾勾，即可點按**NEXT**

![](./images/install/finalization.png)


最後，點按**Move to home page**前往登入頁面

![](./images/install/Resume.png)

登入Administrator帳號：

* Account : admin
* Password : PASSWORD

![](./images/install/login.png)

> 密碼為前面安裝步驟所設定的密碼

## 初始設定

成功登入後，有一些建議的設定如下：

* 關閉Maintenance mode來允許一般使用者登入
  
![](images/setting/maintenance.png)

* 新建的檔案夾權擁有上一層檔案夾之權限

![](images/setting/sub_folder.png)


* 啟用Knowledge base功能，可用來寫公告或使用規範

![](images/setting/knowledge_base.png)

* 允許重複名稱

![](images/setting/duplicate.png)

* 對於沒有權限更改密碼的使用者，可用Password suggestion來建議管理者新增或更改密碼

![](images/setting/suggestion.png)

* 若要使用API功能，請開啟此項設定。開啟後每個使用者將可從個人資料欄取得API Key

![](images/setting/API.png)

若是第一次使用TeamPass，建議先依以下順序創建 Folder → Role → User

## 創建 Folder

每組密碼（Item）都會統一管理在Folder裡，Folder按照階層排列。

點選檔案夾與加號圖示創建Folder

![](./images/folder.png)

![](./images/create_folder.png)

* **Folder label** : 該檔案夾的名稱
* **Parent Folder** : 上層資料夾，選`root`代表此檔案夾為第一層
* **Required Password Strength** : 該檔案夾內儲存的密碼強度
* **Associated access for Roles** : 預設給所有Role的權限
* **Renewal period** : 多久要換一次Item裡的密碼，`0`代表永不

## 創建 Role

在TeamPass中，有類似群組Group的概念，稱作Role。 可以將User放到特定的Role當中，統一管理每個Role對各檔案夾的存取權限。

> 一個User可加入到多個Role

點選選帽子與加號圖案

![](./images/role.png)

![](./images/create_role.png)

* **Required Password Strength** : 在該Role裡的User Account密碼強度。每個User自行更換密碼時，需要符合所屬Role的密碼強度。

可點選各方匡來設定權限

![](./images/role_2.png)

* **綠色**：Read、Write、Delete
* **黃色**：Read Only
* **紅色**：無法存取
* **藍色**：可存取，但有特定權限無法使用

## 創建 User

### User類型

TeamPass主要有四種User類型。

#### Administrator

Administrator可修改TeamPass上所有設定，並且管理所有User、Role以及Folder。 

> Administrator帳號無法存取Item。

#### Manager

Manager可以管理所屬Role（Group）當中的User、Folder以及Item。

#### Human Resource

Human Resources除了擁有跟Manager一樣的權限，還可管理所有TeamPass上的使用者。

> Human Resources 無法管理Administrator帳號。

#### User

若無指定，則為一般使用者。

### 建立 User

點選使用者以及加號圖案來新增使用者

![](./images/user.png)

![](./images/create_user.png)

* **Login** : 該User登入時的帳號
* **Email** : 後續功能使用，MFA、忘記密碼等等
* **User is administrated by** : 該User的管理人
* **Roles** : 該User所屬的群組
* **Allowed/Denied Folders** : 指定特定Folder的權限
* **Options** : 可指定該User為哪種類型，一般使用者則無需選擇
* **Personal folder** : 勾選來允許建立User的個人資料夾

> 若要建立User的個人資料夾，需要到登入Administrator帳號，點選 Setting → Task → Create personal folders for all users without one。

User創建完畢後記得再點選一次鑰匙圖案來設定密碼，左側有幾個小圖案，點選來編輯使用者、更改密碼、查看log、發送帳號資訊以及查看使用者各項存取權限。若要改變該User的類型，可以透過點選開關右側選項來設定。 

![](./images/modify_user.png)

> 若要刪除使用者，需要先點選編輯，鎖定該使用者之後才能刪除。

## 創建 Item

Item為TeamPass存放密碼以及相關資訊的單位。使用User登入後，即可創建Item。

![](./images/Item.png)

點選Menu圖示展開更多功能。若發現有些功能沒有顯示在展開的欄位裡，記得到設定開啟各項功能。

![](./images/item_menu.png)

若誤刪了Item或Folder，可以到admin帳號中，點選 Setting → Deletions，勾選欲回復的Item或Folder，並點選 Restore.

## 其他

### Custom Fields

除了存放帳號密碼外，可以使用Custom Fields功能來自訂可輸入的欄位。例如，信用卡資訊。 若要使用，需要先啟用Custom Fields。點選**Add Category**即可新增新項目，並新增需要填的欄位與套用的Folder。最後，記得要套用Custom Fields到需要的檔案夾。

![](./images/custom_fields.png)

### One time view link

TeamPass提供Item的一次性URL連結，任何人只要擁有該連結即可取得密碼及相關資訊。  
若要使用此功能需要先到設定的Option欄位開啟此功能。

![](./images/onetimelink.png)

開啟設定後，每個Item的選單中將會出現One time view link的選項。

![](./images/onetimelink2.png)

### Task

設定裡面有一欄位Task，可以讓管理者點選來執行各類操作。創建Personal Folder或是更新Salt Key也需要在Task中點選。

![](images/task.png)


### 更改IP

若Server的IP有變更，記得到設定頁面去更改成正在使用的IP。

![](./images/changeIP.png)


