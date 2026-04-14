# LumenX Studio — 前端

LumenX Studio 的前端介面，基於 [Next.js 14](https://nextjs.org) 開發，使用 React 18、TypeScript 與 Tailwind CSS。

## 本機啟動

```bash
npm install --legacy-peer-deps
npm run dev
```

開啟瀏覽器至 [http://localhost:3000](http://localhost:3000) 即可看到介面。

> 後端 API 需同時在 `http://localhost:17177` 運行，完整啟動方式請參閱根目錄 [README.md](../README.md)。

## 可用指令

| 指令 | 說明 |
|------|------|
| `npm run dev` | 啟動開發伺服器（含 Hot Reload） |
| `npm run build` | 建置正式版靜態檔案 |
| `npm run start` | 啟動正式版伺服器 |
| `npm run lint` | 執行 ESLint 程式碼檢查 |

## 技術堆疊

- **Next.js 14** — App Router 架構
- **React 18** — UI 元件框架
- **TypeScript** — 型別安全
- **Tailwind CSS** — 樣式系統
- **Three.js** — 3D 視覺效果

## 目錄結構

```
frontend/
├── src/
│   ├── app/              # Next.js App Router 頁面
│   └── components/       # 依工作流程步驟分組的 UI 元件
├── public/               # 靜態資源
├── tailwind.config.ts    # Tailwind 設定
└── next.config.ts        # Next.js 設定
```

## 相關文件

- [專案總覽 README](../README.md)
- [使用手冊](../USER_MANUAL.md)
- [Next.js 官方文件](https://nextjs.org/docs)
