# Scratch GUI 專案說明

## 專案概述
這是一個基於 Scratch 3.0 的圖形化使用者介面專案，用於創建和運行 Scratch 專案。本專案由麻省理工學院開發，採用 BSD-3-Clause 授權。

## 專案結構
```
app/
├── src/            # 源代碼目錄
│   ├── components/     # React 組件
│   ├── containers/     # Redux 容器組件
│   ├── reducers/       # Redux 狀態管理
│   ├── lib/           # 工具函數和通用邏輯
│   ├── css/           # 樣式文件
│   ├── firmware/      # 硬體相關代碼
│   └── examples/      # 示例代碼
├── test/           # 測試文件目錄
├── translations/   # 多語言翻譯文件
├── static/         # 靜態資源文件
├── build/          # 構建輸出目錄
├── dist/           # 分發文件目錄
└── docs/           # 文檔目錄
```

## 技術棧
- React 16.0.0
- Redux 3.7.2
- Webpack 4.46.0
- Jest (測試框架)
- ESLint (代碼檢查)

## 主要依賴
- scratch-blocks: Scratch 積木系統
- scratch-vm: Scratch 虛擬機
- scratch-render: Scratch 渲染引擎
- scratch-audio: 音頻處理
- scratch-paint: 繪圖功能
- scratch-storage: 存儲系統

## 架構詳解

### 前端框架
- **React 16.0.0**：用於構建用戶界面
- **Redux 3.7.2**：狀態管理
  - 使用 `guiReducers` 統一管理狀態
  - 包含 `locales`、`scratchGui` 和 `scratchPaint` 三個主要 reducer

### 核心模組
1. **GUI 容器** (`containers/gui.jsx`)
   - 整個應用的主要容器組件
   - 負責整合所有子組件

2. **狀態管理** (`reducers/`)
   - `gui.js`：GUI 相關狀態
   - `locales.js`：國際化狀態
   - `project-state.js`：專案狀態管理

3. **工具庫** (`lib/`)
   - `app-state-hoc.jsx`：高階組件，處理應用狀態
   - 其他工具函數和通用邏輯

### Scratch 核心功能
1. **積木系統** (`scratch-blocks`)
   - 提供可視化程式設計介面
   - 支援拖放式程式設計

2. **虛擬機** (`scratch-vm`)
   - 執行 Scratch 程式
   - 處理程式邏輯和運算

3. **渲染引擎** (`scratch-render`)
   - 處理圖形渲染
   - 支援精靈（Sprite）動畫

4. **音頻系統** (`scratch-audio`)
   - 處理音頻播放
   - 支援音效和音樂

5. **繪圖系統** (`scratch-paint`)
   - 提供繪圖功能
   - 支援精靈編輯

### 開發工具
1. **Webpack 4.46.0**
   - 模組打包
   - 開發服務器
   - 資源優化

2. **Jest**
   - 單元測試
   - 集成測試
   - 測試覆蓋率報告

3. **ESLint**
   - 代碼風格檢查
   - 錯誤檢測
   - 最佳實踐強制執行

## 擴展系統

### 核心組件
1. **ExtensionWorker**
   - 負責管理擴展的生命週期
   - 處理擴展的註冊和初始化
   - 管理擴展的通信

2. **ExtensionLibrary**
   - 提供擴展選擇界面
   - 管理擴展的加載和卸載
   - 處理擴展的配置

### 預設擴展
系統預設了幾種主要擴展：
- Music（音樂）
- Pen（畫筆）
- Video Sensing（視訊感測）

### 擴展開發
1. **基本結構**
```javascript
var ExampleExtension = function () {
};

ExampleExtension.prototype.getInfo = function () {
    return {
        id: 'extensionId',        // 擴展唯一識別碼
        name: 'Extension Name',   // 擴展名稱
        blocks: [                 // 擴展提供的積木
            {
                opcode: 'blockId',
                blockType: Scratch.BlockType.COMMAND,
                text: 'block text',
                func: 'functionName'
            }
        ]
    };
};
```

2. **擴展註冊**
```javascript
Scratch.extensions.register(new ExampleExtension());
```

### 擴展功能實現
1. **積木定義**
   - `opcode`：積木的唯一識別碼
   - `blockType`：積木類型（COMMAND/REPORTER/BOOLEAN/HAT）
   - `text`：積木顯示文字
   - `func`：實現函數名稱

2. **參數定義**
```javascript
arguments: {
    PARAM_NAME: {
        type: Scratch.ArgumentType.NUMBER,
        defaultValue: 1
    }
}
```

3. **功能實現**
```javascript
ExampleExtension.prototype.functionName = function (args) {
    // 實現邏輯
    return result;
};
```

### 擴展系統特點
1. **模組化設計**
   - 每個擴展都是獨立的模組
   - 可以動態加載和卸載
   - 支援熱插拔

2. **多語言支援**
```javascript
translation_map: {
    'zh-TW': {
        'extensionName': '擴展名稱',
        'blockText': '積木文字'
    }
}
```

3. **安全性**
   - 擴展運行在獨立的 Worker 中
   - 使用消息傳遞機制進行通信
   - 隔離擴展的執行環境

### 使用方式
1. **加載擴展**
   - 通過擴展庫界面選擇
   - 或通過 URL 直接加載
```javascript
vm.extensionManager.loadExtensionURL(url);
```

2. **擴展管理**
   - 可以動態啟用/禁用擴展
   - 支援擴展的配置和設置
   - 提供擴展狀態監控

### 開發建議
1. **最佳實踐**
   - 使用有意義的擴展 ID
   - 保持積木文字簡潔明了
   - 實現適當的錯誤處理機制
   - 提供用戶友好的錯誤提示
   - 避免阻塞主線程
   - 優化資源使用

2. **測試**
   - 編寫單元測試
   - 進行集成測試
   - 確保跨平台兼容性

## 開發指南

### 環境設置
1. 安裝 Node.js 環境
2. 執行 `npm install` 安裝依賴

### 常用命令
- `npm start`: 啟動開發服務器
- `npm run build`: 構建生產版本
- `npm test`: 運行所有測試
- `npm run test:unit`: 運行單元測試
- `npm run test:integration`: 運行集成測試
- `npm run test:lint`: 運行代碼檢查

### 開發流程
1. 使用 `npm start` 啟動開發服務器
2. 修改源代碼（位於 `src` 目錄）
3. 運行測試確保功能正常
4. 使用 `npm run build` 構建生產版本

### 本地開發詳解
1. **環境設置**
   ```bash
   npm install        # 安裝依賴
   npm start         # 啟動開發服務器
   ```

2. **代碼組織**
   - 組件開發在 `components/` 目錄
   - 狀態邏輯在 `reducers/` 目錄
   - 工具函數在 `lib/` 目錄

3. **測試流程**
   ```bash
   npm run test:unit           # 單元測試
   npm run test:integration    # 集成測試
   npm run test:lint          # 代碼檢查
   ```

### 構建流程
1. **開發構建**
   ```bash
   npm run build    # 生成開發版本
   ```

2. **生產構建**
   ```bash
   npm run build    # 生成生產版本
   ```

## 擴展性設計

### 模組化設計
- 每個功能模組都是獨立的
- 使用 Redux 進行狀態管理
- 支援插件式擴展

### 國際化支援
- 使用 `translations/` 目錄管理多語言
- 支援動態切換語言
- 使用 `npm run i18n:src` 更新翻譯

## 授權
本專案採用 BSD-3-Clause 授權。詳見 LICENSE 文件。

## 相關資源
- [GitHub 倉庫](https://github.com/LLK/scratch-gui)
- [官方文檔](https://github.com/LLK/scratch-gui#readme)

## 注意事項
- 確保使用 Node.js 兼容版本
- 開發時請遵循專案的代碼規範
- 提交代碼前請運行完整的測試套件
