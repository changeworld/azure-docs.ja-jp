---
title: Azure メンテナンス イベントの計画
description: Azure SQL データベースおよび Azure SQL Managed Instance で計画メンテナンス イベントを準備する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: mathoma
ms.date: 3/23/2021
ms.openlocfilehash: a697c0a3095963760d6a95159790c02cbab14d71
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121634"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database および Azure SQL Managed Instance での Azure メンテナンス イベントの計画
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database および Azure SQL Managed Instance 内のデータベースに計画メンテナンス イベントを準備する方法について説明します。

## <a name="what-is-a-planned-maintenance-event"></a>計画メンテナンス イベントとは

Azure SQL Database サービスと Azure SQL Managed Instance サービスのセキュリティ、コンプライアンス、安定、パフォーマンスを維持するため、サービス コンポーネントによる更新がほぼ連続して行われています。 最新で堅牢なサービス アーキテクチャと、[ホット パッチ](https://aka.ms/azuresqlhotpatching)のような革新的なテクノロジにより、ほとんどの更新は完全に透過的であり、サービスの可用性に影響を与えることはありません。 ただし、いくつかの更新の種類では、サービスが短時間中断し、特別な処理が必要になる場合があります。 

計画メンテナンス中は、データベース クォーラムのメンバーは 1 つずつオフラインになります。これは、対応するプライマリ レプリカを 1 つ確保するためです。 Business Critical データベースおよび Premium データベースについては、クライアントのダウンタイムが発生しないように、少なくとも 1 つのセカンダリ レプリカもオンラインになります。 プライマリ レプリカをオフラインにする必要がある場合は、再構成プロセスが発生します。 Business Critical データベースおよび Premium データベースについては、セカンダリ レプリカの 1 つが新しいプライマリ レプリカになります。 General Purpose データベース、Standard データベース、Basic データベースについては、十分な空き容量がある別のステートレス コンピューティング ノードにプライマリ レプリカが移動します。

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>計画メンテナンス イベント時に予期されること

メンテナンス イベントでは、メンテナンス イベントの開始時に集められたプライマリとセカンダリのレプリカに応じて、1 つまたは複数の再構成が発生する場合があります。 平均すると、1 回の計画メンテナンス イベントあたり 1.7 回の再構成が発生します。 通常、再構成は 30 秒以内に完了します。 平均は 8 秒です。 アプリケーションが既に接続されている場合は、データベースの新しいプライマリ レプリカに再接続する必要があります。 新しいプライマリ レプリカがオンラインになる前にデータベースで再構成を行っている間に新しい接続が試行された場合、エラー 40613 (データベースは使用できません):" *"サーバー '{servername}' 上のデータベース '{databasename}' は現在使用できません。後で接続を再試行してください。"* データベースに実行時間の長いクエリがある場合、このクエリは再構成中に中断され、再開する必要があります。

## <a name="how-to-simulate-a-planned-maintenance-event"></a>計画メンテナンス イベントをシミュレートする方法

運用環境にデプロイする前に、クライアント アプリケーションがメンテナンス イベントに対する回復性を備えていることを確認することは、アプリケーションの障害のリスクを軽減するのに役立ち、エンド ユーザーにとっては、アプリケーションの可用性に寄与します。PowerShell、CLI、または REST API を使用して[アプリケーションの障害回復性をテスト](./high-availability-sla.md#testing-application-fault-resiliency)することによって、計画メンテナンス イベント中にクライアント アプリケーションの動作をテストできます。 Managed Instance の[手動フェールオーバーの開始](https://aka.ms/mifailover-techblog)に関するページも参照してください。 プライマリ レプリカをオフラインにするメンテナンス イベントと同じ動作が生成されます。

## <a name="retry-logic"></a>再試行ロジック

クラウド データベース サービスに接続するクライアント運用アプリケーションでは、堅牢な接続[再試行ロジック](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)を実装する必要があります。 これにより、再構成はエンド ユーザーに対して透過的になり、少なくとも、悪影響を最小限に抑えることができます。

### <a name="service-health-alert"></a>Service Health アラート

サービスの問題や計画メンテナンス作業についてのアラートを受け取りたい場合、Azure portal から、適切な種類のイベントとアクション グループに Service Health アラートを組み合わせて使用できます。 詳細については、[Azure サービスの通知でアラートを受け取る](../../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal)方法に関するページを参照してください。

## <a name="resource-health"></a>リソース ヘルス

データベースでログオン エラーが発生している場合は、[Azure portal](https://portal.azure.com) の [[リソース正常性]](../../service-health/resource-health-overview.md#get-started) ウィンドウで、現在の状態を確認します。 [正常性の履歴] セクションには、(該当する場合は) イベントごとのダウンタイムの理由が含まれています。

## <a name="maintenance-window-feature"></a>メンテナンス期間機能

メンテナンス期間機能を使用すると、対象となる Azure SQL データベースと SQL マネージド インスタンスに対して、予測可能なメンテナンス期間スケジュールを構成できます。 詳細については、[メンテナンス期間](maintenance-window.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- Azure SQL Database と Azure SQL Managed Instance の[リソース正常性](resource-health-to-troubleshoot-connectivity.md)について説明します。
- 再試行ロジックの詳細については、「[一時エラーの再試行ロジック](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)」を参照してください。
- [メンテナンス期間](maintenance-window.md)機能を使用して、メンテナンス期間のスケジュールを構成します。