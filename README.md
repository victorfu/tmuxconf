# Victor's tmux Configuration

以 Vim 風格操作為核心的個人 tmux 設定，整合窗格管理、系統剪貼簿、SSH 工作流程，以及 Powerline 風格狀態列。

前綴鍵採用 **`Ctrl+a`**，適合習慣鍵盤操作、經常在本機與遠端終端機之間切換的開發者。

## 特色

- **Vim 風格操作**：使用 `h/j/k/l` 切換窗格，搭配 vi 複製模式。
- **延續工作目錄**：新增視窗與分割窗格時，沿用目前窗格的工作目錄。
- **剪貼簿整合**：透過 OSC 52 傳送複製內容，macOS 額外使用 `pbcopy`。
- **SSH 工作辨識**：在 SSH 環境載入設定時，將狀態列移至頂端。
- **終端機功能**：設定 True Color、延伸按鍵、焦點事件與 escape sequence passthrough。
- **便利的日常操作**：啟用滑鼠、50,000 行歷史緩衝區，以及從 1 開始的視窗與窗格編號。
- **選用工作階段保存**：可透過 TPM 安裝 `tmux-resurrect`。

## 環境需求

| 項目 | 說明 |
| --- | --- |
| tmux | 建議使用近期版本。設定檔註記的基礎需求為 3.2，但 `allow-passthrough` 等選項需要更新版本。 |
| Git | 用於下載與更新設定。 |
| 終端機 | 建議支援 256 色、True Color 與 OSC 52；實際功能取決於終端機設定。 |
| 字型 | 狀態列使用 Powerline 分隔符號，請使用包含這些字元的字型，例如 Nerd Font。 |

先確認 tmux 已安裝：

```sh
tmux -V
```

## 安裝

### 1. 下載專案

```sh
git clone https://github.com/victorfu/tmuxconf.git ~/tmuxconf
```

### 2. 備份既有設定

若已有 `~/.tmux.conf` 或 `~/.tmuxline`，請先備份。以下指令也會保留既有的符號連結：

```sh
backup_suffix="$(date +%Y%m%d-%H%M%S)"
for config in "$HOME/.tmux.conf" "$HOME/.tmuxline"; do
  if [ -e "$config" ] || [ -L "$config" ]; then
    mv "$config" "$config.backup-$backup_suffix"
  fi
done
```

### 3. 建立符號連結

```sh
ln -s ~/tmuxconf/tmux.conf ~/.tmux.conf
ln -s ~/tmuxconf/tmuxline ~/.tmuxline
```

### 4. 啟動或重新載入

建立新的工作階段：

```sh
tmux new-session -s work
```

若 tmux 已經在執行，可在終端機載入設定：

```sh
tmux source-file ~/.tmux.conf
```

載入後，也可以使用 **`Ctrl+a` → `r`** 重新載入。部分設定，例如歷史緩衝區大小與窗格內的終端機環境，需在新建窗格或視窗中確認效果。

## 快捷鍵

以下的 **Prefix** 代表先按 `Ctrl+a`，放開後再按指定按鍵。原本的 `Ctrl+b` 前綴鍵已停用。

### 基本操作

| 快捷鍵 | 功能 |
| --- | --- |
| Prefix → `Ctrl+a` | 將原始 `Ctrl+a` 傳送給窗格內的程式，例如 shell 的移至行首操作。 |
| Prefix → `r` | 重新載入 `~/.tmux.conf`。 |
| Prefix → `c` | 在目前工作目錄建立新視窗。 |
| Prefix → `b` | 切換至上一次使用的視窗。 |
| Prefix → `p` / `n` | 切換至上一個／下一個視窗（tmux 預設）。 |
| Prefix → `1` … `9` | 依編號切換視窗（tmux 預設）。 |
| Prefix → `d` | 離開工作階段，讓它繼續在背景執行（tmux 預設）。 |

重新連回工作階段：

```sh
tmux attach-session -t work
```

### 分割窗格與版面配置

| 快捷鍵 | 功能 |
| --- | --- |
| Prefix → `"` | 上下分割，沿用目前工作目錄。 |
| Prefix → `%` | 左右分割，沿用目前工作目錄。 |
| Prefix → `h` / `j` / `k` / `l` | 切換至左／下／上／右窗格。 |
| `Alt+h` / `Alt+j` / `Alt+k` / `Alt+l` | 直接切換窗格，不需 Prefix。 |
| Prefix → `H` / `J` / `K` / `L` | 向左／下／上／右調整窗格大小，每次 5 格；可連續按鍵。 |
| Prefix → `m` | 主窗格置於左側，其餘窗格排列於右側（`main-vertical`）。 |
| Prefix → `t` | 平鋪窗格（`tiled`）。 |
| Prefix → `f` | 左右等寬排列（`even-horizontal`）。 |

在 iTerm2 中使用 `Alt` 快捷鍵時，需將對應的 Option 鍵設定為傳送 `Esc+`。

### 複製與貼上

先按 **Prefix → `[`** 進入複製模式，再使用下列按鍵：

| 按鍵 | 功能 |
| --- | --- |
| `h` / `j` / `k` / `l` | 移動游標。 |
| `v` | 開始選取。 |
| `Ctrl+v` | 切換矩形選取。 |
| `y` / `Enter` | 複製選取內容並離開複製模式。 |
| `H` / `L` | 移至行首／行尾。 |
| `Escape` | 取消並離開複製模式。 |

滑鼠拖曳選取後放開，也會觸發複製並離開複製模式。

在一般模式下，使用 **Prefix → `P`** 或 **Prefix → `]`** 貼上 tmux 緩衝區。這兩個快捷鍵會在目標程式啟用 bracketed paste 時採用該模式。`Prefix → p` 保留為切換至上一個視窗。

> tmux 緩衝區與系統剪貼簿是不同的來源。上述貼上快捷鍵使用 tmux 緩衝區；若要貼上其他應用程式複製的文字，請使用終端機的系統貼上功能。

## SSH 與剪貼簿

設定啟用 `set-clipboard on`，並為外層終端機是 tmux 的情境宣告剪貼簿與色彩能力，支援巢狀 tmux 的使用方式。

- **本機 macOS**：複製時額外透過 `pbcopy` 寫入系統剪貼簿。
- **遠端 SSH**：透過 OSC 52 將文字傳回本機終端機，由終端機寫入本機剪貼簿；終端機必須支援並允許這項操作。
- **狀態列位置**：載入設定時若偵測到 `SSH_CONNECTION`，會將該 tmux server 的狀態列設為頂端。

若在 SSH 中使用 tmux，請在遠端主機也安裝這份設定，並確認遠端具備 `tmux-256color` 的 terminfo 定義。

## 選用外掛

基本設定不依賴外掛。若要使用工作階段保存功能，可安裝 [TPM](https://github.com/tmux-plugins/tpm)：

```sh
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
tmux source-file ~/.tmux.conf
```

在 tmux 內按 **Prefix → `I`**（大寫 i）安裝設定中宣告的 [tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect)。

設定檔只有在 `~/.tmux/plugins/tpm/tpm` 存在且可執行時才會載入 TPM。工作階段保存與還原的操作及限制，請參閱外掛本身的說明；本專案未設定自動定期保存。

## 檔案結構與自訂

```text
tmuxconf/
├── README.md   # 安裝與使用說明
├── tmux.conf   # 按鍵、終端機能力、剪貼簿與外掛設定
└── tmuxline    # 狀態列主題
```

修改 [`tmux.conf`](tmux.conf) 可調整前綴鍵、快捷鍵、窗格邊框及外掛；修改 [`tmuxline`](tmuxline) 可調整狀態列色彩與內容。

狀態列顯示工作階段名稱、視窗編號與名稱、日期時間，以及主機名稱。主題最初由 tmuxline.vim 產生，使用時不需要安裝 Vim 或 tmuxline.vim。若未建立 `~/.tmuxline`，主設定會略過主題載入。

主題在 `tmux.conf` 開頭載入，因此主設定後面的窗格邊框色彩會覆寫主題中的對應值。

## 更新

```sh
git -C ~/tmuxconf pull --ff-only
tmux source-file ~/.tmux.conf
```

若有自行修改設定，更新前請先提交或備份變更。

## 疑難排解

| 問題 | 檢查方式 |
| --- | --- |
| 載入時顯示 `invalid option` 或 `unknown command` | 執行 `tmux -V` 確認版本。較舊版本可能不支援部分設定，請更新 tmux 或移除不支援的選項。 |
| 狀態列出現方框或缺字 | 將終端機字型改為包含 Powerline 字元的字型。 |
| 遠端複製無法寫入本機剪貼簿 | 確認本機終端機支援且允許 OSC 52；巢狀 tmux 的各層也需正確設定剪貼簿傳遞。 |
| `Alt+h/j/k/l` 沒有切換窗格 | 確認終端機將 Alt／Option 當作 Meta 傳送；iTerm2 可設定為 `Esc+`。 |
| 顯示 `unknown terminal: tmux-256color` | 在相關主機安裝包含 `tmux-256color` 的 terminfo，或依環境調整 `default-terminal`。 |
| 外掛未載入 | 確認 TPM 安裝於 `~/.tmux/plugins/tpm`，重新載入設定後按 Prefix → `I`。 |

## 作者

Victor Fu · [GitHub](https://github.com/victorfu) · [supergothere@gmail.com](mailto:supergothere@gmail.com)
