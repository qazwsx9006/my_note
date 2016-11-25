## 1. ssl 憑證購買使用紀錄 （使用 COMODO）
向 COMODO 購買憑證後，桌機版瀏覽器，IOS 瀏覽器憑證皆可認證。唯獨 android 會出現認證失敗（非安全性瀏覽）。
使用 [https://www.ssllabs.com/ssltest/analyze.html](https://www.ssllabs.com/ssltest/analyze.html) 以及 [https://cryptoreport.websecurity.symantec.com/checker/views/certCheck.jsp](https://cryptoreport.websecurity.symantec.com/checker/views/certCheck.jsp) 做檢測。 ssllabs 顯示 `Chain issues  Incorrect order, Contains anchor`。 cryptoreport: 檢測出錯誤 `Contact the certificate supplier to download and install the missing certificate`。 根據 cryptoreport 訊息，檢查 server ssl 指向的 xxx.crt 。發現內有三個crt 組合。 檢查過後(參考[https://gist.github.com/bradmontgomery/6487319](https://gist.github.com/bradmontgomery/6487319)) 應該要有 4 個 .crt 組合。重組產生新的 .crt 後。
android 即可通過認證。 目前不知道為什麼少一個 .crt 檔 桌機 & IOS 還是會通過。

* `STAR_domain.crt` STAR 表示多個Domain 使用（子網域 *.example.com ）

避免未來該參考的github內容被移除，保留該教學檔案於 [ssl_setting_from_comodo.md](./ssl_setting_from_comodo.md)
