Cloudflare 批量網域導轉
===

方法一、 網頁規則
---
> * `需要的權限Token: 區域-網頁規則-編輯`
> * 使用網頁規則，dashbord上直接點擊API，就會秀出API，比較直觀，是相對簡單的方法。
> * 在 free 方案時沒有辦法加上國別判斷，pro 方案好像可以

基本上接 api 的步驟需要
1. 先取得 zoneId: https://api.cloudflare.com/client/v4/zones?name=[domain_name]
2. 取得 page rule: https://api.cloudflare.com/client/v4/zones/[zone_id]/pagerules
3. - 如果沒有rule，新增 post: https://api.cloudflare.com/client/v4/zones/[zone_id]/pagerules
   - 如果有rule，則更新 put: https://api.cloudflare.com/client/v4/zones/[zone_id]/pagerules/[rule_id]


方法二、 Workers
---
> * 此為方案 free 版本，Pro版也許有更好作法。不過步驟也並不複雜
> * 此方法須注意 free 方案有使用次數限制
> * 使用這個方法算是在有大量且頻繁更換網域導轉位置時，可以不用接API的方法
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

方法三、 重新導向轉接規則 (rulesets)
---
> * `需要的權限Token: 區域-單一重新導向轉接-編輯`
> * 使用網頁規則沒辦法設定一些邏輯判斷，例如判斷來源國家，這時可以使用重新導向轉接規則

接 api 大致跟網頁規則差不多，稍微有點不同，只是範例比較難找
1. 先取得 zoneId: https://api.cloudflare.com/client/v4/zones?name=[domain_name]
2. 這個例子要新增 http_request_dynamic_redirect，http_request_dynamic_redirect 在 rulesets 中似乎只能有一個  
   直接新增 rulesets: post: https://api.cloudflare.com/client/v4/zones/[zond_id]/rulesets
3. 新增過一次以後即使在介面中的網頁刪除該規則，呼叫 get: https://api.cloudflare.com/client/v4/zones/[zone_id]/rulesets  
   查看規則集，此 http_request_dynamic_redirect 規則也不會被刪除，但介面是可以刪除此規則的，只是畫面也會變得跟尚未新增以前不一樣  
   有點奇怪，感覺是 cloudflare沒改好。所以之後若有要修改就只要使用  
   put: https://api.cloudflare.com/client/v4/zones/[zone_id]/rulesets/[rule_id]

ex. 官方新增的範例
``` json
{
  "name": "Redirect rules ruleset",
  "kind": "zone",
  "phase": "http_request_dynamic_redirect",
  "rules": [
    {
      "expression": "(ip.src.country eq \"GB\" or ip.src.country eq \"FR\") and http.request.uri.path eq \"/\"",
      "description": "Redirect GB and FR users in home page to localized site.",
      "action": "redirect",
      "action_parameters": {
        "from_value": {
          "target_url": {
            "expression": "lower(concat(\"https://\", ip.src.country, \".example.com\"))"
          },
          "status_code": 307,
          "preserve_query_string": true
        }
      }
    }
  ]
}
```

API Token
---
API Token 使用右上角的建立(如圖)
* 不建議在帳號底下的左側欄 `管理帳號->帳戶 API 權杖` 建立token  
  雖然選擇了區域，但不知為何就是大部分api都無法呼叫，一直顯示不支持user token
* 可以使用 API 金鑰 Global API Key，應該都能通，不過似乎是最大權限，官方比較不建議這樣做
![image](https://github.com/user-attachments/assets/5cb5b959-4b60-4b14-83b7-56158d9cca89)
