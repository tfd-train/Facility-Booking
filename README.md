場地借用管理系統 (Facility Booking System)
一個基於 Web 的場地預約與管理系統，專為訓練中心或機關單位設計。本系統採用 Serverless 架構，前端使用 HTML/Tailwind CSS，後端結合 Google Apps Script (GAS) 與 Google Sheets 進行資料儲存，並整合 Google Calendar 顯示預約狀況。
![alt text](https://img.shields.io/badge/license-MIT-blue.svg)

![alt text](https://img.shields.io/badge/version-1.0.0-green.svg)

![alt text](https://img.shields.io/badge/status-Active-success.svg)
✨ 主要功能
預約申請：
支援單日及批次多日預約（可選擇排除週末）。
自動偵測衝堂（需後端配合）。
整合 Google 日曆嵌入顯示。
照片回傳機制：
強制要求「使用前、使用中、歸還後」三階段照片紀錄。
前端自動壓縮圖片：上傳前將圖片壓縮至 800px 寬，節省流量與儲存空間。
缺件提醒：首頁自動偵測已核准但照片未齊全的預約，並以紅燈提醒。
管理與查詢：
即時預約清單與狀態燈號（核准/待審/拒絕/取消）。
支援整批取消或單筆取消功能。
CSV 報表匯出。
RWD 響應式設計：
支援手機版側邊欄選單，行動裝置操作友善。
🛠️ 技術棧
Frontend: HTML5, JavaScript (ES6+), Tailwind CSS (CDN), Remix Icon
Backend: Google Apps Script (GAS)
Database: Google Sheets
Storage: Google Drive (用於儲存上傳的照片)
🚀 部署指南
本系統由兩個部分組成：前端網頁 (index.html) 與 後端腳本 (Google Apps Script)。
步驟 1：準備 Google Sheets (資料庫)
建立一個新的 Google Sheet。
將工作表命名為 Bookings。
建立以下標題列 (第一列)：
id (唯一識別碼)
batchId (批次群組碼)
timestamp (申請時間)
unit (單位)
contact (聯絡人)
phone (電話)
email (Email)
facility (場地)
date (日期 YYYY-MM-DD)
startTime (開始時間)
endTime (結束時間)
purpose (用途)
status (狀態：待審核/已核准/拒絕/取消)
uploadStatus (照片狀態，例：使用前,使用中)
folderUrl (照片資料夾連結)
步驟 2：設定 Google Apps Script (後端 API)
在 Google Sheet 中，點選 擴充功能 > Apps Script。
設定常數：在程式碼最上方，您需要設定一個 Google Drive 資料夾 ID，用來存放上傳的照片。
複製程式碼：由於前端依賴特定的 API 格式，請參考以下 Code.gs 的範本結構（需自行實作詳細邏輯）：
code
JavaScript
// 範本：Code.gs 關鍵結構
const PHOTO_FOLDER_ID = "您的_GOOGLE_DRIVE_FOLDER_ID";

function doGet(e) {
  // 回傳所有預約資料 (JSON 格式)
  const data = getDataFromSheet(); 
  return ContentService.createTextOutput(JSON.stringify(data))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  const params = JSON.parse(e.postData.contents);
  const action = params.action;
  
  if (action === 'create_batch') {
    // 處理批次新增
  } else if (action === 'upload_photo') {
    // 處理照片上傳 (Base64 解碼 -> DriveApp.createFile)
    // 更新 uploadStatus 欄位
  } else if (action === 'cancel_request') {
    // 處理取消
  }
  
  return ContentService.createTextOutput(JSON.stringify({status: 'success'}));
}
部署：
點擊右上角「部署」>「新增部署作業」。
類型選擇「網頁應用程式」。
執行身分：我。
誰可以存取：所有人 (這是關鍵，否則前端無法呼叫)。
複製產生的 網頁應用程式網址 (Web App URL)。
步驟 3：設定前端
開啟 index.html。
找到 <script> 區塊中的設定區：
code
JavaScript
// ==========================================
// ⚠️ 1. GAS 網址
const GAS_ENDPOINT = "您的_GAS_WEB_APP_URL_貼在這裡";

// ⚠️ 2. 日曆網址 (Google Calendar 設定頁面取得嵌入碼中的 src)
const CALENDAR_EMBED_URL = "https://calendar.google.com/calendar/embed?..."; 

// ⚠️ 3. 系統整合連結 (選填)
const PORTAL_URL = "..."; 
const OTHER_SYSTEM_URL = "...";
// ==========================================
填入步驟 2 取得的網址。
📖 使用說明
新增申請：
填寫表單，若需連續借用多天，請展開「多日/連續申請」選單。
勾選安全規範後送出。
補傳照片：
若首頁出現「待補照片提醒」區塊（紅色呼吸燈），點擊按鈕即可上傳。
選擇照片類型（使用前/中/後），系統會自動壓縮並上傳。
取消預約：
在預約清單中，點擊垃圾桶圖示。
若是批次預約，可選擇「整批取消」。
需輸入 Email 進行簡易身分驗證。
⚠️ 注意事項
API 配額：Google Apps Script 對於免費帳號有單日執行次數與執行時間的限制，若使用量極大建議升級 Workspace。
瀏覽器支援：建議使用 Chrome、Edge、Safari 或 Firefox 最新版本。
圖片隱私：上傳的照片將存放於您的 Google Drive，請確保資料夾權限設定正確。
🤝 貢獻
歡迎提交 Pull Request 或 Issue 來改進這個專案。
