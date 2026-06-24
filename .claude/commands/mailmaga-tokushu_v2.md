# /mailmaga-tokushu_v2：特集メルマガ作成（自動モード・TEXT版）

特集ページURLとIDを指定するだけで、TEXT版ファイルを自動生成します。

## 使い方

```
/mailmaga-tokushu_v2 https://www.chibacari.com/career/advertisement/detail/?id=XXXX
ID: XXXX
```

## 実行手順

引数として渡された情報: $ARGUMENTS

### Step 1: 特集ページから求人情報を収集

$ARGUMENTS のURLをWebFetchで取得し、以下を収集:
- テーマ名（ページのメインタイトル）
- IDは $ARGUMENTS で指定された値をそのまま使用する
- 各求人ごとに：
  - 職種名
  - 会社名
  - キャッチフレーズ（引用文・コメント）
  - 仕事内容
  - 給与
  - 勤務地（都道府県・市区町村・町字まで。丁目・番地は不要）
  - 詳細ページURL（相対パスは `https://www.chibacari.com` を補完）

---

### Step 2: UTMパラメータ設定

| 項目 | 値 |
|---|---|
| utm_source | email |
| utm_medium | newsletter |
| utm_campaign | {ID}b |
| 求人リンク | utm_content=job_{求人ID}（求人ごとに個別ID） |
| コンテンツ記事 | utm_campaign={ID}b_contents / utm_content=contents |

※IDはユーザー指定値をそのまま使用する（「tokushu_」などのプレフィックスは付けない）

---

### Step 3: コンテンツ記事の選定

以下の2URLをWebFetchで順番に取得:

```
https://chibacaricorp.com/mediawp2/wp-json/wp/v2/posts?per_page=100&page=1&_fields=title,link,excerpt,date
https://chibacaricorp.com/mediawp2/wp-json/wp/v2/posts?per_page=100&page=2&_fields=title,link,excerpt,date
```

テーマに最も関連性が高い記事を2件選定し、各記事URLをWebFetchで取得して以下を抽出:
- 記事タイトル（正確なもの）
- 記事概要（冒頭2〜3文、HTMLタグを除いたテキスト、100〜150文字程度）
- 記事URL（APIが返す `chibacaricorp.com/mediawp2/` のURLをそのまま使用）

**選定後、2件の記事タイトルとURLをユーザーに提示し、使用する1件を選んでもらう。**
選定に悩んだ場合は3件に絞ってユーザーに提示し、使用する1件を選んでもらう。
「別の記事を使いたい」場合はURLを指定してもらい、記事情報を再取得する。

---

### Step 4: 件名案の提示

Step 1で収集したテーマ名・職種名・会社名・キャッチフレーズをもとに、3パターン×3本、計9本の件名案を提示。

**各パターンの定義と件名ルール：**

#### 【数字・条件パターン】（3本）
- 具体的な数字（給与・時間・件数・勤続年数など）や条件（未経験OK・週3日など）を前面に出す
- 目を惹く訴求型。プロモーション扱いされる可能性があるが、クリック率が高い傾向
- 例：「未経験でも月23万、千葉市内の求人です」「週4日・扶養内OKの求人が3件あります」

#### 【共感パターン】（3本）
- 求職者が抱える悩みや気持ちに寄り添う語りかけ調
- 「〜で困っている」「〜したい」という内面の声を代弁する
- 例：「定時に帰れる仕事、まだあります」「通勤時間を減らしたい方へ」

#### 【ニュース記事系パターン】（3本）
- 販促ワードを使わず、事実・情報を淡々と伝えるお知らせ調
- プロモーション要素を最小限に抑えた件名
- 例：「千葉市内・医療事務の求人をご案内します」「今月のご紹介：柏市周辺の事務求人」

**共通ルール：**
- 「特集」「おすすめ」「厳選」「今週の〜特集」などの販促ワードは使わない
- 25〜35文字程度
- 9本はすべて切り口を変える

```
📧 メルマガ件名案（9本）

【数字・条件パターン】
① （案1）
② （案2）
③ （案3）

【共感パターン】
④ （案4）
⑤ （案5）
⑥ （案6）

【ニュース記事系パターン】
⑦ （案7）
⑧ （案8）
⑨ （案9）

番号で選んでください。修正したい場合はそのままお伝えください。
```

---

### Step 5: ファイルを生成（TEXT版）

- TEXT版: `C:/Users/chiba/Documents/メルマガ格納/mailmaga_{ID}TEXT.html`

---

## デザイン：TEXT版（テキストリスト型）

### ダークモード対応＋レスポンシブ余白（必須）
```html
<meta name="color-scheme" content="light only">
<meta name="supported-color-schemes" content="light">
<style>
  :root { color-scheme: light only; }
  body { color-scheme: light only !important; background-color: #ffffff !important; }
  @media (prefers-color-scheme: dark) {
    body { background-color: #ffffff !important; }
    table { background-color: inherit !important; }
    td { background-color: inherit !important; color: inherit !important; }
    p, h1, h2, h3, span, a { color: inherit !important; }
  }
  @media screen and (max-width: 600px) {
    .sp-hdr  { padding-left: 24px !important; padding-right: 24px !important; }
    .sp-card { padding-left: 14px !important; padding-right: 14px !important; }
  }
  @media screen and (min-width: 601px) {
    .sp-outer { padding-left: 32px !important; padding-right: 32px !important; }
  }
</style>
```

### CSSカラー
- 番号: #888888
- 職種名: #222222
- 本文: #444444
- 給与・勤務地・条件: #555555
- リンク: #0858a8
- 区切り線: #e5e5e5
- 免責・フッターテキスト: #999999

### 構成

1. **宛名・リード文**
   - `___name___ 様`（15px）
   - 「こんにちは。ちばキャリ編集部の花見川です。」
   - 語りかけ調の挨拶文（販促ワードを使わない。例：「〜な求人を{件数}件見つけました」）
   - 「気になる求人があればリンクからご確認ください。」

2. **求人リスト**（件数分、01〜順番に）
   - 各求人は `border-top: 1px solid #e5e5e5` で区切る
   - 番号（13px、#888888）
   - 職種名（16px、bold、#222222）
   - 仕事内容の要約（#444444）
   - 給与・勤務地・応募条件（14px、#555555）
   - 「求人詳細を見る →」テキストリンク（#0858a8、GA4付きURL）

3. **免責テキスト**（最終求人の後、`border-bottom: 1px solid #e5e5e5`）
   ```html
   <p style="margin:0;font-size:12px;color:#999999;line-height:1.7;">※ご覧いただくタイミングによっては、掲載終了や変更している場合もございます。詳細は求人情報をご覧ください。</p>
   ```

4. **コンテンツパート**（白背景・装飾なし）
   - 導入文（テキスト調）
   - 記事タイトル（bold）
   - 記事概要テキスト
   - 「記事を読む →」テキストリンク（#0858a8、GA4付きURL）
   - 区切り線（`border-top: 1px solid #e5e5e5`）

5. **結び**
   ```html
   <tr>
     <td style="border-top:1px solid #e5e5e5;padding:24px 32px;">
       <p style="margin:0 0 12px;font-size:14px;color:#444444;">（雑談テキスト）</p>
       <p style="margin:0;font-size:14px;color:#444444;">ちばキャリ編集部</p>
     </td>
   </tr>
   ```

6. **フッター**（区切り線後）
   ```html
   <tr>
     <td class="sp-hdr" style="padding:24px 32px 0;border-top:1px solid #e5e5e5;">
       <p style="margin:18px 0 8px;font-size:12px;color:#888888;line-height:1.8;">
         メルマガの停止は、マイページ「登録情報」より<a href="https://www.chibacari.com/career/user/login/" style="color:#888888;text-decoration:underline;">お手続き</a>ください。配信停止までは約1日お時間を頂戴します。<br>
         ご質問は<a href="https://tayori.com/q/chibacari-faq/" style="color:#888888;text-decoration:underline;">こちら</a>からご確認をお願いいたします。
       </p>
       <p style="margin:14px 0 0;font-size:12px;color:#888888;">
         ちばキャリ｜千葉県の求人・転職・就職サイト<br>
         <a href="https://www.chibacari.com/" style="color:#888888;text-decoration:underline;">https://www.chibacari.com/</a>
       </p>
     </td>
   </tr>
   ```

---

### Step 6: 完了報告

- TEXT版の保存先ファイルパス
- 決定したメルマガ件名
- 付与したUTMパラメータの構成
- 採用したコンテンツ記事のタイトルとURL
