# Character Data Sync（繁中版）

FINAL FANTASY XIV **繁體中文版（台服）** 專用的 Dalamud 插件，可將**主角色**的熱鍵列、巨集、鍵位等設定同步給其他所有角色使用。

本專案移植自 [Xpahtalo/Dalamud.CharacterSync](https://github.com/Xpahtalo/Dalamud.CharacterSync)，以上游最後一個 API13 版本（2.0.0.15）為基礎，並針對台服客戶端進行修正。

> [!WARNING]
> 本插件會攔截遊戲讀取角色設定檔的動作。首次使用前，請先**手動備份**角色設定資料夾：
> `文件\My Games\FINAL FANTASY XIV - TC` 底下所有 `FFXIV_CHR…` 開頭的資料夾。

## 環境需求

- FFXIV 繁體中文版客戶端
- Dalamud **API13**（台服版 Dalamud）

## 與上游版本的差異

| 項目 | 上游 API13 版 | 本版 |
|------|---------------|------|
| 角色設定資料夾 | 寫死為 `My Games\FINAL FANTASY XIV - A Realm Reborn`，台服找不到，導致自動備份失敗 | 直接向遊戲讀取實際的使用者資料夾；讀取失敗時退回 `My Games\FINAL FANTASY XIV - TC` |
| 載入時機 | 未指定 | 加入 `LoadRequiredState` / `LoadSync`，讓插件在登入前載入，避免進入 safe mode |

Hook 使用的函式簽名與上游相同，已在台服客戶端實測可用。

## 可同步的項目

每一項都可以在設定視窗中個別開啟或關閉（預設全部開啟）：

- 熱鍵列（HOTBAR.DAT）
- 巨集（MACRO.DAT）
- 鍵位設定（KEYBIND.DAT）
- 聊天紀錄篩選（LOGFLTR.DAT）
- 角色設定（COMMON.DAT）
- 鍵盤操作設定（CONTROL0.DAT）
- 手把操作設定（CONTROL1.DAT）
- 九宮幻卡牌組與模擬戰設定（GS.DAT）

介面配置（ADDON.DAT）會一律同步。物品排序、套裝、UI 存檔等角色專屬資料不會同步。

## 安裝（開發插件）

1. 安裝 [.NET SDK](https://dotnet.microsoft.com/download)。
2. Clone 本 repo（`tw-api13` 分支）。
3. 在有 `Dalamud.CharacterSync.sln` 的資料夾開啟終端機，指定台服 Dalamud 的位置並編譯：

   ```
   dotnet build -c Release -p:DalamudLibPath="<台服 Dalamud 資料夾>\"
   ```

   例如使用 FFXIVSimpleLauncher 時，路徑為 `%AppData%\FFXIVSimpleLauncher\Dalamud\Injector\`。

   > 若未指定路徑，SDK 會改用國際服 XIVLauncher 的 Dalamud（API15）而導致編譯失敗。

4. 在遊戲中開啟 Dalamud 設定 → 實驗性功能 → 開發插件位置，加入編譯出的 `Dalamud.CharacterSync.dll`。
5. 在插件安裝器的「開發工具」中啟用插件。

## 使用方式

1. 用**主角色**登入，輸入 `/pcharsync` 開啟設定視窗。
2. 按下 **Set save data to current character**，勾選要同步的項目後按 **Save**。
3. **完全關閉遊戲並重新啟動**。
4. 之後登入任何角色，都會使用主角色的設定。

### 注意事項

- 插件必須在**登入角色前**載入才會生效。若在已登入狀態下才啟用插件，會進入 safe mode（不進行同步），重新啟動遊戲即可。
- 同步期間，在分身上修改的設定會**直接寫入主角色的設定檔**。
- 插件每次啟動時會自動備份所有角色設定，存放在 Dalamud 插件設定資料夾的 `backups` 底下，保留最近幾份。

### 確認是否正常運作

輸入 `/xllog` 開啟 Dalamud log，應可看到：

- `FileInterfaceOpenFileAddress` 後面是一個非 0 的位址
- `XIV user folder:` 顯示台服的角色設定資料夾
- `Backup OK!`

## 致謝

- 原作者：goat、Franz
- 上游維護者：[X'pahtalo](https://github.com/Xpahtalo/Dalamud.CharacterSync)
- 台服 Dalamud 插件生態：[cycleapple/DalamudPlugins-TW](https://github.com/cycleapple/DalamudPlugins-TW)
