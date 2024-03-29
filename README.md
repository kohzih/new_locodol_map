# 新ロコドルマップ（仮）

## サービス概要

全国のローカルアイドル（ご当地アイドル）の一覧を、  
各都道府県毎に、  
半自動で作成し続けるサービス。  

## メインのターゲットユーザー

- ローカルアイドルが好きな人
- アイドルは好きだが、ローカルアイドルには今まで興味がなかった人
- 近所で推しを見つけたい人
- 地元のアイドルとコラボレーションを考えている、企業や自治体関係者

## ユーザーが抱える課題

あまちゃんがブームになっていた時期(2013～2014)は、全国のローカルアイドルを網羅したようなサイト・アプリが複数存在したが、現在は、ほとんど更新が停止されている。  
また、存在しているサイトも、人気のあるローカルアイドルしか網羅されておらず、全国でどれだけのローカルアイドルが活動しているかを知るには、個別に検索する必要がある。  

## 解決方法

ローカルアイドルを一覧表示するWEBサイトを、管理者による手動更新とすると、現状のように管理者のモチベーションが下がった(ブームが去った)後、放置される可能性が高い。  
現在、ほとんどローカルアイドルは、X(旧Twitter)のアカウントを作成しているため、  
~~XのAPIを利用し、バッチ処理で一覧を作成し、定期的に自動更新する、~~  
→APIの有料化に伴い、当面の間、手作業で定期的に収集し更新する、  
WEBサイトを作成する。

## 実装予定の機能

- アカウント未登録ユーザー
  - 各都道府県毎のローカルアイドルの一覧の参照・検索
    - 表示内容
      - 名前
      - 公式サイトのURL
      - Xを含むSNS
      - Xのフォロワー数
      - お気に入り登録者数
  - アカウントの登録

- アカウント登録ユーザー
  - お気に入り登録機能
    - 登録すると以下のメール通知機能が使用できる(都度通知すると煩わしいため、それぞれ１日１回とする)
      - 公演・イベントの開催日の事前通知
      - 公式サイトの更新時の通知
      - X、Instagramの更新内容の通知
    - また、上記の内容はサイト内の各アイドルの詳細ページでも確認可能
  - 一覧に表示されていないアイドルの登録申請機能
    - 申請があった場合、管理者によるチェックを行う
  - 一覧に表示されているアイドルへの編集機能
    - 公式サイトURLの登録
    - SNSアカウント(X以外)の登録
    - Youtubeに投稿されているライブ映像等の動画埋め込み
    - 自身のコメント登録・編集・削除
  - 一覧登録されているローカルアイドルの情報の外部連携機能の使用
    - アイドルの情報を取得できるWeb APIの使用
    - 画面上からのCSV出力機能
  - 自身のアカウントの編集・削除

- 管理者ユーザー
  - アイドルの追加・編集・削除
  - ユーザアカウントの作成・編集・削除
  - バッチ処理で自動作成された一覧の編集・公開機能
  - ユーザから登録申請のあったアイドルの正式登録機能

- 一覧作成処理(バッチ処理。Rakeタスクとして作成する)
  - ローカルアイドルリスト作成機能
    - ~~Xのユーザーアカウントを検索するAPI(GET users/search)を使用し、以下のキーワードを含むアカウントを検索する~~
    - →X上で、手作業で、以下のキーワードを含むアカウントを検索する
      - 都道府県名、公式、(アイドル or ユニット or ご当地アイドル)
    - 取得したアカウントのうち、`ローカルアイドルじゃないリスト`と、`ローカルアイドルリスト`存在するアカウントを除いて、`新規登録アイドル候補リスト`を作成する
    - このタスクは週ごとに行う
    - 管理者による手作業(毎週どこかで・・・)
      - `新規登録アイドル候補リスト`を最終確認し、追加有無の判定を行う
        - 追加する　　→`ローカルアイドルリスト`へ追加
        - 追加しない　→`ローカルアイドルじゃないリスト`へ追加
      - おそらく、初回の振り分け作業が一番大変ではある。。
  - アカウント登録ユーザー向けの通知内容の収集
    - 公演・イベントの開催日の自動収集(Googleカレンダーが連携されている場合のみとするか・・・)
      - 可能であれば、Xの投稿から自動収集したい
    - 公式サイトの更新有無
    - X、Instagramの投稿内容の収集
    - これらのタスクは、登録アイドルの数が増えると時間がかかると想定されるため、実行頻度は要検証

### 画面遷移図

[新ロコドルマップ(仮)](https://www.figma.com/file/wkNmtAAE9OChRcU5etwDHf/%E6%96%B0%E3%83%AD%E3%82%B3%E3%83%89%E3%83%AB%E3%83%9E%E3%83%83%E3%83%97(%E4%BB%AE)?t=SmvCbb6n9hPmcchK-1)

### ER図

![ER図](https://i.gyazo.com/ac3908fd7bfd11c65a7086b7438299c2.jpg)

#### Idols

ローカルアイドルの一覧。

- name
- prefecture_id
- register_type
  - 0: 自動登録
  - 1: 手動登録
- user_id
  - 自動登録の場合は管理者のuser_idを設定
  - ユーザー申請による手動登録の場合は申請者のuser_idを設定
- activity_status
  - 0: 活動中
  - 1: 解散または活動休止(一覧に表示しない)
- sns_account_id

#### Idol_candidates

Xから~~自動~~収集、または、ユーザから申請のあったローカルアイドル情報を格納する。

- name
- prefecture_id
- request_type
  - 0: バッチ申請
  - 1: ユーザー申請
- user_id
  - バッチ申請の場合は管理者のuser_idが設定
  - ユーザー申請の場合は申請者のuser_idを設定
- account_status
  - 0: 判定待ち
  - 1: ローカルアイドル
  - 2: アイドルではない
- sns_account_id

#### Users

会員情報(管理者も含む)。

- email
- name
- prefecture_id
- role
  - 0: 一般ユーザ
  - 10: 管理者ユーザ
- notice_website(メール通知)
  - 0: なし
  - 1: する
- notice_X
- notice_instagram
- notice_event
- last_login
  - 最終ログイン日時
- crypted_password
- salt
- reset_password_token
- reset_password_token_expires_at
- reset_password_email_sent_at
- access_count_to_reset_password_page

#### Prefectures

都道府県名。

- name

#### Comments

各アイドルに追加されたユーザからのコメント。

- idol_id
- user_id
- body

#### Bookmarks

ユーザがお気に入りに追加したアイドルのリスト。

- user_id
- idol_id

#### Embeds

ユーザが各アイドルのページに埋め込んだSNSの投稿。

- idol_id
- user_id
- embed_type
  - 0: Youtube
  - 1: X
  - 2: Instagram
- embed_link

#### Event_infos

公式サイト・SNSの更新や、公演情報のメール通知用。各ユーザに通知後、削除する。

- idol_id
- notice_type
  - 0: website
  - 1: X
  - 2: instagram
  - 3: event
- notice_status
  - 0: 未通知
  - 1: 通知済
- body

#### Sns_accounts

アイドルのSNSアカウント格納用。

- idlable_id
- idlable_type
  - 'Idols'
  - 'Idol_candidates'
- sns_type
  - 0: twitte
  - 1: website
  - 2: instagram
  - 3: youtube
  - 4: facebook
- account
- profile
- avatar
- followers

## なぜこのサービスを作りたいのか？

あまちゃんのブーム以降、ローカルアイドルの活動は興味のない人からすると見えづらくなっている。  
私は、AKB48グループやラブライブシリーズなど、アイドル好きであるが、ローカルアイドルにはあまり興味がない。  

しかし、最近、近所でローカルアイドルのイベントをよく見かけたり、  
昨年は、地元の大学の学園祭で、北陸地区のローカルアイドルを集めたライヴも開催されていた。  

少々興味を持ち調べて見ると、今でも全国ではかなりの数のローカルアイドルが活動している事がわかった。  

が、Googleを使用した検索では、ローカルアイドルの一覧を調べようとしても、更新が停止したアプリ・サイトか、  
全国的に人気のあるローカルアイドルを網羅したサイトしか見つからなかった。  

結局、Xで検索した方が見つけやすかったため、  
どうせ手作業で検索するなら、各都道府県毎に一覧を自動作成したほうが便利だと考えたたため。  

---

・・・と、それっぽい事を書いて見ましたが、正直、自分以外にどこまで需要があるかは考えていません(笑)。  
ただ、この仕組みを使うことで、今後、検索キーワードさえ変更すれば、全国のパン屋さんの一覧等も作れそうな気がするため、  
今後の拡張と、自身のスキルアップのため作成したいと考えました。

## スケジュール

- 企画〜技術調査：1/21〆切
- README〜ER図作成：2/5 〆切
- メイン機能実装：2/6 - 3/25
- β版をRUNTEQ内リリース（MVP）：3/12〆切
- 本番リリース：3月末
