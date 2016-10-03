<properties
	pageTitle="サーバーのデータベースは現在使用できません、SQL データベースに接続してください | Microsoft Azure"
	description="アプリケーションが SQL データベースに接続するときの ";サーバーのデータベースは現在使用できません"; というエラーをトラブルシューティングします。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""
	keywords="サーバーのデータベースは現在使用できません, SQL データベースへの接続"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="daleche"/>

# SQL データベースに接続するときの "サーバーのデータベースは現在使用できません" というエラー
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

アプリケーションが Azure SQL データベースに接続するとき、次のエラー メッセージが表示されます。

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] 通常、このエラー メッセージは (短時間の) 一時的なものです。

このエラーは、Azure データベースが移動 (または再構成) されたり、アプリケーションが SQL データベースへの接続を失ったときに発生します。SQL データベースの再構成イベントは、計画されたイベント (ソフトウェアのアップグレードなど) または計画されていないイベント (プロセスのクラッシュ、負荷分散など) が原因で発生します。通常、ほとんどの再構成イベントは一時的であり、長くて 1 分もかかりませんが、これらのイベントは、大規模なトランザクションによる実行時間の長い復旧など、場合によっては、完了に時間がかかることがあります。

## 一時的な接続の問題を解決する手順
1.	アプリケーションによって報告されたエラーで発生している既知の障害については、[Microsoft Azure サービス ダッシュボード](https://azure.microsoft.com/status)を参照してください。
2. Azure SQL Database など、クラウド サービスに接続するアプリケーションは、定期的な再構成イベントを想定し、これらをアプリケーション エラーとしてユーザーに示すのではなく、再試行ロジックを実装してこれらのエラーを処理します。詳細および一般的な再試行戦略については、[一時エラー](sql-database-connectivity-issues.md)のセクション、および「[SQL Database の開発: 概要](sql-database-develop-overview.md)」のベスト プラクティスや設計ガイドラインを参照してください。その後、具体的な内容を「[SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」のコード サンプルで確認してください。
3.	データベースがリソースの制限に近づくと、一時的な接続の問題に見える場合があります。[パフォーマンスの問題のトラブルシューティング](sql-database-troubleshoot-performance.md)のトピックを参照してください。
4.	接続の問題が解消されない場合、アプリケーションでのエラーの継続時間が 60 秒を超えた場合、または 1 日にエラーが複数回発生した場合は、[Azure サポート](https://azure.microsoft.com/support/options) サイトの **[サポートの要求]** を選択して、サポート要求を送信してください。

## 次のステップ
- 別のエラーが発生している場合は、原因の手掛かりとなる[エラー メッセージ](sql-database-develop-error-messages.md)を確認してください。
- 問題が解消されない場合は、「[Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md)」のガイダンスを参照してください。

<!---HONumber=AcomDC_0921_2016-->