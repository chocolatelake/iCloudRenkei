# 🛡️ Remote PM System: Streamlit (VPN) Edition

## 1. システム構成図
スマホ (Tailscale ON) ➡️ VPNトンネル ➡️ Mac mini (Streamlit) ➡️ テキストファイル ➡️ Keyboard Maestro ➡️ Gemini & VS Code

この構成の最大のメリットは、**「通知が来ない」**ことです。あなたの好きなタイミングでダッシュボードを見に行き、指示を出し、結果を確認できます。

---

## 2. 準備: Mac miniでの環境構築

まず、Mac miniのデスクトップに作業用の「基地」を作ります。

### Step 1: フォルダとライブラリ
ターミナルを開き、以下のコマンドを一行ずつ実行してください。

```bash
# 1. デスクトップに専用フォルダ作成
mkdir ~/Desktop/Gemini_Cockpit
cd ~/Desktop/Gemini_Cockpit

# 2. 必要なPythonライブラリのインストール
pip install streamlit watchdog
```

### Step 2: コックピットアプリ (`app.py`) の作成
`~/Desktop/Gemini_Cockpit` フォルダの中に、以下のコードを `app.py` という名前で保存してください。
これがあなたのスマホに表示される操作画面になります。

```python
import streamlit as st
import os
import time
from datetime import datetime

# ================= 設定エリア =================
# 作業フォルダ（このファイルの場所）
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
INSTRUCTION_FILE = os.path.join(BASE_DIR, 'instruction.txt')
SCREENSHOT_FILE = os.path.join(BASE_DIR, 'report.png')
# ============================================

# ページ設定（スマホで見やすくする）
st.set_page_config(page_title="Lazy Dev PM", layout="centered")

st.title("👨‍💻 Remote PM Console")

# --- 1. ステータス表示 ---
if os.path.exists(SCREENSHOT_FILE):
    last_mod = datetime.fromtimestamp(os.path.getmtime(SCREENSHOT_FILE))
    st.caption(f"最終更新: {last_mod.strftime('%H:%M:%S')}")
else:
    st.caption("No screenshot yet.")

# --- 2. 画面リロードボタン ---
if st.button("🔄 画面を更新 (結果確認)"):
    st.rerun()

# --- 3. スクリーンショット表示エリア ---
# キャッシュ回避のために時刻パラメータを付与して読み込む
if os.path.exists(SCREENSHOT_FILE):
    st.image(SCREENSHOT_FILE, caption="Current Screen", use_column_width=True)
else:
    st.info("ここにビルド結果のスクショが表示されます")

st.divider()

# --- 4. 指示出しエリア ---
st.subheader("📢 指示を送る")

with st.form("instruction_form", clear_on_submit=True):
    instruction = st.text_area("Geminiへの指示:", height=100, placeholder="例: ヘッダーを赤くして、文字を大きく")
    
    # フォームの送信ボタン
    submitted = st.form_submit_button("指示を実行")
    
    if submitted and instruction:
        # 指示ファイルに書き込み
        with open(INSTRUCTION_FILE, 'w', encoding='utf-8') as f:
            f.write(instruction)
        st.toast(f"指示を送信しました！ KMが起動します...", icon="🚀")
        time.sleep(1)
        st.rerun()

# --- 5. コマンドボタンエリア ---
st.subheader("⚙️ コマンド")
col1, col2 = st.columns(2)

with col1:
    # Pushボタン: 特殊なキーワードをファイルに書き込む
    if st.button("🚀 Git Push"):
        with open(INSTRUCTION_FILE, 'w', encoding='utf-8') as f:
            f.write("CMD_PUSH")
        st.success("Push指示を送信しました")

with col2:
    # 停止ボタン: 空文字を送って誤動作防止など（運用に合わせて）
    if st.button("🛑 Clear"):
        with open(INSTRUCTION_FILE, 'w', encoding='utf-8') as f:
            f.write("")
        st.info("指示をクリアしました")
```

---

## 3. Keyboard Maestro (KM) の設定

Streamlitが書き換えた `instruction.txt` をトリガーにして、Macを動かす設定です。
ここが一番のキモです。

### マクロ名: `Gemini Streamlit Runner`

#### **Trigger (トリガー):**
* **The folder** `~/Desktop/Gemini_Cockpit` **adds an item** (or changes)
    * *注意: Folder Triggerで `instruction.txt` の変更を検知するように設定してください。*

#### **Actions (アクションの流れ):**

1.  **Read File:** `instruction.txt` を読み込み、変数 `Task` に格納。
2.  **If All Conditions Met:** (変数が空じゃないかチェック)
    * Condition: Variable `Task` is not empty.
    * **Execute Actions:**
        3.  **If All Conditions Met:** (Pushコマンドかどうかの判定)
            * Condition: Variable `Task` is `CMD_PUSH`
            * **Then (Pushの場合):**
                * **Execute Shell Script:** `cd /your/project/path && git push origin gemini-wip`
            * **Else (通常の指示の場合):**
                * **Activate Application:** Google Chrome (Gemini)
                * **Insert Text:** `%Variable%Task%` をペースト & Enter。
                * **Pause:** 画像認識（コピーアイコンが出るまで待機）。
                * **Click at Found Image:** コピーアイコンをクリック。
                * **Activate Application:** VS Code
                * **Keystroke:** `Cmd+A` -> `Cmd+V` -> `Cmd+S`
                * **Execute Shell Script:** `cd /your/project/path && git commit -am "Auto" && npm run build`
        4.  **Screen Capture:**
            * Capture Main Screen (or Window)
            * Save to: `~/Desktop/Gemini_Cockpit/report.png`
            * *※重要: 常に同じファイル名で上書き保存してください。Streamlitがそれを読み込みます。*

---

## 4. 運用開始手順

### 外出前の儀式（Mac mini）
1.  **Streamlit起動:**
    ターミナルで `Gemini_Cockpit` フォルダに移動し、以下を実行。
    ```bash
    streamlit run app.py
    ```
    * `Network URL: http://100.x.y.z:8501` のようなURLが表示されます。このIP（100から始まるやつ）がTailscaleのIPです。

2.  **アプリ配置:**
    Chrome（Gemini）とVS Codeを開いておく。

### 外出先からのアクセス
1.  スマホの **Tailscaleアプリ** を開き、**Active** にする（左上のVPNアイコンが出る状態）。
2.  スマホのブラウザ（Safari/Chrome）で `http://[MacのTailscale-IP]:8501` にアクセス。
3.  **「Remote PM Console」** が表示されたら成功！

---

## 💡 ヒント: 画像認識のコツ
Keyboard Maestroで「Geminiのコピーボタン」を画像認識させる際、Retinaディスプレイだと解像度の問題で認識しないことがあります。
KMの画像マッチング設定にある **「Fuzziness（あいまいさ）」** バーを少し右（スライダーを動かす）にして、厳密すぎない設定にすると安定します。
