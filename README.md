# 斜頸角度紀錄（Torticollis Angle Recorder）

以手機拍攝幼童正面半身照，自動畫出「雙肩連線」與「頭部中線（兩眼中點—頸點）」，計算小於 90° 一側的夾角，並匯出含量測資訊的圖片。

> 本工具僅為輔助紀錄之工具，不具醫療診斷效力，不可作為治療決策之唯一依據。

## 部署到 GitHub Pages

1. 建立新 repo（例如 `torticollis-angle`），上傳 `index.html` 與本 README。
2. 進入 repo 的 **Settings → Pages**，Source 選 `Deploy from a branch`，Branch 選 `main` / `(root)`，儲存。
3. 數分鐘後以 `https://<帳號>.github.io/torticollis-angle/` 開啟。

APP 內相機需要 HTTPS，GitHub Pages 已符合。以 `file://` 直接開啟時，相機會改用手機內建相機，且偵測模型可能因 CORS 無法載入。

## 外部相依

| 項目 | 來源 |
|---|---|
| MediaPipe Tasks Vision 1.0.1（JS + WASM） | cdn.jsdelivr.net |
| Pose Landmarker（full）模型 | storage.googleapis.com |
| Face Landmarker 模型 | storage.googleapis.com |

以上網址集中在 `index.html` 的 `CONFIG` 區塊。若要避免外部 CDN 變動，可把 WASM 與 `.task` 模型檔放進 repo，再把 `CONFIG` 中的網址改成相對路徑。

## 量測定義

- 肩線：右肩點 → 左肩點（MediaPipe Pose #11、#12）。
- 中線：兩眼中點（Face Landmarker 虹膜中心 #468、#473）→ 頸點。
- 頸點：預設為雙肩中點，可手動指定；按「頸點回到雙肩中點」可恢復。
- 鼻尖只作參考顯示，不參與計算。
- 夾角 = 90° − |中線相對肩線垂直方向的偏離角|；偏向側依兩眼中點相對頸點的位置判定。
- 左右以孩童本人為準（假設照片未鏡像）。
- 年齡以曆法計算；出生日在拍攝月份不存在時（如 31 日），以該月最後一天計。

## 品質檢查門檻

`CONFIG` 中的下列數值為初始設定，**需以實際幼童照片校正**：

| 參數 | 意義 | 預設 |
|---|---|---|
| `minShoulderVisibility` | 肩點信心值下限 | 0.5 |
| `maxYawRatio` | 鼻尖偏離臉寬中心比例上限（轉頭） | 0.12 |
| `minShoulderWidthRatio` | 雙肩寬／影像寬下限 | 0.18 |
| `minSharpness` | 清晰度下限 | 15 |
| `minBrightness` / `maxBrightness` | 亮度範圍 | 50 / 225 |
| `maxPhonePitch` | APP 內拍攝時手機前後傾斜上限 | 10° |
| `maxPhoneRoll` | APP 內拍攝時手機左右傾斜提醒 | 5° |

## 隱私

照片、姓名、出生日期只在瀏覽器內處理，不上傳、不保存。請勿把孩童照片提交到 repo。

## 版本

- 0.2.1：垂直參考虛線加長；角度數值標籤移到頭頂上方，避免與鼻尖點重疊；「頭偏左／右」與角度數值垂直置中對齊。
- 0.2.0：角度改為「與垂直線的偏差角」；確認點位畫面可縮放／平移檢視；匯出前可裁切調整照片範圍（不變形）；匯出圖片內容精簡為角度、姓名、年齡、拍攝時間。
- 0.1.0：第一版。
