
If you want English manual, please refers to below link.    

[D-Laravel English manual](#d-laravel-english-manual)


# D-Laravel

D-Laravel採用了docker-compose的微服務架構，

經由超簡易的console及create指令，幫您快速建立出基本的Laravel開發環境，

使用D-Laravel，代表了，我們的Mac系統，不再需要安裝mysql，nginx及php-fpm。

可自訂docker-compose-custom.yml快速創建出自己的開發環境。

### 為什麼用D-Laravel

提供簡易的Bash執行docker-compose指令，快速生成laravel專案環境。

可用最新的PHP在Mac OS上執行。

快速產生self-signed自我簽署的憑證，並完成HTTPS設定。

不需要安裝DnsMasq軟體。

D-laravel停止時，不會佔用80埠，拜docker-compose之賜，極易調整Listen的連接埠。

秒級的啟動速度。

可模擬不同的資料庫環境。

可同時開啟多個不同的測試站台。

簡易的docker-compose v2設定檔。

使用Dockerhub標註offical的image，建立Laravel基本執行環境。

用docker聽起來，好像比較潮。:p
<pre>
./console help (幫助，console參數用法。)
./create test1  (建立test1.dev)
./console down或./console up (啟用及停用container)
./console restart    (./console down再./console up)
`./console alias`  
(打./console指令太長了嗎，上方指令可暫時用c代表./console，所以執行後，輸入:c info、c up或c down..即可執行。)
./console alias    (印出console的別名範本，自行加到.bashrc或.zshrc永久生效)

為了讓Chrome正常顯示，需要把生成的域名存入系統鑰匙圈中，因此會詢問系統密碼(MacOS only)。
./console secure
更新，並加密所有sites資料夾內的域名
</pre>

#### 主要目錄結構
<pre>
etc/   (nginx、php.ini及mysql的相關設定檔)
data/  (mysql的資料檔案，./console up 自動生成)
sites/ (專案的資夾，./create test1時，會建立在這個此目錄下)
create (簡化的bash，用來快速的建立laravel的專案)
console(簡化的bash，用來快速使用各種docker-compose的命令。例如:./console mysql即可進入mysql)
docker-compose.yml (一個softlink，連結到不同的設定檔，例如:./console custom，即何將連結連到docker-compose-custom.yml)
</pre>

#### 一、請先安裝docker-for-mac
<pre>
https://docs.docker.com/docker-for-mac/
</pre>

#### 二、例如用git clone這個repo，並進入laravel資料夾，然後執行：./console pull。
<pre>
git clone https://github.com/DevinY/dlaravel.git
cd dlaravel

下方pull的動作是可以略過，啟動時，會依docker-compose.yml的設定自動下載所需的images。
./console pull
當第一次執行會花較久的時間從dockhub上，下載docker-compose所需要的images.
</pre>

#### 三、執行./create ['project name'] 建立開發專案
<pre>
例如:
./create test1
那麼就會建立出 http://test1.dev 網站(本機測試用的外部無法存取)。
可以建立多個project站台

需有系統權修改/etc/hosts檔，./create的過程中會需要詢問您的系統密碼:
目前只可在Mac OS上使用。
</pre>


#### 四、停止請在目錄下執行./console down
<pre>
注意事項: 這個只是拿來開發測試用的，Mysql的root是沒有密碼的。
你可能需要修改docker-compose.yml啟動時的TZ，目前設為Asia/Taipei
</pre>

#### 五、更新d-laravel bash程式及一些基本設定檔。
<pre>
git pull

在d-laravel的.gitignore已排除了會變動的區域了，例如: docker-compose.yml、docker-compse-custom.yml, sites專案資料夾等。
所以在dlaravel的目錄下，您可以透過git pull取得最新的版本及設定。
</pre>

#### 其他
<pre>
./create [project名稱] 會建立及下載laravel，搞定一切設定，包含資料庫，
但如果是一個已存在的Project，只需有nginx的設定，應該怎麼做呢?

使用--conf 即可修改系統的/etc/hosts檔，加入project的域名，如: project1.dev。
./create --conf [project名稱] 例如:./create --conf project1

可以將已存在的Laravel專案移到sites資料夾內。

或是使用composer create-project指令建立Project.
例如使用手動指令手動建立Project，這裡用lumen示範。
./console exec，可用於執行php contaiener內的命令

./console exec composer create-project --prefer-dist laravel/lumen project1
</pre>
#### 調整設定檔的image切換
PHP: (OFFICIAL REPOSITORY重build符合Laravel環境)
https://hub.docker.com/r/deviny/fpm/tags/
<pre>
 image: deviny/fpm:7.1.4
 image: deviny/fpm:7.0.18
 image: deviny/fpm:5.6.30
</pre>

Nginx: (OFFICIAL REPOSITORY)
https://hub.docker.com/r/library/nginx/

Mysql: (OFFICIAL REPOSITORY)
https://hub.docker.com/_/mysql/

註: 原data資料夾已產生時，變更不同的mysql版本，在docker-compose.yml的設定檔內，
你需可能需調整資料庫夾data的名稱，確保新版的mysql image能正常運作。
例如:我將data設更為data_mysql8

<pre>
db:
  image: mysql:8
  略..
  volumes:
      - ./etc/mysql/my.cnf:/etc/mysql/my.cnf
      - ./data_mysql8:/var/lib/mysql
</pre>

#### 進階
如果您想學習或重build自己php的fpm image版本，例如擴展php的功能，
那麼可到下方連結參考，就可了解dlaravel的fpm image怎麼來的，及如何用docker重build一個自己的phpfpm image。
https://github.com/DevinY/fpm/blob/master/README.md

YouTube上操作如何重build fpm image的過程。

https://www.youtube.com/watch?v=vzCcWpWstP4

Docker指令及DevinY/dlaravel提供的./console的bash指令

|Docker官方指令   |簡易./console Bash指令| 說明|
|---|---|---|
| docker-compose pull  |./console pull   |抓最新的images   |
| docker-compose up -d  |./console up   |啟動container   |
| docker-compose down  |./console down  |停止container   |
| docker-compose ps或docker ps|./console ps  |查看docker-compose的process   |
| docker-compose exec php sudo -u dlaravel bash   |./console  |進入php的container   |
| docker-compose exec php 指令  |./console exec 指令 |執行php container 指令，例如: ./console exec php -v|
| docker-compose exec db mysql   |./console mysql  |執行mysql   |
| docker-compose exec web nginx -s reload   |./console reload  |重載nginx設定   |
| docker-compose logs -f [SERVICE]   |./console logs  |看nginx的log，Ctrl+C停止 |
|   |./create [ProjectName]|建立一個project，並完成所有基本的環境設定   |
|   |例如: ./create test1  |例如: 這樣會建立一個http://test1.dev的網站   |
|   |./console restart  |重啟container   |
|   |./console info  |查看目前可用的sites資料(這裡是查單的查詢sites資料夾)   |
|模式切換:|
|   |./console random  |container啟動時，使用隨機埠|
|   |./console normal  |使用本機port 80及127.0.0.1:3306|
|   |./console custom  |使用自己的docker-compose-custom.yml|
|下方: -f: 指定docker-compose設定檔。 up -d:啟動在背景執行。|
|docker-compose -f docker-compose-normal.yml up -d| |使用port 80及port 3306|
|docker-compose -f docker-compose-random.yml up -d| |指定隨機埠的啟動檔|



# D-Laravel (English manual)


D-Laravel uses docker-composer micro service framework,

it helps you quickly to build basic Laravel development environment by using simple commands: console and create.

Using D-Laravel also means no longer to install mysql, nginx and php-fpm in Mac.

You can deploy the environment by self-defined docker-compose-custom.yml file.


### Why use D-Laravel


It provides simple Bash to execute docker-compose that helps you to create laravel project quickly.

You can execute the latest PHP version in Mac OS.

It helps you to generate self-signed certification and completes HTTPS setting.

No need to install DnsMasq. 

Port 80 won't be used when not start dlaravel.

Initial speed in seconds!!!

It can simulate different DB environment.

It also could open kinds of testing hosts.


Easy docker-compose v2 setting.

Using Dockerhub to remark official image to build basic Laravel environment.


It seems fashion if use docker. :P
<pre>
./console help (help, console parameter)
./create test1  (build test1.dev)
./console down or ./console up (stop or start container)
./console restart    (first ./console down and then ./console up)
`./console alias`  
(If you think ./console command is too log, you could create c alias in terminal which simplifies command to "c info", "c up", or "c down")
./console alias (print console alias samples, it can be added to .bashrc or .zshrc)   

Below's command saves the generated domain name in system keychain to let Chrome working normally, it would ask for system password.(MacOS only)
./console secure test1 
(https://test1.dev domain)
</pre>


### Main Directory structure
<pre>
etc/   (nginx、php.ini and mysql settings)
data/  (mysql data files, auto-generated by ./console up)
sites/ (project files, the project will be set in this folder when executes ./create test1)
create (Simplified bash, to fast build laravel project) 
console (Simplified bash file, to use kinds of docker-compose commands. ex. login mysql by "./console mysql")
docker-compose.yml (A soft link to connect different settings. ex. linking to docker-compose-custom.yml by "./console custom") 
</pre>


#### 1. Please install docker-for-mac first
<pre>
https://docs.docker.com/docker-for-mac/
</pre>


#### 2. git clone D-Laravel repo and execute "./console pull" in the folder
<pre>
git clone https://github.com/DevinY/dlaravel.git
cd dlaravel


You can ignore below's pull command. It will auto download the images according to docker-compose.yml when initials.
./console pull
The first time to download needed images from dockhub will take longer time. Be patient. :P
</pre>


#### 3. execute "./create ['project name']" to biuild new project
<pre>
ex.
./create test1
It will build http://test1.dev website (can't be accessed from outside)
So you can use this command to build many different projects.

You need to provide privilege to modify /etc/hosts file, so D-Laravel will ask for your systme password during executing "./create project". (Mac OS using only)
</pre>


#### 4. executing "./console down" in D-Laravel foldee
<pre>
Attention:
D-Laravel is only used for development, Mysql root password is empty.
You may need to revise TZ in docker-compose.yml, default is Asia/Taipei.
</pre>


#### 5. Updating D-Laravel bash program and some settings
<pre>
git pull

.gitignore in D-Laravel already excludes dynamic files, ex. docker-comnpose.yml, docker-compose-custom.yml, sites folder etc.
You can git pull latest version and setting in D-Laravel folder.
</pre>


#### Others
<pre>
./create [project name] will create and build laravel, it would set all configures automatically including DB.
But how do we do if project already exists and only needs nginx setting?

Using "--conf" to update /etc/hosts file, for example: "127.0.0.1 project1.dev". 
./create --conf [project name] (./create --conf project1)


You can move exist Laravel project to D-Laravel site folder.

Or manually executes "composer create-project" (not default version etc.) 

Ex. Create project manually by using lumen
./console exec  //It can execute php command in container.

./console exec composer create-project --prefer-dist laravel/lumen project1
</pre>



#### Adjust images in setting
PHP: [Official repository rebuilds php to fit Laravel environment](https://hub.docker.com/r/deviny/fpm/tags/)
<pre>
 image: deviny/fpm:7.1.4
 image: deviny/fpm:7.0.18
 image: deviny/fpm:5.6.30
</pre>

Nginx: [OFFICIAL REPOSITORY](https://hub.docker.com/r/library/nginx/)

Mysql: [OFFICIAL REPOSITORY](https://hub.docker.com/_/mysql/)

Remark: 
When you need to revise different mysql version and that D-Laravel/data already exist, you may need to reset DB name in D-Laravel.data to ensure mysql image works normally.  

Ex. Rename data to data_mysql8

<pre>
db:
  image: mysql:8
  ...
  volumes:
      - ./etc/mysql/my.cnf:/etc/mysql/my.cnf
      - ./data_mysql8:/var/lib/mysql
</pre>


#### Advandace

If you wish to learn or rebuild your own php fpm image version, ex. php extension,
please refers to below's link, it will help you to understand how D-Laravel fpm image created and how to use docker to rebuild your own php-fpm image.  
[Click me](https://github.com/DevinY/fpm/blob/master/README.md)




### Docker commands and Devin/dlaravel "./console" bash command comparison table

|Docker official commands|simplified ./console Bash commands| Describtion|
|---|---|---|
| docker-compose pull  |./console pull   |pull latest images   |
| docker-compose up -d  |./console up   |start container   |
| docker-compose down  |./console down  |stop container   |
| docker-compose ps or docker ps|./console ps  |show docker-compose process   |
| docker-compose exec php sudo -u dlaravel bash   |./console  |access php container   |
| docker-compose exec php command  |./console exec command |execute php container command，ex. ./console exec php -v|
| docker-compose exec db mysql   |./console mysql  |exectue mysql   |
| docker-compose exec web nginx -s reload   |./console reload  |reload nginx setting   |
| docker-compose logs -f [SERVICE]   |./console logs  |show nginx log，stop it with Ctrl+C |
|   |./create [ProjectName]|create a project with default settings   |
|   |Ex. ./create test1  |Ex. create a http://test1.dev website   |
|   |./console restart  |restart container   |
|   |./console info  |show url info   |
|Switch model:|
|   |./console random  |using random port when initiate container|
|   |./console normal  |using local port 80及127.0.0.1:3306|
|   |./console custom  |using self-defined docker-compose-custom.yml|
|command below: -f: specific docker-compose.yml path。 up -d:start container in background|
|docker-compose -f docker-compose-normal.yml up -d| |using port 80 and port 3306|
|docker-compose -f docker-compose-random.yml up -d| |specific yml file with random port|


