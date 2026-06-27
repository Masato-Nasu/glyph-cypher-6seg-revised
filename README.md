# GLYPH CYPHER 6SEG v0.1.2

6セグメント風のglyph画像を作る静的PWAです。現在のユーザー向けモードは2つだけです。

## Modes

- **ENCRYPT MODE**: 日本語を含むUnicode本文をAES-GCMで暗号化します。PNG読み取りと復号には作成時と同じパスワードが必要です。
- **GLYPH ALPHABET MODE**: 英数字・基本記号を対応表に基づいて描画します。暗号化せず、パスワードも不要です。Webページで使われる基本ASCII記号は `SYM NEXT` 拡張表記で表現します。

GLYPH ALPHABET MODEは対応表で人間が解読できます。64種類の6SEG制限を超えないために、`SYM NEXT` 制御グリフを使います。`SYM NEXT` の直後のグリフは通常文字ではなく拡張記号として読みます。ENCRYPT MODEの日本語入力は暗号化データであり、パスワード必須です。

## 修正内容

- ENCRYPT MODE / GLYPH ALPHABET MODEの2本立てに整理
- GLYPH ALPHABET MODEの `alphabet-v2` 対応表を実装
- `CAP NEXT`, `CAPS ON`, `LOWER ON` による大文字制御を維持
- `SYM NEXT` による拡張記号表記を追加
- Web拡張記号（<, >, [, ], {, }, backslash, ^, backtick）を `SYM NEXT + A-I` で表現
- `%` `#` `)` `~` を含む基本記号は直接1グリフで表現
- PNG保存データにモード別メタデータを埋め込み、GLYPH ALPHABETをパスワードなしで復元可能に変更
- 四隅マーカーのsafe areaとフッターを本文glyphの描画禁止領域として扱うレイアウトに修正
- Manuscript themeを維持し、黒背景・黄緑glyphのTerminal themeを追加
- UIとREADMEに「英数字版は対応表で解読可能 / 日本語版は暗号化・パスワード必須」の違いを明記
- アプリ内の「換字表」からManuscript版PNG、Terminal版PNG、CSVをダウンロード可能に変更

## 使い方

1. 「作る」タブでモードを選びます。
2. ENCRYPT MODEでは本文とパスワードを入力します。
3. GLYPH ALPHABET MODEでは英数字・基本記号・Web用基本ASCII記号を入力できます。
4. Themeを選び、「glyph画像を生成」を押します。
5. 「PNG保存」で画像を保存します。
6. 「読む」タブでPNGを選び、「画像から復号」を押します。
7. 換字表が必要な場合は「生成画像」欄の「換字表」からPNGまたはCSVを保存します。

## 技術メモ

- 暗号: AES-GCM 256bit
- 鍵導出: PBKDF2-SHA-256、160000回
- 画像内形式: `GLYP` ヘッダー + payload長 + payload + CRC32
- ENCRYPT MODE payload: 既存互換の `FSC1` 暗号payload
- GLYPH ALPHABET payload: `glyph_alphabet` JSON metadata
- 新しいGLYPH ALPHABET PNG metadataの `tableVersion` は `alphabet-v2` です。
- 拡張記号表: SYM NEXT + A = &lt;, B = &gt;, C = [, D = ], E = {, F = }, G = backslash, H = ^, I = backtick です。
- 保存PNGには private ancillary chunk `gcYP` として `GLYP` payload を埋め込みます
- 外部依存なし。ローカルHTTPサーバーでそのまま実行できます。

## ローカル実行例

```powershell
python -m http.server 8787
```
