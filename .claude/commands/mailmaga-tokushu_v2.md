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
- 特集ページの導入文（タイトル直下にある、特集の趣旨を説明するリード文の全文。メルマガのリード文の要約元になるため必ず取得する）
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

#### 4-1. フックの棚卸し（件名を書く前に必ず行う）

Step 1で収集した求人情報から、「読者が思わず反応する具体的な事実」をすべて書き出す：

- **数字**：月給◯万円、時給◯円、年間休日◯日、残業月◯時間、◯時退社、週◯日勤務、賞与◯ヶ月分
- **条件**：未経験OK、資格不要、土日休み、在宅あり、車通勤OK、面接1回、履歴書不要
- **地名**：市区町村レベルの地名（「自分の生活圏だ」と思わせる。「千葉県内」より「船橋」「柏」）
- **意外性**：常識とのギャップがある事実（「工場なのに冷暖房完備」「事務なのに服装自由」など）
- **キャッチフレーズ**：求人ページの引用文で心に引っかかるもの

この中から**最も強いフック上位3〜5個**を選び、それを軸に件名を作る。テーマ名をそのまま件名にしない。

#### 4-2. 件名の共通ルール（全パターン必須）

- **冒頭15文字に一番強いフックを置く**（スマホの受信箱では冒頭しか表示されない。「千葉市内の求人のご案内…」のような前置きで冒頭を消費しない）
- **抽象語・あいまい語は禁止**：「魅力的な求人」「人気のお仕事」「ご案内します」「ご紹介します」「〜特集」「おすすめ」「厳選」など、どのメルマガにも書ける言葉は使わない
- **実在する求人の事実だけを使う**（誇張・創作はしない。本文を読めば必ず回収できること）
- **誰に向けた求人かが一読で伝わる**こと（職種・働き方・エリアのいずれかが具体的にわかる）
- 20〜32文字程度
- 9本はすべて切り口（使うフック）を変える

#### 4-3. 3パターン×3本、計9本を作成

##### 【数字インパクトパターン】（3本）
- 棚卸しした中で最も強い数字を**冒頭に**置き、地名や職種で着地させる
- 数字は「読者の生活が変わるレベルで具体的」なものを選ぶ
- 例：「月26万・残業なし。千葉駅徒歩5分の事務です」「年間休日125日、未経験から始める検査の仕事」

##### 【共感・自分ごとパターン】（3本）
- 読者のいまの状況・本音を一言で言い当て、その解決の入り口を見せる
- 「〜な方へ」「〜、ありませんか」「〜したい人向け」など語りかけ調
- 悩みだけで終わらせず、**答えが本文にあることを匂わせる**
- 例：「17時に帰れる仕事、船橋にありました」「『家から近い』を最優先にした転職、アリです」

##### 【意外性・好奇心パターン】（3本）
- 「え、どういうこと？」と続きが気になる引きを作る。求人の意外な事実やキャッチフレーズを活かす
- 常識とのギャップ、疑問形、あえて言い切らない余白などを使う
- 釣りタイトルにはしない（本文で必ず回収できる事実ベース）
- 例：「倉庫なのに、空調完備。その会社の求人です」「面接1回・履歴書不要。柏のこの会社の理由」

#### 4-4. セルフチェック（提示前に必ず実施）

9本それぞれについて以下を確認し、**1つでも×があれば作り直してから**提示する：

- [ ] 具体的な事実（数字・地名・条件のいずれか）が入っているか
- [ ] 冒頭15文字だけを読んでも興味を引くか
- [ ] 誰向けの求人かが伝わるか
- [ ] 開かないと得られない情報が予告されているか（読んだだけで完結する件名はNG）
- [ ] 他社のメルマガにコピペしても成立してしまう汎用文になっていないか

#### 4-5. 提示フォーマット

```
📧 メルマガ件名案（9本）

【数字インパクトパターン】
① （案1）
② （案2）
③ （案3）

【共感・自分ごとパターン】
④ （案4）
⑤ （案5）
⑥ （案6）

【意外性・好奇心パターン】
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

1. **宛名・リード文**（以下のフォーマットに従う）
   - `___name___ 様`（15px）
   - 「こんにちは。ちばキャリ編集部の花見川です。」
   - 「「{特集タイトル}」の求人をお届けします。{特徴の要約}です。」
     - {特集タイトル}：Step 1で収集したテーマ名（特集ページのメインタイトル）をカギ括弧付きでそのまま使う
     - {特徴の要約}：Step 1で収集した**特集ページの導入文を要約**して入れる（1〜2文）
       - 職種名の列挙ではなく、導入文が語っている「誰に向けた特集か」「どんな切り口・背景か」を要約する
       - 求職者が「自分のことだ」と興味を持つ表現にまとめる（導入文にある問いかけや背景事情を活かす）
       - 販促ワードは使わない
   - 「気になる求人を、リンクからご確認ください！」

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
