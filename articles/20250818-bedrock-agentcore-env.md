---
title: "Amazon Bedrock AgentCore Runtime で環境変数を手軽に設定する"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["aws", "bedrock", "AgentCore"]
published: true
---

## はじめに

こんにちは。最近、個人的に小さな AI エージェントを開発していて、Bedrock AgentCore と仲良くなってきた気がします。
今回は、Bedrock AgentCore Runtime へのデプロイを試す中で、環境変数を扱いたくなったのですが、以下の先人のみなさまの記事に目から鱗になりつつも、より手軽な方法を試してみた記録です。

https://qiita.com/har1101/items/63b80b32892c038e2779

https://qiita.com/moritalous/items/6c822e68404e93d326a4

結論から言うと、`agentcore launch` コマンドの `--env` オプションを使えば、簡単に環境変数を設定してデプロイできることがわかりました。

## 前提

この記事では、`bedrock-agentcore-starter-toolkit` のバージョン `0.1.4` を使用しています。
(執筆時点の最新は `0.1.6` でした)

```shell
% pip index versions bedrock-agentcore-starter-toolkit
bedrock-agentcore-starter-toolkit (0.1.6)
Available versions: 0.1.6, 0.1.5, 0.1.4, 0.1.3, 0.1.2, 0.1.1, 0.1.0, 0.0.1
  INSTALLED: 0.1.4
  LATEST:    0.1.6
```

## 環境変数を設定してみる

まずは、デプロイに使う `agentcore launch` のヘルプを確認しました。

```shell
% agentcore launch --help

 Usage: agentcore launch [OPTIONS]

 Launch Bedrock AgentCore with three deployment modes.

 🚀 DEFAULT (no flags): CodeBuild + cloud runtime (RECOMMENDED)
    - Build ARM64 containers in the cloud with CodeBuild
    - Deploy to Bedrock AgentCore runtime
    - No local Docker required
    - CHANGED: CodeBuild is now the default (previously required --code-build flag)

 💻 --local: Local build + local runtime
    - Build container locally and run locally
    - requires Docker/Finch/Podman
    - For local development and testing

 🔧 --local-build: Local build + cloud runtime
    - Build container locally with Docker
    - Deploy to Bedrock AgentCore runtime
    - requires Docker/Finch/Podman
    - Use when you need custom build control but want cloud deployment

 MIGRATION GUIDE:
 - OLD: agentcore launch --code-build  →  NEW: agentcore launch
 - OLD: agentcore launch --local       →  NEW: agentcore launch --local (unchanged)
 - NEW: agentcore launch --local-build (build locally + deploy to cloud)

╭─ Options ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ --agent                    -a        TEXT  Agent name (use 'agentcore configure list' to see available agents) [default: None]                                   │
│ --local                    -l              Build locally and run container locally - requires Docker/Finch/Podman                                                │
│ --local-build              -lb             Build locally and deploy to cloud runtime - requires Docker/Finch/Podman                                              │
│ --auto-update-on-conflict  -auc            Automatically update existing agent instead of failing with ConflictException                                         │
│ --env                      -env      TEXT  Environment variables for agent (format: KEY=VALUE) [default: None]                                                   │
│ --help                                     Show this message and exit.                                                                                           │
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯

```

ヘルプを眺めると、`--env` オプションを発見！このオプションは、KEY=VALUE の形式で環境変数を設定できるようです。
実際のコードを軽くハックすると、この辺でオプションを解釈してそうです。
https://github.com/aws/bedrock-agentcore-starter-toolkit/blob/c4317323ffe5695d19ff82ebb721c944c144f838/src/bedrock_agentcore_starter_toolkit/cli/import_agent/commands.py#L416-L440

今回は、環境変数のキーを `ENV_HOGE`、値を `FUGA` としてコマンドを実行しました。

```shell
% agentcore launch --env ENV_HOGE="FUGA"
（省略）
🔄 PROVISIONING started (total: 5s)
⠋ Launching Bedrock AgentCore...✅ PROVISIONING completed in 10.4s
🔄 PRE_BUILD started (total: 16s)
⠙ Launching Bedrock AgentCore...✅ PRE_BUILD completed in 10.4s
🔄 BUILD started (total: 26s)
⠹ Launching Bedrock AgentCore...✅ BUILD completed in 31.3s
🔄 POST_BUILD started (total: 58s)
⠇ Launching Bedrock AgentCore...✅ POST_BUILD completed in 5.2s
🔄 COMPLETED started (total: 63s)
⠼ Launching Bedrock AgentCore...✅ COMPLETED completed in 5.2s
🎉 CodeBuild completed successfully in 1m 8s
CodeBuild completed successfully
（省略）
```

デプロイ完了後、マネジメントコンソールで確認すると、期待どおり環境変数が設定されていました！
![](/images/20250818-bedrock-agentcore-env/2025-08-18-01-33-52.png)

## さいごに

今回は、セキュリティ上問題のない値を環境変数として扱いたかったので、`agentcore launch` コマンドの `--env` オプションを使う方法で満足です。
ただ、もし機密情報を扱う場合は、AWS Secrets Manager や AgentCore Identity を利用して、より安全に情報を管理するようにしましょう。
（自分自身も、使い方をきちんと理解せねば… 💪）
