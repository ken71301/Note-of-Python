# 於pycharm開啟pylint / isort / black的流程

## Pylint
### linter，修改code style
https://plugins.jetbrains.com/plugin/11084-pylint

裝好plugin後，在terminal安裝pylint

```
pip3 install pylint
```

打開pycharm的 prefrence > other setting > Pylint 
修改 "Path to Pylint executable" 對應到pylint安裝的位置.
如果不知道裝在哪，可以輸入以下指令:
```
which pylint
```
例圖
![](https://i.imgur.com/jZDbN5i.png)

## black
### formatter 修改排版

```
pip3 install 'black[d]'
```
https://plugins.jetbrains.com/plugin/14321-blackconnect

裝好pluin後，打開pycharm的

prefrence > Tools > BlackConnect 

打勾 "Start local blackd instance when plugin loads"

按一下 Detect 按鈕 ，plugin會找black放在哪裡

在 Trigger Settings 打勾 "Trigger on code reformat" 讓pycharm的code format功能使用black

現在就可以用Black修改Format了， 打開工具列的Code -> Reformat Code

如果想在每次修改檔案時都自動format，可以在 Trigger Settings 內打勾 "Trigger when saving changed files"

例圖
![](https://i.imgur.com/vSquZtj.png)


## isort
### 更改import module的排版

```
pip3 install isort
```

安裝File Watcher Plugin:
https://plugins.jetbrains.com/plugin/7177-file-watchers

Pycharm 點開 Preferences -> Tools -> File Watchers ，點左上角的 + 新增 watcher

在對應欄位輸入下列值
Name -> isort
File Type -> Python
Scope -> Project Files
Program -> \$PyInterpreterDirectory$/python
Arguments -> -m isort \$FilePath$ --profile black
Output paths to refresh -> \$FilePath$
Working directory -> \$ProjectFileDir$

Advanced Options 區塊
Uncheck "Auto-save edited files to trigger the watcher"
Uncheck "Trigger the watcher on external changes"

例圖
![](https://i.imgur.com/ivNRfBw.png)


## 用法
照這樣設定後，pylint會出現在pycharm下方，點開後可以自行決定要掃哪裡
![](https://i.imgur.com/MzAmQk1.png)

也可以在pycharm的inspection中決定要不要開pylint real-time scan，個人建議關閉或不要開通知
![](https://i.imgur.com/vHJdWyU.png)

isort部分則只要對單一檔案進行修改並存檔，就會自動幫你在import部分拉成三個block
python 自帶lib -> 第三方lib -> 本地lib

black部分，打開工具列的code -> reformat code 即可


整理資料來源如下：
https://stackoverflow.com/questions/38134086/how-to-run-pylint-with-pycharm/46409649#46409649

https://github.com/pycqa/isort/wiki/isort-Plugins

https://black.readthedocs.io/en/stable/integrations/editors.html

