---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行: 移行後の管理'
description: 移行が正常に完了したら、次のフェーズで、新しいクラウドベースのデータ ワークロード リソースを管理します。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5c7ef5f12cf4ec1f6776abbc405cf5145c27c0f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297451"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-post-migration-management"></a>オンプレミスの MySQL から Azure Database for MySQL への移行: 移行後の管理

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[MySQL Workbench を使用したデータ移行](09-data-migration-with-mysql-workbench.md)

## <a name="monitoring-and-alerts"></a>監視とアラート

移行が正常に完了したら、次のフェーズで、新しいクラウドベースのデータ ワークロード リソースを管理します。 管理操作には、コントロール プレーンとデータ プレーンの両方のアクティビティが含まれます。 コントロール プレーン アクティビティは、Azure リソースに関連しています。それに対し、データ プレーンは Azure リソース (この場合、MySQL) **内** にあります。

Azure Database for MySQL は、[Azure Monitor](../../../azure-monitor/overview.md)、[Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md)、[Microsoft Sentinel](../../../sentinel/overview.md) などの Azure ベースのツールを使用して、これら両方の種類の操作アクティビティを監視する機能を備えています。 Azure ベースのツールに加え、これらのログを使用するようにセキュリティ情報イベント管理 (SIEM) システムを構成することもできます。

新しいクラウドベース ワークロードの監視にどちらのツールを使用する場合でも、疑わしいアクティビティについて Azure とデータベース管理者に警告するアラートを作成する必要があります。 特定のアラート イベントに明確に定義された修復パスがある場合、アラートによって自動化された [Azure Runbook](../../../automation/learn/powershell-runbook-managed-identity.md) を起動し、そのイベントに対処できます。

完全に監視された環境を作成する最初の手順は、MySQL ログ データが Azure Monitor に流れるようにすることです。 詳細については、「[Azure portal での Azure Database for MySQL の監査ログの構成とアクセス](../../howto-configure-audit-logs-portal.md)」を参照してください。

ログ データが流れるようになったら、[Kusto Query language (KQL)](/azure/data-explorer/kusto/query/) クエリ言語を使用して、さまざまなログ情報に対するクエリを実行します。 KQL を使い慣れていない管理者は、[こちら](/azure/data-explorer/kusto/query/sqlcheatsheet)または「[Azure Monitor でログ クエリの使用を開始する](../../../azure-monitor/logs/get-started-queries.md)」ページで SQL から KQL へのチート シートを見つけることができます。

たとえば、Azure Database for MySQL のメモリ使用量を取得するには、次のようにします。

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "memory\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
CPU 使用率を取得する場合:

```
AzureMetrics
| where TimeGenerated \> ago(15m)
| limit 10
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where MetricName == "cpu\_percent"
| project TimeGenerated, Total, Maximum, Minimum, TimeGrain, UnitName 
| top 1 by TimeGenerated
```
KQL クエリを作成したら、これらのクエリに基づいて[ログ アラート](../../../azure-monitor/alerts/alerts-unified-log.md)を作成します。

## <a name="server-parameters"></a>サーバー パラメーター

移行の一環として、高速エグレスをサポートするようにオンプレミスの[サーバー パラメーター](../../concepts-server-parameters.md)が変更されている可能性があります。 また、Azure Database for MySQL パラメーターにも、高速イングレスをサポートするように変更が行われています。 移行後は、Azure サーバー パラメーターを、オンプレミスのワークロード用に最適化された元の値に戻す必要があります。

ただし、必ず確認し、ワークロードと環境に適したサーバー パラメーターの変更を行ってください。 オンプレミス環境に最適な値の中には、クラウドベースの環境に最適でないものがあります。 さらに、現在のオンプレミスのパラメーターを Azure に移行しようとしている場合は、それらが実際に設定できることを確認してください。

一部のパラメーターは、Azure Database for MySQL で変更することが許可されません。

## <a name="powershell-module"></a>PowerShell モジュール

Azure Database for MySQL の管理には、Azure portal と Windows PowerShell を使用できます。 PowerShell の使用を開始するには、次の PowerShell コマンドを使用して、MySQL 用の Azure PowerShell コマンドレットをインストールします。

`Install-Module -Name Az.MySql`

モジュールをインストールしたら、次のようなチュートリアルを参照して、管理アクティビティのスクリプトを活用する方法を確認してください。

  - [チュートリアル:PowerShell を使用して Azure Database for MySQL を設計する](../../tutorial-design-database-using-powershell.md)

  - [PowerShell を使用して Azure Database for MySQL サーバーをバックアップおよび復元する方法](../../howto-restore-server-powershell.md)

  - [PowerShell を使用して Azure Database for MySQL のサーバー パラメーターを構成する](../../howto-configure-server-parameters-using-powershell.md)

  - [PowerShell を使用した Azure Database for MySQL サーバーのストレージの自動拡張](../../howto-auto-grow-storage-powershell.md)

  - [PowerShell を使用して Azure Database for MySQL の読み取りレプリカを作成し、管理する方法](../../howto-read-replicas-powershell.md)

  - [PowerShell を使用して Azure Database for MySQL サーバーを再起動する](../../howto-restart-server-powershell.md)

## <a name="azure-database-for-mysql-upgrade-process"></a>Azure Database for MySQL のアップグレード プロセス

Azure Database for MySQL は PaaS オファリングであるため、管理者がオペレーティング システムや MySQL ソフトウェアの更新を管理する必要はありません。 ただし、アップグレード プロセスはランダムである場合があること、およびデプロイ時に MySQL サーバーのワークロードが停止される可能性があることを知っておくことが重要です。 特定のインスタンスがメンテナンス モードになったときに読み取りレプリカにワークロードを経路変更するようにして、これらのダウンタイムに備えて計画してください。

> [!NOTE]
> このスタイルのフェールオーバー アーキテクチャでは、この種のフェールオーバー シナリオをサポートするために、アプリケーション データ レイヤーに対する変更が必要になる場合があります。 読み取りレプリカが読み取りレプリカとして維持され、レベル上げされない場合、アプリケーションはデータの読み取りのみを行うことができ、何らかの操作でデータベースに情報を書き込もうとすると失敗することがあります。

[計画メンテナンス通知](../../concepts-monitoring.md#planned-maintenance-notification)機能により、更新プログラムまたは重要なセキュリティ パッチのインストールの最大 72 時間前に、リソース所有者に通知されます。 データベース管理者は、計画および計画外のメンテナンスについてアプリケーション ユーザーに通知することが必要になる場合があります。

> [!NOTE]
> Azure Database for MySQL のメンテナンス通知は極めて重要です。 データベース メンテナンスにより、データベースおよび接続されているアプリケーションが一定期間停止される可能性があります。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI では、Azure アクティビティ ログを利用し、MySQL ログを [Log Analytics ワークスペース](../../../azure-monitor/logs/design-logs-deployment.md)に流すことができるようにしました。 このワークスペースは [Microsoft Sentinel](../../../sentinel/index.yml) の一部として構成され、すべての[脅威分析](../../concepts-security.md#threat-protection)イベントを明らかにして、インシデントが作成されるようにします。

MySQL DBA は、Azure Database for [MySQL の Azure PowerShell コマンドレット](../../quickstart-create-mysql-server-database-using-azure-powershell.md)をインストールし、Azure portal に毎回ログオンしなくて済むように、MySQL サーバーの管理を自動化しました。

## <a name="management-checklist"></a>管理のチェックリスト

  - CPU やメモリなどの一般的な内容に関するリソース アラートを作成します。

  - 移行後に、サーバー パラメーターがターゲット データのワークロード用に構成されていることを確認します。

  - 一般的な管理タスクをスクリプト化します。

  - アップグレードやパッチなどのメンテナンス イベントの通知を設定します。 必要に応じてユーザーに通知します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最適化](./11-optimization.md)
