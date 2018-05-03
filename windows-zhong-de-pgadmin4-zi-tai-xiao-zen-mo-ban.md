# Windows 中的 pgAdmin4 字太小怎麼辦？

由於新版的 pgAdmin 4 是以 QT5 開發，所以如果你是在高 DPI 的環境（如 4k 螢幕）下使用 pgAdmin 4 時，字母顯示可能會太小。

因為 QT5 處理 DPI 的方式與 Windows [不太一樣](http://doc.qt.io/qt-5/highdpi.html)，所以你需要做的是讓 pgAdmin 4 不要自動調整字型大小。

而簡單來說，你有兩種作法：（路徑目錄可能需要隨你的安裝環境調整）

## 一、在啓動指令加入參數：

```text
cd "c:\Program Files\PostgreSQL\9.6\pgAdmin 4\bin"
.\pgAdmin4.exe -platform windows:dpiawareness=0
```

或

在捷徑按「內容」，修改「目標」，在後面加入：

```text
-platform windows:dpiawareness=0
```

## 二、設定 qt.conf：

增加一個 qt.conf 設定檔給 pgAdmin 4 使用。

請使用**系統管理者權限**開啓 cmd

```text
cd "c:\Program Files\PostgreSQL\9.6\pgAdmin 4\bin"
notepad qt.conf
```

內容加入：

```text
[Platforms]
WindowsArguments = dpiawareness=0
```

再重啓你的 pgAdmin 4 即可。

