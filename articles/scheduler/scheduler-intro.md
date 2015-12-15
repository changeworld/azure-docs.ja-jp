<properties
 pageTitle="Azure Scheduler とは | Microsoft Azure"
 description="Azure Scheduler では、クラウドで実行するアクションを宣言的に記述することができます。その後スケジュールを設定し、アクションを自動的に実行します。"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# Azure Scheduler とは

Azure Scheduler では、クラウドで実行するアクションを宣言的に記述することができます。その後スケジュールを設定し、アクションを自動的に実行します。Scheduler では、[Azure ポータル](scheduler-get-started-portal.md)、コード、[REST API](https://msdn.microsoft.com/library/dn528946)、または Azure PowerShell を使用してこれを実行します。

Scheduler には、スケジュールされた作業の作成、保守、呼び出しの機能があります。Scheduler が、ワークロードをホストしたり、コードを実行したりすることはありません。Scheduler は他の場所 (Azure、オンプレミス、または別のプロバイダー) でホストされるコードを_呼び出す_だけです。HTTP、HTTPS、ストレージ キューを使用して呼び出します。

Scheduler では[ジョブ](scheduler-concepts-terms.md)をスケジュールし、レビュー可能なジョブ実行結果の履歴を保持し、ワークロードを確実かつ高い信頼度で実行するようにスケジュールします。Azure WebJobs (Azure App Service の Web Apps 機能の一部) およびその他の Azure スケジュール機能では、Scheduler をバック グラウンドで使用します。[Scheduler REST API](https://msdn.microsoft.com/library/dn528946) では、こうしたアクションにおける通信を管理します。このため、Scheduler は[複雑なスケジュールおよび高度な定期実行](scheduler-advanced-complexity.md)に容易に対応できます。

Scheduler の使用に適しているシナリオがいくつかあります。次に例を示します。

+ _定期的なアプリケーション アクション_: Twitter からのデータを定期的に収集し、フィードに集めます。
+ _日々のメンテナンス:_ 日々のログの排除、バックアップの実行、その他のメンテナンス タスク。たとえば、管理者は、今後 9 か月間、毎日午前 1 時にデータベースをバックアップすることができます。

Scheduler では、ジョブおよび[ジョブ コレクション](scheduler-concepts-terms.md)の作成、更新、削除、表示、および管理を、プログラム、スクリプト、およびポータルを使用して実行することができます。

## 関連項目

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法](scheduler-advanced-complexity.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_1210_2015-->