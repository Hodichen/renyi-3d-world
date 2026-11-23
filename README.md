# 三重漫遊 交接說明（V51｜2026/11/23）

一個 3D 開放世界遊戲（GTA 風格），用 Three.js r128 寫成，整個遊戲在 index.html 一個檔案裡（目前約 4600 行）。
地點：新北市三重 仁義街 → 五華國小，照真實街景 1:1 重現。

## 檔案
- index.html：遊戲本體
- people/（people.zip）：寫實人物模型（meshopt GLB 轉成 base64 的 .b64.txt）＋動作 motions.json、motions2.json
- cars/real/（cars.zip）：6 款真車模型
- people/ 和 cars/ 要放在 index.html 旁邊。要用網頁伺服器開（GitHub Pages 或 `python -m http.server`），直接點兩下開會讀不到模型。

## 修改規則
1. 檔案很大，請不要重寫整個檔案，只給「找到哪一段 → 換成什麼」，或給改好的完整檔案下載。
2. 不要動人物系統（RIG、rigBuild、npcRigUpdate、allRigTick）和真車系統（carRigUpdate、CARLIST）。
3. 有版權的店名改一個字、顏色不變（seven eleven → 7-TEN、全家 → 全佳 之類）。

## 座標（x 往東、z 往北是負的）
- 仁義街 x≈0；左側店面前緣 x=-9.6、後牆 x=-27
- 巷子：93巷 z=4、230巷 z=65、228巷 z=92、71巷 z=134
- 五華國小 x[-52,55] z[-412,-186]

## 常用函式
- boxR(x1,x2,z1,z2,y0,h,材質或[px,nx,py,ny,pz,nz])：方塊。左側建築朝仁義街的是 px 面（材質陣列第 1 個）
- flat、vplane(x,y,z,w,h,mat,ry)、cyl、tex、signMat、addCol（碰撞）、addInteract(x,z,r,文字,fn)、addLight
- 可走進的小店：SHOPIN({x1,x2,z1,z2,face:'px'或'pz',door:[u0,u1],kind:'food'|'hotpot'|'breakfast'|'grocery'|'pet'|'chicken',th:{key,name,hello,sub,c1},sign(g,W,H),menu})
- 可走進的便利商店：WS({...})（全家、7-TEN、美連社）
- 自動門：AUTODOORS；購物：openShop、buy、STORE_ITEMS、window.EXTRA_ITEMS[key]（菜單）

## 目前可以走進去的店（左側，由北到南）
全家(z -37~-25)、金華雜貨店(-20~-9)、上鮮涮涮鍋(-8.8~1.5)、7-TEN(49.6~61.6)、意麵寶貝／寵物店(228巷內)、美連社(94.8~107.2)、煮婦救心(107.8~118.2)、炸不停(118.8~131.2)、JSP呷上寶(136.8~149.7)

## 待辦
- 較早做的店還不能進去：妘芳早午餐、公益彩券、嘉義雞肉飯、多一點團購、咪啼早餐、漢堡大帥、泓陞機車、茂源地瓜、魷鮮攤、右側 7-TEN → 用 SHOPIN 改
- 換成真人後，手上拿的東西（掃把、球）看不到
- 攻擊鍵接上出拳動作（jab/cross）
- 賺錢系統（還沒設計）
- iPad 流暢度（真人 18 個、真車 14 台）


## 版本歷程與部署
- **V51（2026/11/23）**：起始畫面顯示版本及更新日期；原創霓虹電影片頭、3D 街景運鏡與原創 Web Audio 電子配樂。點「▶ 播放電影片頭」開始有聲動畫；因瀏覽器禁止自動播放有聲音，須由玩家點擊啟動；可略過、關閉音樂，開始遊戲後配樂淡出。片頭版本在 `GAME_META` 管理，更新時同時修改畫面版本與日期。
- **V50（2026/11/23）**：仁義街路邊轎車、E 鍵上下車、加速駕駛；簡化行人／機車／腳踏車碰撞受傷與恢復；台灣時間、Open-Meteo 三重附近即時天氣。為輕量遊戲模擬，非完整真實剛體物理。
- GitHub Pages 正式測試入口：https://hodichen.github.io/renyi-3d-world/
- GitHub `main` 是正式原始碼版本；Claude Artifact 不會自動從 GitHub 同步。所有修改先確認最新 commit，避免覆寫另一個 AI 的變更。

## 開場音樂與測試注意事項
- 配樂完全由 Web Audio API 即時合成，沒有使用外部版權音樂；音訊在按「▶ 播放電影片頭」或「♫ 音樂：關」後啟用。
- 建議測試：電腦 Chrome、手機/iPad Safari 的有聲播放、略過、開始漫遊是否正常；注意低功耗裝置的 3D 畫面與音效效能。
- 本版已做 JavaScript 語法檢查；不等同完成跨裝置實際遊玩驗收。
