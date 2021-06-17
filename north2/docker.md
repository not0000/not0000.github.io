# docker臨時筆記 fossology sonarqube


## fossology 原始碼使用哪些授權掃描工具
https://hub.docker.com/r/fossology/fossology/


docker pull fossology/fossology
docker run -p 8081:80 fossology/fossology

The server must be ready at http://localhost:8081/repo/. The login credentials are:
user: fossy
pass: fossy

### 上傳檔案
上方選單 的 Upload
* From File | From URL
上傳單一壓縮檔，例如 iso, tar, rpm, jar, zip, bz2, msi, cab 之類的格式
* From Version Control System
從版本控制系統直接取回原始碼 (推薦使用)
支援git 或 svn，需要產生token來連線，要給的是git的位址


### 等候中
上方選單的 Jobs 選 My Recently Jobs
Status狀態可能有
* Started 執行中
* Failed 失敗
* Completed 完成

### 如果有完成該怎麼看?
可以從 Browse 看有哪些程式，點名字一層一層進去看
上方表格也有一個 (tree view or flat)的字，選flat就不會是一層一層，而是全部展開依照名稱排序
建議先選想看的主要資料夾再展開避免太亂不好找


## sonarqube 原始碼白箱檢測工具

https://hub.docker.com/_/sonarqube
docker pull sonarqube


打開sonarqube hub
https://hub.docker.com/_/sonarqube

找到 TAG 標籤進去
https://hub.docker.com/_/sonarqube?tab=tags&page=1&ordering=last_updated

找有community字眼的，複製起來
例如
docker pull sonarqube:lts-community


下載時，找一下要用哪個port
點一下 剛剛 tag 中 lts-community  的字
https://hub.docker.com/layers/sonarqube/library/sonarqube/lts-community/images/sha256-f6748e7367be7709a2abc9926d9fe4868c96cca53183ed868ba94a560ad24d40?context=explore

看到有 EXPOSE 9000，代表docker繫結port的時候右邊的port要輸入9000

範例
docker run  -p 9001:9000 sonarqube

9001是 自己的port，9000是docker裡面的port

所以如果在自己的電腦，可以在瀏覽器localhost:9001打開docker的sonarqube裡面9000的站台


run成功但沒有開起來 要找問題
https://ithelp.ithome.com.tw/articles/10215842
https://myoceane.fr/index.php/sonarcube-docker/


給azure url 
https://not1911.visualstudio.com/
rt6d36jrga7sahouu3a54wi5l5cfmn3ikvbirfzgru5bkclkmb4a



https://community.sonarsource.com/t/error-when-trying-to-run-sonarscanner-for-my-netcore-project/36365/2

default is 
SonarScanner.MSBuild.exe begin /k:"UUEICore_UUEICore" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="rt6d36jrga7sahouu3a54wi5l5cfmn3ikvbirfzgru5bkclkmb4a"

if error 

The token you provided doesn't have sufficient rights to check license.
Pre-processing failed. Exit code: 1

change to 

/d:sonar.login="admin" /d:sonar.password="你的密碼"

cd C:\Users\not\Desktop\sonar-scanner-msbuild-5.2.1.31210-net46

SonarScanner.MSBuild.exe begin /k:"UUEICore_UUEICore" /d:sonar.login="admin" /d:sonar.password="1qaz@WSX#EDC"

cd C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\

MSBuild.exe C:\UUEICore\UUEICore.sln /t:Rebuild

cd C:\Users\not\Desktop\sonar-scanner-msbuild-5.2.1.31210-net46
SonarScanner.MSBuild.exe end /d:sonar.login="admin" /d:sonar.password="1qaz@WSX#EDC"

MSBuild Path
https://docs.microsoft.com/zh-tw/visualstudio/msbuild/whats-new-msbuild-16-0?view=vs-2019


在環境變數 編輯 path
https://helpdeskgeek.com/windows-10/add-windows-path-environment-variable/
https://www.java.com/zh-TW/download/help/path_zh-tw.html

把SonarScanner.MSBuild.exe 和 MSBuild.exe 的位置都加進去
C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\
C:\sonar-scanner-msbuild-5.2.1.31210-net46
然後到專案資料夾執行以下指令


SonarScanner.MSBuild.exe begin /k:"UUEICore_UUEICore" /d:sonar.login="admin" /d:sonar.password="1qaz@WSX#EDC"
MSBuild.exe /t:Rebuild
SonarScanner.MSBuild.exe end /d:sonar.login="admin" /d:sonar.password="1qaz@WSX#EDC"

就可以回去sonarqube看結果了


## minecraft
拉最新版的下來
```
docker pull itzg/minecraft-server
```

生成一個minecraft，並且從本機的地圖檔複製進去給docker用
```
docker run -d -it -p 25565:25565 -v C:\MinecraftFile\Minecraft\saves\World1:/worlds:ro -e WORLD=/worlds -e EULA=TRUE itzg/minecraft-server
```


-v 來源資料夾:/worlds:ro -e WORLD=目的資料夾


-e MEMORY=2G 


## docker pull會被拉去哪裡?

%AppData%\..\Local\Docker\wsl

## 從docker外copy檔案進去 或 從docker內copy到外面
先用 docker ps -a 找到目標的 ContainerID
再用搬進docker內，前面的是來源資料夾，後面的是目的資料夾
docker cp -a C:\Folder ce1de29bac40:Folder/

```
docker cp -a C:\Folder ce1de29bac40:Folder/
```

## docker vmmem佔用太多記憶體的解決方法 
用 wsl -l -v 列出有哪些wsl在運作

用 wsl -t {insert distro} 終止正在使用的wsl。
或者 wsl --shutdown 一次全刪

開一個 %UserProfile%\.wslconfig的純文字檔案，裡面寫以下內容限制記憶體
[wsl2]
memory=2GB

```
[wsl2]
kernel=<path>              # An absolute Windows path to a custom Linux kernel.
memory=<size>              # How much memory to assign to the WSL2 VM.
processors=<number>        # How many processors to assign to the WSL2 VM.
swap=<size>                # How much swap space to add to the WSL2 VM. 0 for no swap file.
swapFile=<path>            # An absolute Windows path to the swap vhd.
localhostForwarding=<bool> # Boolean specifying if ports bound to wildcard or localhost in the WSL2 VM should be connectable from the host via localhost:port (default true).

# <path> entries must be absolute Windows paths with escaped backslashes, for example C:\\Users\\Ben\\kernel
# <size> entries must be size followed by unit, for example 8GB or 512MB
```

https://blog.simonpeterdebbarma.com/2020-04-memory-and-wsl/



researt docker
https://ithelp.ithome.com.tw/articles/10186431

```
用 docker ps -a 看所有的docker ID

# 啟動 docker container
$ docker start [Container ID]

# 停止 docker container
$ docker stop [Container ID]

# 重新啟動 docker container
$ docker restart [Container ID]

# ex:
# 產生一個 container 在背景執行
$ docker run -i -t -d ubuntu bash

$ docker ps
$ docker stop [Container ID]

$ docker ps
$ docker start [Container ID]

$ docker ps
$ docker restart [Container ID]

```