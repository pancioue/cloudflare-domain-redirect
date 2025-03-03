# Cloudflare 批量網域導轉
> * 單網域導轉需求可以直接使用 Page Rules
> * 此為方案 free 版本，Pro版也許有更好作法。不過步驟也並不複雜

## 步驟一
1. 到帳號底下建立worker
![創建worker](https://github.com/user-attachments/assets/840ea81a-0224-4c0f-8337-c9a60d577bac)

2. 點選以後可能會看到 `從範本開始`，直接點選 `Hello World`
3. 直接點選部屬即可，之後可以再改 **worker.js** 檔案(不過名稱之後要更改的話，功能似乎會影響，所以如果需要有意義的名稱，可能須先設定好)
![deploy-hello-world](https://github.com/user-attachments/assets/fbfb3c4b-e0a3-4007-9bc4-ff5f8e78c654)


## 步驟二
1. 建立完回到 **Workers 和 Pages**，應該會出現該worker，如圖一畫黑線的地方，可以點擊 *造訪* 測試看看
2. 進到 worker 後可以點選編輯剛才的 **worker.js**檔
   ![點選編輯worker](https://github.com/user-attachments/assets/fcb7ca94-beda-47f9-b9ec-63eb3708f0a2)
3. 編輯完要跳轉的邏輯，點選套用即可
   ![編輯完套用](https://github.com/user-attachments/assets/a8069081-c06d-4164-8c84-6c7ddf5dd0a8)

## 步驟三
1. 到要跳轉的網域設定
   ![要跳轉的網域Workers路由](https://github.com/user-attachments/assets/7719886e-8ded-4b47-b3f2-a53669f250f1)
2. 寫入想要的規則，點選剛產生的 Worker

## 完成
如此一來便算設定完了，所有網域只要設定一次即可。之後如果有導轉網域更換，只要更改 worker.js 裡面的url，便可把所有設定好的網域導轉
