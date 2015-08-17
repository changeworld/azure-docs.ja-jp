<properties
 pageTitle="Scheduler とは"
 description=""
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
 ms.date="08/04/2015"
 ms.author="krisragh"/>

# Scheduler とは

Azure Scheduler では、クラウドで実行するアクションを宣言的に記述することができます。その後スケジュールを設定し、アクションを自動的に実行します。Azure Scheduler では、[Azure ポータル](scheduler-get-started-portal.md)、コード、[REST API](https://msdn.microsoft.com/library/dn528946)、PowerShell を使用してこれを実行します。

Azure Scheduler では、スケジュールされた作業を維持、管理、スケジュール、呼び出します。Azure Scheduler では、ワークロードをホストしたり、コードを実行することはありません。別の場所でホストされるコードを_呼び出す_のみです。コードは Azure、オンプレミス、その他のプロバイダーでホストされている場合があります。HTTP、HTTPS、ストレージ キューを使用して呼び出します。

Azure Scheduler ではジョブをスケジュールし、問い合わせに対応できるジョブの実行結果履歴を保持し、ワークロードを確実かつ高い信頼度で実行するようにスケジュールします。Azure Scheduler は Azure Mobile Services Scheduled Scripts、Azure Web Apps WebJobs、その他の Azure スケジュール機能のバックグラウンドで使用されています。[Scheduler REST API](https://msdn.microsoft.com/library/dn528946) では、こうしたアクションにおける通信を管理します。そのため、Scheduler では[複雑なスケジュールと高度な定期実行を簡単に](scheduler-advanced-complexity.md)サポートします。

Azure Scheduler の使用に役立つシナリオがいくつかあります。次に例を示します。

+ _定期的なアプリケーション アクション_: Twitter からのデータを定期的に収集し、データをフィードに集めます。
+ _日常的なメンテナンス_: 日常的なログの排除、バックアップやその他のメンテナンス タスクを実施します。たとえば、管理者は次の 9 か月間、毎日午前 1 時に、データベースをバックアップするように選択できます。

Scheduler では、ポータルでスクリプトを使用し、["ジョブ コレクション" や "ジョブ"](scheduler-concepts-terms.md) をプログラムによって作成、更新、削除、表示、および管理できます。

## 関連項目

 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)

 [管理ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Plans and Billing in Azure Scheduler (Azure Scheduler のプランと課金)](scheduler-plans-billing.md)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler (Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法)](scheduler-advanced-complexity.md)

 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Scheduler PowerShell Cmdlets Reference (Scheduler PowerShell コマンドレット リファレンス)](scheduler-powershell-reference.md)

 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Scheduler の制限、規定値、エラー コード](scheduler-limits-defaults-errors.md)

 [Scheduler 送信認証](scheduler-outbound-authentication.md)
 

<!---HONumber=August15_HO6-->