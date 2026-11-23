# 三重漫遊 交接說明（V54｜2026/11/23）

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
- **V54（2026/11/23）**：依使用者截圖修正 GLB 來源的輪組方向：3DAssets.dev 30073/30074 的腳踏車車架長軸為 X，但輪胎網格的原始軸也沿 X，造成輪面朝錯方向；在各輪 pivot 下的 mesh 做局部 Y 軸旋轉 90°，保持 pivot 原位置，再沿正確 Z 軸滾動。機車原輪軸方向正確不旋轉。載入 GLB 後先做世界座標 Box3 接地校正，依前後輪處地面高度取較高者避免穿越人行道；碰撞翻倒與移動時，定期以模型最低點調整垂直位置，避免輪胎穿入地板。保留 E 上下車、原有真車與 NPC 系統，修正項目為 `v54TerrainUnderWheels`、`v54SnapVehicleToGround`、`v54WheelGroundTick`，並提供 `window.__v54.debug` 可供測試。**已通過 JavaScript 語法檢查，仍需 Chrome 與 iPad 實際視覺驗收。**
- **V53（2026/11/23）**：依遊戲實拍回饋修正機車與腳踏車高度：停放車、AI 車、店外展示車以 `groundY` 地面高度定位，GLB 以車輪最低點貼地，修正長軸沿 X 的腳踏車輪旋轉方向。全面移除方塊轎車外觀：`makeCar`、`regCar` 與 `decorCar` 的方塊車不再被渲染或靜態網格合併，既有 `CARLIST` 和 `carRigUpdate` 繼續載入真實 GLB 車款；沒有真車時保持空車位，不顯示方塊替代。舊方塊路邊機車與店家展示腳踏車也改以 CC0 GLB 取代，遠處展示車做可見距離控制。保留 E 鍵上下車、WASD、交通與碰撞。注意：模型依賴 CDN 和 `cars/real`，遊玩需驗證真車載入與車體地面接觸。  
- **V52（2026/11/23）**：以 CC0 速克達與城市腳踏車的 GLB 模型替換原本程序建模外觀；保留 `makeScooter`/`makeBike` 的騎乘及碰撞資料、E 鍵上下車、WASD、騎士坐姿。新增固定停放的腳踏車（含出生點附近），路上 AI 同樣換模型。模型透過原版 Three.js r128 `GLTFLoader` 從 3DAssets.dev 的永久 CDN 載入（無須 KTX2/Draco/Meshopt）；網路失敗自動維持舊外觀。程式參考：`V52_ASSET_SOURCES`、`v52SetupVehicle`、`v52LoadBikeModels`、`v52WheelTick`。模型僅替換外觀，不應重寫車輛物理或人物 RIG。

- **V51（2026/11/23）**：起始畫面顯示版本及更新日期；原創霓虹電影片頭、3D 街景運鏡與原創 Web Audio 電子配樂。點「▶ 播放電影片頭」開始有聲動畫；因瀏覽器禁止自動播放有聲音，須由玩家點擊啟動；可略過、關閉音樂，開始遊戲後配樂淡出。片頭版本在 `GAME_META` 管理，更新時同時修改畫面版本與日期。
- **V50（2026/11/23）**：仁義街路邊轎車、E 鍵上下車、加速駕駛；簡化行人／機車／腳踏車碰撞受傷與恢復；台灣時間、Open-Meteo 三重附近即時天氣。為輕量遊戲模擬，非完整真實剛體物理。
- GitHub Pages 正式測試入口：https://hodichen.github.io/renyi-3d-world/
- GitHub `main` 是正式原始碼版本；Claude Artifact 不會自動從 GitHub 同步。所有修改先確認最新 commit，避免覆寫另一個 AI 的變更。

## 開場音樂與測試注意事項
- 配樂完全由 Web Audio API 即時合成，沒有使用外部版權音樂；音訊在按「▶ 播放電影片頭」或「♫ 音樂：關」後啟用。
- 建議測試：電腦 Chrome、手機/iPad Safari 的有聲播放、略過、開始漫遊是否正常；注意低功耗裝置的 3D 畫面與音效效能。
- 本版已做 JavaScript 語法檢查；不等同完成跨裝置實際遊玩驗收。

## V52 免費 3D 模型來源
- 通勤速克達：https://3dassets.dev/assets/motorcycle-racing-and-street-bikes-commuter-scooter-6889697c
  - GLB：https://cdn.3dassets.dev/assets/15431/v1/model.glb
- 城市腳踏車（綠）：https://3dassets.dev/assets/japanese-school-and-city-street-bicycle-commuter-d3ed9187
  - GLB：https://cdn.3dassets.dev/assets/30073/v1/model.glb
- 城市腳踏車（紅）：https://3dassets.dev/assets/japanese-school-and-city-street-bicycle-commuter-red-a66398c4
  - GLB：https://cdn.3dassets.dev/assets/30074/v1/model.glb
- 三者皆 CC0 1.0；可商業使用，不需署名。直接 CDN 載入會依賴外部服務，日後可自行下載 GLB 存入專案後改為相對路徑，降低斷線風險。
- 驗收：Chrome/iPad 載入後檢查車體朝向、車輪動畫、E 鍵上下車、玩家騎乘、AI 行車及撞擊翻車；語法檢查通過不代表實際瀏覽器測試完成。
