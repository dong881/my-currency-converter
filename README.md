# 貨幣轉換器 (Currency Converter)

一個使用 Vue.js 3 建置的貨幣轉換器，可透過 GitHub Actions 自動部署到 GitHub Pages。

## 功能特色

- 🌍 支援多種國際貨幣轉換
- 💱 即時匯率查詢（使用免費 API）
- 🔄 快速交換來源與目標貨幣
- 📱 響應式設計，支援各種裝置
- 🚀 自動化部署到 GitHub Pages

## 支援貨幣

包含但不限於：
- 美元 (USD)、歐元 (EUR)、英鎊 (GBP)
- 新台幣 (TWD)、人民幣 (CNY)、港幣 (HKD)
- 日圓 (JPY)、韓元 (KRW)、新加坡幣 (SGD)
- 以及更多其他國際貨幣

## API 使用

本專案使用 [ExchangeRate-API](https://www.exchangerate-api.com/) 免費版本，無需 API 金鑰即可使用。

### 如果需要更高級功能

如需要更高的 API 請求限制或額外功能，可以：

1. 在 [ExchangeRate-API](https://www.exchangerate-api.com/) 註冊免費帳號取得 API 金鑰
2. 在 GitHub 專案設定中添加 Secret：`VITE_EXCHANGE_API_KEY`
3. 修改 `CurrencyConverter.vue` 中的 API 呼叫以使用該金鑰

## 本地開發

### 專案設定
```bash
npm install
```

### 開發伺服器（熱重載）
```bash
npm run serve
```

### 建置生產版本
```bash
npm run build
```

### 代碼檢查與修正
```bash
npm run lint
```

## GitHub Pages 部署

### 自動部署

本專案已設定 GitHub Actions，當程式碼推送到 `main` 分支時會自動：
1. 安裝依賴
2. 建置專案
3. 部署到 GitHub Pages

### 手動設定步驟

1. 在 GitHub 專案的 Settings > Pages 中：
   - Source: 選擇 "GitHub Actions"
   
2. 推送程式碼到 main 分支即會觸發自動部署

3. 部署完成後，網站將可在以下網址訪問：
   ```
   https://[你的用戶名].github.io/my-currency-converter/
   ```

### 配置說明

- `vue.config.js` 已設定 `publicPath` 為 `/my-currency-converter/`
- `.github/workflows/deploy.yml` 定義了自動化部署流程
- 不需要額外的 API 金鑰即可運作（使用免費 API）

## 技術棧

- Vue.js 3
- Vue CLI
- ExchangeRate-API（免費匯率 API）
- GitHub Actions
- GitHub Pages

## 自訂配置

查看 [Configuration Reference](https://cli.vuejs.org/config/)。

## 授權

MIT License

