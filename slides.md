# Email, Messaging, and Self-Sovereign Identity
## Ryo Kajiwara @ WIDE Meeting, 2021/05/28

----

# 注意事項

- これはどちらかというと意見表明のような性質の発表です
  - 一時期のQiitaでいうところの「ポエム」
  - よって、**プロダクトや開発成果のデモではありません**
  - また、**議論や前提に抜けや穴は余裕で存在するハズです**

----

# スライド(のソース)は以下のURLから見れます

## `https://github.com/sylph01/20210528-wide-email-talk`

----

# <!--fit--> TL;DR

----

# <!--fit--> SMTPを**やめろ**

----

# <!--fit--> **どうやって**やめる？

----

# 遅れましたが自己紹介

![bg right:30% contain](me_.jpg)

- 梶原 龍 といいます
  - Twitter: `@s01`
  - 暗号とかできます
  - ネットワークまるでわからん

----

# もうちょっと真面目に

![bg right:30% contain](ddbook_cover.png)

- やせいのプログラマ
  - 2019/3までの所属はACCESS、2020/10までの所属はレピダム
  - 大学時代はユーザーインターフェースとかやってたらしい
- "Dark Depths of SMTP"(2017)という薄い本を書きました
- W3C, IETFなどでセキュリティ周りの標準化の調査・お手伝いをしていました
  - HTTPS in Local Network CG @ W3C
  - Messaging Layer Security @ IETF
- Internet Society日本支部のOfficerをやっています

----

----

# 第1部: なんでSMTPをやめる？

----

# メッセージングに求める性質

- End-to-End暗号化
  - 派生して、暗号化されたグループメッセージング
- マルチデバイスのアクセス
- アイデンティティ表現の制御
  - 場面によって使うIDを変えたい

----

# Eメールはどこまでできる？

- End-to-End暗号化 -> **S/MIMEを使えば**1対1なら可能
- 暗号化されたグループメッセージング -> **不可能**
- マルチデバイスのアクセス -> POP3なら不可能、IMAPなら可能
  - ただし、IMAPの場合データがサーバーに残ることが前提
- アイデンティティ表現の制御
  - メールアドレスを複数所持すれば可能
  - 受信アドレスを出し分けるのはローカルパート `+` suffixを使えば可能だが関連付けは容易

----

# Q: なんで **LINE/Facebook Messenger/WhatsApp etc.** があるのにメールなんか使ってるの？

----

# Q: なんでLINE/Facebook Messenger/WhatsApp etc.があるのにメールなんか使ってるの？

- SMTPは十分な暗号化や認証を持っていない → **insecure**
  - Eメールは通常End-to-End暗号化を持っていない
  - そもそもSMTPの認証は拡張仕様
    - POP before SMTP, SMTP-AUTH, ...
  - PGPやS/MIMEを使ったところでグループに対する暗号化コミュニケーションはできない
- **Eメールにはspamがある**

----

# A: Eメールでは **事前の信頼関係のない人からも** メッセージを受け取ることができる

----

# でもEメールには **spamがある** じゃん！

----

# Eメールにspamがあるのはプロトコルに埋め込まれた **匿名性** が原因

----

# Eメールの匿名性

「事前の信頼関係のない人からもメッセージを受け取れる」という性質は**電話**にもあてはまるが、Eメールには電話網にあるようなanti-abuse mechanismを持っていない。これはEメールのプロトコルに埋め込まれた**匿名性**が原因である。

- 電話網をabuseした場合逆探知が可能
- Eメールにおいてはidentityのspoofingが容易で、捕まえることが困難

----

# Eメールには **identity layerがない**

----

# まともなidentity layerがないので
# **まともなtrustが構築できない**

----

# なのでspammerはこの **trustの不在を悪用する**

----

# そもそも匿名のEメール、欲しい？

匿名のEメールは高確率でspam。

新たに信頼関係を結びたい場合匿名であることにいいことはそんなにない。

----

# じゃあ全面的にS/MIME、使う？

----

# S/MIMEの問題

- 高い

----

# マイナンバーカード使う？

----

# じゃあ暗号化諦める？

----

# Eメールがdigital identityの核となることの問題

(draft)

- Eメールはinsecure
- Eメールの乗っ取りですべてのidentityの乗っ取りが可能

----

----

# 第2部: セキュアメッセージング

----

- building blocks
  - Signalプロトコル
  - Messaging Layer Security
  - JSON Web Message
- 現在までにうまくセキュアメッセージングを実装しているところ
  - LINE (Letter Sealing)
  - WhatsApp
  - Signal
  - Telegram (MTProto)
  - Keybase
- サービス事業者の「悪堕ち」
  - LINEのデータ管理問題
  - Facebook Messenger
    - E2EEできるけど明示的にonにしないと有効にならない、やるとマルチデバイス対応しない
      - まあこれは鍵交換って難しいよねという問題になるんだけど
- interoperableなプロトコルが必要

----

----

# 第3部: Self-Sovereign Identity

----

- サービス事業者の「悪堕ち」に耐えるには？→Self-Sovereign Identity
- SSIのコンセプト
- identity表現の出しわけをしたい
  - corelationを割けたい
  - pair-wise identifier
- EメールはもともとSelf-Sovereignだった
  - Eメールというプロトコルがいかに中央集権化されていったか
- Principles of User Sovereignty / Fundamental Problems of Distributed Systems @ IIW30


----

# 第4部: どうやってSMTPをやめる？

----

- Better PGPだよね？
  - 知ってる。
  - ただし我々は既にBetter PGPを手にしている
    - LINE, WhatsApp, Keybase(, FB Messenger)っていうんですけど
  - 暗号鍵の管理がしんどいはそのとおり
- グループメッセージングはチャットアプリのインターフェースに寄せて再設計していいのでは？
- 通知にsubscribeする形のmessaging(Notifs)
- DIDComm
  - ちゃんと調べて書く
- Verifiable Credentialを用いたEメール
  - VC modelの説明
- data at restの暗号化: JWM

----

# まとめ

----

- Eメールには暗号化がない、まともなidentity layerがない
- identity layerの構築をEメールプロバイダに任せるとself-sovereignでなくなる
- セキュアメッセージングのinteroperabilityが欲しい
- DIDを使ったidentity layerを作ってそれに乗っかってしまうのがいいのでは？
- a
