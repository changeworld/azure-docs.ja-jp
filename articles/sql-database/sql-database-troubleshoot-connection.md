<properties
	pageTitle="Azure SQL Database では現在サーバーのデータベースのトラブルシューティングを使用できません"
	description="Azure SQL Database の接続エラーを特定して解決する手順。"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="daleche"/>

# 「サーバー上のデータベースは現在使用できません。後で再試行してください」などの接続エラーのトラブルシューティング
「サーバー <servername> のデータベース <dbname> は現在使用できません」は、Azure SQL Database の最も一般的で一時的な接続エラーです。一時的な接続エラーは、通常新しいサーバーへのフェールオーバーや、一時的なシステム障害などのプラットフォームの再構成が原因であり、長くは続きません。別のエラーが発生している場合は、原因の手掛かりとなる[エラー メッセージ](sql-database-develop-error-messages.md)を評価し、問題が一時的か永続的かを判断して、このトピックのガイダンスを使用します。

## 一時的な接続の問題を解決する手順
1.	アプリで再試行ロジックを使用していることを確認します。一般的な再試行戦略については、[接続の問題](sql-database-connectivity-issues.md)のトピックと、[ベスト プラクティスと設計のガイドライン](sql-database-connect-central-recommendations.md)のトピックを参照してください。次に、具体的な内容を[コード サンプル](sql-database-develop-quick-start-client-code-samples.md)で確認してください。
2.	データベースがリソースの制限に近づくと、一時的な接続の問題に見える場合があります。[パフォーマンスの問題のトラブルシューティング](sql-database-troubleshoot-performance.md)のトピックを参照してください。
3.	接続の問題が続く場合は、サポートに連絡してサポート ケースを登録してください。

## 永続的な接続の問題を解決する手順
アプリがまったく接続できない場合、通常は IP とファイアウォールの構成の問題です。これには、クライアント側 (たとえば、新しい IP アドレスやプロキシ) のネットワークの再構成が含まれます。接続パラメーター (接続文字列など) の入力が間違っている場合もあります。

1.	クライアントの IP アドレスを許可するには、[ファイアウォール規則](sql-database-configure-firewall-settings.md)を設定します。
2.	クライアントとインターネットの間のすべてのファイアウォールで、送信接続用のポート 1433 が開いていることを確認します。
3.	接続文字列およびその他の接続設定を確認します。[接続の問題のトピック](sql-database-connectivity-issues.md)で、接続文字列のセクションを確認します。
4.	ダッシュ ボードでサービスの正常性を確認します。リージョンの障害があると思われる場合は、新しいリージョンへの回復手順を[障害からの回復](sql-database-disaster-recovery.md)のトピックで参照してください。
5.	接続の問題が続く場合は、サポートに連絡してサポート ケースを登録してください。

<!---HONumber=AcomDC_0128_2016-->