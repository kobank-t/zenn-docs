---
title: "AWS CloudShell で Amazon Q CLI を起動できず…"
emoji: "💣"
type: "tech"
topics: ["aws", "amazonq", "cloudshell"]
published: true
---

## はじめに

Amazon Q Developer を使っていますか？
普段はローカル環境でメインに利用していますが、他のメンバーにも気軽に試してほしいと思い、AWS CloudShell での利用を試みました。
しかし、現時点（2025/5/23）では一時的に無効化されているとのことなので、記録として残しておきます。

## どんなエラーか？

CloudShell には Amazon Q がインストールされていますが、`q`コマンドを実行するとエラーが発生します。

```shell
$ which q
/usr/local/bin/q

$ q --version
Q CLI integration is temporarily disabled. For continued access to Q Chat, please use the integrated version in your AWS Console.

$ q
Q CLI integration is temporarily disabled. For continued access to Q Chat, please use the integrated version in your AWS Console.
```

AWS の公式ドキュメントによると、AWS CloudShell における Amazon Q のチャット機能は、内部的な問題のため現在一時的に無効化されている状況とのことでした。

https://docs.aws.amazon.com/cloudshell/latest/userguide/q-cli-features-in-cloudshell.html

![](/images/20250523-amazonq-with-cloudshell/2025-05-23-09-50-30.png)

> AWS CloudShell has temporarily disabled Amazon Q chat functionality due to an internal issue. We're actively investigating and will restore this functionality as soon as possible. In the meantime, you can continue using Q chat in the AWS Management Console.
>
> (DeepL 訳)
> AWS CloudShell は内部的な問題のため、一時的に Amazon Q のチャット機能を無効にしています。現在積極的に調査しており、できるだけ早くこの機能を復元する予定です。それまでの間、AWS Management Console で Q チャットを使用し続けることができます。

:::message
なお、日本語サイトでは、この注意喚起は表示されていないので注意してください。
![](/images/20250523-amazonq-with-cloudshell/2025-05-23-09-51-16.png)
:::

代替案としては、以下の 2 点が挙げられます。

-   AWS マネジメントコンソール内での Amazon Q チャット機能の直接利用（ただし英語のみ）
-   ローカル環境に Amazon Q Developer CLI をインストールして利用

## さいごに

公式ドキュメントは英語サイトも確認するようにしましょう。（自分への戒め）
