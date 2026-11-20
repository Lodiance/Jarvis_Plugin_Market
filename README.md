# Jarvis Plugin Market

Jarvis 市集的目錄。Jarvis 在自己的「市集」頁讀這裡的兩份檔案，讓使用者瀏覽、挑選 plugin；
plugin 的程式碼留在開發者自己的公開 repo，這裡只記錄「去哪裡拿、拿哪一版」。

| 檔案 | 內容 | 誰能加 |
|---|---|---|
| `official.json` | 官方目錄 | 只有維護者 |
| `community.json` | 社群目錄 | 還沒開放（見下方） |

Jarvis 讀的是 raw 檔：

```text
https://raw.githubusercontent.com/Lodiance/Jarvis_Plugin_Market/main/official.json
https://raw.githubusercontent.com/Lodiance/Jarvis_Plugin_Market/main/community.json
```

## 格式

```json
{
  "catalog": 1,
  "name": "目錄名稱",
  "plugins": [
    {
      "id": "plugin 的 id（和 manifest.json 的 id 相同）",
      "name": "顯示名稱",
      "description": "一兩句說明",
      "author": "repo 擁有者的 GitHub 帳號",
      "repo": "https://github.com/<帳號>/<repo>.git",
      "listed_at": "YYYY-MM-DD",
      "versions": [
        {
          "version": "manifest.version",
          "spec": "manifest.spec",
          "tag": "v1.2.3",
          "sha": "tag 指向的 commit，40 碼",
          "permissions": { "network": false, "wiki": false, "secrets": [] },
          "checks": {},
          "checked_at": null
        }
      ]
    }
  ],
  "revoked": [
    { "id": "plugin id", "sha": "省略代表所有版本", "reason": "下架原因", "at": "YYYY-MM-DD" }
  ]
}
```

- **一個版本一筆，鎖在 `sha`**：Jarvis 安裝時 clone `repo`、切到這個 commit，對不上就拒絕安裝。已經列出的版本不能改，要改就加新版本。
- `permissions` 只給市集頁顯示；安裝時一律以 repo 裡的 `manifest.json` 為準，兩邊不一致時 Jarvis 會警告。
- `checks` 是自動檢查的評分卡，`checked_at` 是檢查時間。上架的自動檢查還沒做，目前是空的。
- `revoked`：從這份目錄安裝、`id`（和 `sha`，有寫的話）符合的 plugin，Jarvis 讀到後會停掉它並顯示原因，由使用者決定移除或繼續用。
- 檔案用 UTF-8、LF。

## 上架

- 官方目錄：維護者直接修改。
- 社群目錄：還沒開放。開放前要先完成兩件事：plugin 的 `network` 權限改成宣告要連的網域清單，以及這個 repo 的上架自動檢查
  （repo 擁有者、帳號年齡、tag 和 `manifest.version` 一致、`check` 沒有錯誤、`id` 不重複也不冒用官方名稱、lockfile、`npm audit`）。
  開放之後，開發者對 `community.json` 發 PR，檢查全過就自動合併，不經過人工審核。

自己用、不打算上架的 plugin 不需要這裡：在 Jarvis 的市集頁貼 repo 網址就能安裝。

## 回報問題 plugin

開一個 issue，寫明 plugin 的 `id`、版本和問題。確認有問題的版本會列進 `revoked`。
