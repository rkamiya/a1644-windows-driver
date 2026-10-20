# A1644 Windows Driver

[English](README.md) | 日本語

Apple Magic Keyboard A1644 を Windows 11 で使いやすくするための UMDF ドライバーです。このリポジトリではビルド済みドライバーを配布します。ソースコードは公開していません。

接続方法に応じて、次のどちらかのドライバーを使います。

| ドライバー | 接続方法 | 配布ファイル |
|---|---|---|
| **a1644-lightning-umdf** | Lightning ケーブルによる USB 接続 | `a1644-lightning-umdf-v<バージョン>-win-x64.zip` |
| **a1644-bluetooth-umdf** | Bluetooth 接続 | `a1644-bluetooth-umdf-v<バージョン>-win-x64.zip` |

どちらのドライバーも、キー変換を Windows のユーザーモードドライバー（UMDF）内で行います。専用アプリを常駐させる必要はありません。

## 機能

### キー割り当て

| キー | 動作 |
|---|---|
| Eject | Delete（前方削除） |
| Caps Lock | 左 Ctrl |
| 左 Control | Caps Lock |
| 左／右 Option | 左／右 Windows |
| 左／右 Command | 左／右 Alt |
| 英数 | IME を OFF |
| かな | IME を ON |
| バックスラッシュ・円記号キー | 日本語配列に従って入力 |
| F1〜F12 | 通常のファンクションキー |

### Fn キーとの組み合わせ

| キー | 動作 |
|---|---|
| Fn＋F1 | 画面を暗くする |
| Fn＋F2 | 画面を明るくする |
| Fn＋F3 | タスクビュー |
| Fn＋F4 | クイック設定（Win＋A） |
| Fn＋F5／F6 | 動作なし |
| Fn＋F7 | 前の曲 |
| Fn＋F8 | 再生／一時停止 |
| Fn＋F9 | 次の曲 |
| Fn＋F10 | ミュート |
| Fn＋F11 | 音量を下げる |
| Fn＋F12 | 音量を上げる |

Fn の状態は、F キーを押した時点で決まります。たとえば Fn＋F11 を押したまま Fn だけを離しても、F11 には切り替わりません。

画面の明るさを調整するには、Windows 標準の輝度制御に対応したディスプレイが必要です。また、バックスラッシュはアプリやフォントによって円記号に見えることがあります。

## 動作環境

- Windows 11（x64）。ARM64 版と 32 ビット版には対応していません。
- Apple Magic Keyboard A1644
  - Lightning 版：データ通信に対応した Lightning ケーブルによる USB 接続
  - Bluetooth 版：Bluetooth 接続
- インストール・アンインストールを行うための管理者権限
- 日本語入力を使う場合は、日本語キーボード配列と日本語 IME
- インストール時に必要なツール（配布ファイルには含まれていません）
  - [Windows SDK](https://developer.microsoft.com/windows/downloads/windows-sdk/) の SignTool
  - [WDK](https://learn.microsoft.com/windows-hardware/drivers/download-the-wdk) の Inf2Cat

## 署名について

配布するドライバーには、Microsoft の署名（WHQL 認定や構成証明署名）が付いていません。

インストーラー（`install.bat`）は、インストールする PC で専用の自己署名証明書を作成し、ドライバーに署名してから登録します。そのため、インストール時には SignTool と Inf2Cat が必要です。インストール後の通常使用には必要ありません。

- メモリ整合性、Secure Boot、ドライバー署名の強制を無効にする必要はありません。
- インストーラーは、これらの設定、Windows 全体のキー割り当て、PowerShell の永続的な実行ポリシーを変更しません。
- 証明書は、インストールした Windows アカウントと PC に対して作成されます。アンインストールすると削除されます。

| ドライバー | 証明書の発行先・発行者 |
|---|---|
| Lightning 版 | `A1644 Lightning UMDF Local` |
| Bluetooth 版 | `A1644 Bluetooth UMDF Local` |

| 管理画面 | 登録先 | 用途 |
|---|---|---|
| certlm.msc | 信頼されたルート証明機関 → 証明書 | ローカル署名の信頼 |
| certlm.msc | 信頼された発行元 → 証明書 | ドライバー発行元の信頼 |
| certmgr.msc | 個人 → 証明書 | 署名用証明書・秘密鍵 |

## インストール

### 事前準備

1. 管理者権限を許可できる Windows アカウントでサインインします。
2. Windows SDK の SignTool と WDK の Inf2Cat をインストールします。
3. A1644 を PC に接続し、通常どおり文字を入力できることを確認します。
   - Lightning 版：A1644 の電源を ON にし、データ通信に対応した Lightning ケーブルで接続します。
   - Bluetooth 版：Lightning ケーブルを抜き、Bluetooth で接続します。
4. 作業中も操作できるように、別のキーボードを接続しておきます。

### 手順

1. [Releases](https://github.com/rkamiya/a1644-windows-driver/releases) から、接続方法に合った `…-win-x64.zip` をダウンロードします。「Source code」ではなく、リリースに添付された ZIP を選んでください。
2. （任意）同じリリースに添付された `.zip.sha256` で、ダウンロードしたファイルを検証します。
   ```powershell
   (Get-FileHash .\a1644-lightning-umdf-v0.4.0-win-x64.zip -Algorithm SHA256).Hash.ToLower()
   ```
3. ZIP を右クリックして「すべて展開」を選び、アンインストールまで保管できるローカルフォルダーに展開します。ZIP の中から直接実行しないでください。
4. 展開したフォルダーの **install.bat** をダブルクリックします。
5. ユーザーアカウント制御（UAC）で「はい」を選び、画面の案内に従います。別のアカウントの資格情報で昇格せず、現在の Windows アカウントのまま進めてください。
6. 完了画面に `Installed.` と表示されたら、A1644 で文字入力とキー割り当てを確認します。

SignTool と Inf2Cat は標準のインストール先から自動で検出します。標準以外の場所にインストールした場合は、環境変数 `A1644_SIGNTOOL` と `A1644_INF2CAT` に、それぞれの EXE の絶対パスを指定してください。

インストールの対象は A1644 の入力インターフェイスだけです。Lightning 版は USB の MI_01 だけを、Bluetooth 版は Bluetooth HID のキーボード入力だけを変更します。ほかの物理キーボードや、もう一方の接続方法用のドライバーには影響しません。

> [!IMPORTANT]
> **アンインストールが終わるまで、展開したフォルダーを削除しないでください。** フォルダー内の `runtime` には、元のドライバーの復元と専用証明書の削除に使う記録が保存されています。この記録は PC と Windows アカウントごとに作成されます。ほかの PC にコピーして使わないでください。

## アンインストール

1. 別のキーボードを接続します。A1644 も、インストール時と同じ方法で接続しておきます。
2. インストール時と同じ Windows アカウントで、保管しておいたフォルダーの **uninstall.bat** をダブルクリックします。
3. UAC で「はい」を選び、処理が終わるまで待ちます。
4. 完了画面に `Restored:` と表示されたことを確認します。
5. 必要に応じて A1644 を接続し直し（Lightning 版はケーブルを抜き差し、Bluetooth 版は電源を OFF→ON）、標準のキー割り当てで入力できることを確認します。

アンインストーラーは、A1644 の入力を Windows 標準のドライバーに戻します（Lightning 版は `HidUsb / input.inf`、Bluetooth 版は `kbdhid / keyboard.inf`）。そのうえで専用のドライバーパッケージを削除し、復元を確認してから専用証明書と秘密鍵も削除します。アンインストールに SignTool と Inf2Cat は必要ありません。

再起動を求められた場合や削除に失敗した場合は、展開したフォルダーと `runtime` を残したまま、案内に従って再起動または再接続し、**uninstall.bat をもう一度実行**してください。元のドライバーへの復元が確認できるまで、専用証明書は削除されません。

アンインストール後も、操作ログと復元用の記録はフォルダーに残ります。完了を確認したら、フォルダーを削除してかまいません。

## 状態の確認

**inspect.bat** を実行すると、ドライバーと専用証明書の登録状態を確認できます。不具合を報告するときは、この出力を添付してください。

## 既知の制限

- v0.4.0 の時点では、install.bat と uninstall.bat の実機での実行、追加機能の実機入力、日本語入力、再接続、長時間使用の確認がまだ完了していません。
- Microsoft の署名がないため、インストール時に SDK と WDK のツールが必要です。

## 不具合の報告

[Issues](https://github.com/rkamiya/a1644-windows-driver/issues) から報告してください。ソースコードは非公開のため、プルリクエストは受け付けていません。

## ライセンス

[LICENSE](LICENSE) を参照してください。

本ソフトウェアは Apple Inc. とは関係ありません。Apple、Magic Keyboard、Lightning は Apple Inc. の商標です。
