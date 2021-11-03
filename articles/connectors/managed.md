---
title: マネージド コネクタの操作
description: Microsoft が管理するトリガーとアクションを使用することで、Azure Logic Apps を使用して他のアプリ、データ、サービス、システムを統合する自動ワークフローを作成します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 05/16/2021
ms.openlocfilehash: 4f0bb89992f76e7a8eb9311d733a3b791b73114a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131086604"
---
# <a name="managed-connectors-in-azure-logic-apps"></a>Azure Logic Apps のマネージド コネクタ

[マネージド コネクタ](apis-list.md)により、[組み込みのトリガーとアクション](built-in.md)を使用できない他のサービスやシステムにアクセスする手段が提供されます。 これらのトリガーとアクションを使用すると、データ、アプリ、クラウドベースのサービス、オンプレミスのシステムを統合するワークフローを作成できます。 組み込みのトリガーやアクションと比較して、これらのコネクタの場合は通常、特定のサービスやシステム (Azure Blob Storage、Office 365、SQL、Salesforce、SFTP サーバーなど) に関連付けられます。 Microsoft によって管理され、Azure でホストされているマネージ コネクタでは、通常、まずワークフローからの接続を作成し、ID を認証する必要があります。 繰り返しベースのトリガーと Webhook ベースのトリガーの両方を使用できます。そのため、繰り返しベースのトリガーを使用する場合は、[繰り返しの動作の概要](apis-list.md#recurrence-behavior)を確認してください。

サービス、システム、プロトコルが少数の場合、Azure Logic Apps には、組み込み操作と共に、その[マネージド コネクタ バージョン](managed.md)が用意されています。 使用できる数と範囲は、マルチテナントの Azure Logic Apps で実行される従量課金プランベースのロジック アプリ リソースを作成するか、シングル テナントの Azure Logic Apps で実行する Standard プランベースのロジック アプリ リソースを作成するかによって異なります。 詳細については、[シングルテナントとマルチテナント、および統合サービス環境 (ISE)](../logic-apps/single-tenant-overview-compare.md) に関するページを参照してください。 ほとんどの場合、組み込みのバージョンの方がパフォーマンス、機能、価格などに優れています。

たとえば、シングルテナントのロジックアプリを作成する場合は、Azure Service Bus、Azure Event Hubs、SQL Server、MQ で組み込みの操作を使用できます。 一部のケースでは、組み込みバージョンとマネージド コネクタ バージョンの両方を使用できます。 ほとんどの場合、組み込みのバージョンの方がパフォーマンス、機能、価格などに優れています。 マルチテナント ロジック アプリを作成する場合、Azure Functions、Azure App Services、および Azure API Management の組み込み操作を使用できます。

Azure Logic Apps 内の一部のマネージド コネクタは、複数のサブカテゴリに属しています。 たとえば、SAP コネクタは、[エンタープライズ コネクタ](#enterprise-connectors)でもあり[オンプレミス コネクタ](#on-premises-connectors)でもあります。

* [標準コネクタ](#standard-connectors)では、Azure Blob Storage、Office 365、SharePoint、Salesforce、Power BI、OneDrive など多くのサービスへのアクセスが提供されます。
* [エンタープライズ コネクタ](#enterprise-connectors)では、SAP、IBM MQ、IBM 3270 などのエンタープライズ システムへのアクセスが提供されます。
* [オンプレミス コネクタ](#on-premises-connectors)では、SQL Server、SharePoint Server、SAP、Oracle DB、ファイル共有などのオンプレミス システムへのアクセスが提供されます。
* [統合アカウント コネクタ](#integration-account-connectors)は、XML を変換および検証したり、フラット ファイルをエンコードおよびデコードしたり、AS2、EDIFACT、X12 プロトコルを使用して企業間 (B2B) メッセージを処理したりするのに役立ちます。
* [統合サービス環境コネクタ](#ise-connectors)は、特に ISE で実行するように設計され、ISE 以外のバージョンに対してメリットを提供します。

## <a name="standard-connectors"></a>Standard コネクタ

Azure Logic Apps には、これらのサービスとシステムを使用して自動ワークフローを構築するために、このような一般的な標準コネクタが用意されています。 一部の標準コネクタでは、[オンプレミス システム](#on-premises-connectors)または[統合アカウント](#integration-account-connectors)もサポートされています。

:::row:::
    :::column:::
        [![Azure Service Bus アイコン][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Logic Apps で最もよく使用されているコネクタを使用して非同期メッセージ、セッション、およびトピック サブスクリプションを管理します。
    :::column-end:::
    :::column:::
        [![SQL Server アイコン][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        オンプレミスの SQL Server、またはクラウド内の Azure SQL Database に接続して、レコードの管理、ストアド プロシージャの実行、クエリの実行を行えるようにします。
    :::column-end:::
    :::column:::
        [![Azure Blog Storage アイコン][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage**][azure-blob-storage-doc]
        \
        \
        Azure Storage アカウントに接続して、BLOB コンテンツの作成と管理ができるようにします。
    :::column-end:::
    :::column:::
        [![Office 365 Outlook アイコン][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        職場または学校のメール アカウントに接続して、メール、タスク、カレンダー イベントおよび会議、連絡先、要求などの作成と管理ができるようにします。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![STFP-SSH アイコン][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        SSH を使用してインターネットからアクセス可能な SFTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。
    :::column-end:::
    :::column:::
        [![SharePoint Online アイコン][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        SharePoint Online に接続して、ファイル、添付ファイル、フォルダーなどを管理できるようにします。
    :::column-end:::
    :::column:::
        [![Azure キュー アイコン][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure キュー**][azure-queues-doc]
        \
        \
        Azure Storage アカウントに接続して、キューとメッセージの作成と管理ができるようにします。
    :::column-end:::
    :::column:::
        [![FTP アイコン][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        インターネットからアクセス可能な FTP サーバーに接続して、ファイルとフォルダーを操作できるようにします。
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![ファイル システム アイコン][file-system-icon]][file-system-doc]
        \
        \
        [**ファイル システム**][file-system-doc]
        \
        \
        オンプレミスのファイル共有に接続して、ファイルの作成と管理ができるようにします。
    :::column-end:::
    :::column:::
        [![Azure Event Hubs アイコン][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        イベント ハブを通じてイベントを使用したり、発行したりします。 たとえば、ロジック アプリから Event Hubs を使用して出力を取得し、リアルタイム分析プロバイダーに送信できます。
    :::column-end:::
    :::column:::
        [![Azure Event Grid アイコン][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Azure やサードパーティのリソースが変更されたときなどに、Event Grid が発行するイベントを監視します。
    :::column-end:::
    :::column:::
        [![Salesforce アイコン][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Salesforce アカウントに接続して、レコード、ジョブ、オブジェクトなどの項目の作成と管理ができるようにします。
    :::column-end:::
:::row-end:::

## <a name="on-premises-connectors"></a>オンプレミス コネクタ

オンプレミス システムへの接続を作成するには、最初に[オンプレミス データ ゲートウェイのダウンロード、インストール、設定][gateway-doc]を行う必要があります。 このゲートウェイは、セキュリティで保護された通信チャネルを提供します。必要なネットワーク インフラストラクチャを設定する必要はありません。

次のコネクタは、オンプレミス システムのデータとリソースにアクセスするために Azure Logic Apps で提供され、よく使用されているいくつかの[標準コネクタ](#standard-connectors)です。 オンプレミス コネクタの一覧については、「[サポートされるデータ ソース](../logic-apps/logic-apps-gateway-connection.md#supported-connections)」を参照してください。

:::row:::
    :::column:::
        [![Biztalk Server アイコン][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![ファイル システム アイコン][file-system-icon]][file-system-doc]
        \
        \
        [**ファイル システム**][file-system-doc]
    :::column-end:::
    :::column:::
        [![IBM DB2 アイコン][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![IBM Informix アイコン][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL アイコン][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB アイコン][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL アイコン][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server アイコン][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server アイコン][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata アイコン][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>統合アカウント コネクタ

統合アカウント コネクタは、Azure Logic Apps での[企業間 (B2B) 通信シナリオ](../logic-apps/logic-apps-enterprise-integration-overview.md)に特に対応しています。 [統合アカウントを作成](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)し、取引先、契約、マップ、スキーマなどの B2B 成果物を定義したら、統合アカウント コネクタを使用して、メッセージのエンコードとデコード、コンテンツの変換などを行うことができます。

たとえば、Microsoft BizTalk Server を使用する場合、[BizTalk Server オンプレミス コネクタ](#on-premises-connectors)を使用してワークフローからの接続を作成できます。 これで、これらの統合アカウント コネクタを使用して、ワークフローで BizTalk のような操作を拡張または実行できます。

> [!NOTE]
> マルチテナント、従量課金プランベースの Azure Logic Apps で統合アカウント コネクタを使用する前に、[ロジック アプリ リソースを統合アカウントにリンクする](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)必要があります。 

:::row:::
    :::column:::
        [![AS2 のデコード アイコン][as2-icon]][as2-doc]
        \
        \
        [**AS2 のデコード**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 のエンコード アイコン][as2-icon]][as2-doc]
        \
        \
        [**AS2 のエンコード**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT のデコード アイコン][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT のデコード**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT のエンコード アイコン][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT のエンコード**][edifact-encode-doc]
    :::column-end:::
    :::column:::
        [![X12 のデコード アイコン][x12-icon]][x12-decode-doc]
        \
        \
        [**X12 のデコード**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 のエンコード アイコン][x12-icon]][x12-encode-doc]
        \
        \
        [**X12 のエンコード**][x12-encode-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>エンタープライズ コネクタ

次のコネクタでは、エンタープライズ システムへのアクセスが提供されます。これには、追加コストがかかります。

:::row:::
    :::column:::
        [![IBM 3270 アイコン][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ アイコン][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP アイコン][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="ise-connectors"></a>ISE コネクタ

統合サービス環境 (ISE) では、これらのマネージド コネクタに [ISE バージョン](apis-list.md#ise-and-connectors)もあります。これには、マルチテナント バージョンとは異なる機能があります。

> [!NOTE]
> ISE 実行されるロジック アプリとそのコネクタは、これらのコネクタが実行されている場所には関係なく、固定価格プランまたは使用量ベースの価格プランに従います。 詳細については、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)および [Logic Apps の価格の詳細](https://azure.microsoft.com/pricing/details/logic-apps/)に関するページをご覧ください。

:::row:::
    :::column:::
        [![AS2 ISE アイコン][as2-icon]][as2-doc]
        \
        \
        [**AS2** ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE アイコン][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE アイコン][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE アイコン][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE アイコン][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE アイコン][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure Files ISE アイコン][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure Files** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE アイコン][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor Logs ISE アイコン][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor ログ** ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE アイコン][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE アイコン][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure Table Storage ISE アイコン][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Queues ISE アイコン][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure キュー** ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE アイコン][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![File System ISE アイコン][file-system-icon]][file-system-doc]
        \
        \
        [**ファイル システム** ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE アイコン][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** ISE][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE アイコン][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![IBM DB2 ISE アイコン][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![IBM MQ ISE アイコン][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE アイコン][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE アイコン][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE アイコン][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE アイコン][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE アイコン][x12-icon]][x12-doc]
        \
        \
        [**X12** ISE][x12-doc]
    :::column-end:::
:::row-end:::

詳細については、以下のトピックを参照してください。

* [Azure Logic Apps から Azure Virtual Network リソースにアクセスする](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps から Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps から呼び出しできるカスタム API の作成](../logic-apps/logic-apps-create-api-app.md)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "クラウドおよびオンプレミス インフラストラクチャ用のオートメーション ジョブを作成して管理します"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage コネクタを使用して BLOB コンテナーのファイルを管理します。"
[azure-cosmos-db-doc]: ./connectors-create-api-cosmos-db.md "Azure Cosmos DB に接続して、Azure Cosmos DB ドキュメントにアクセスして管理できるようにします"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure やサードパーティのリソースが変更されたときなど、Event Grid により発行されるイベントを監視します"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Azure Event Hubs に接続して、ロジック アプリと Event Hubs 間でイベントを送受信できるようにします"
[azure-file-storage-doc]: /connectors/azurefile/ "Azure Storage アカウントに接続して、ファイルを作成、更新、取得、削除できるようにします"
[azure-key-vault-doc]: /connectors/keyvault/ "シークレットとキーを管理できるように Azure Key Vault に接続します"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Log Analytics ワークスペースと Application Insights コンポーネントの全体にわたって、Azure Monitor ログに対するクエリを実行します"
[azure-queues-doc]: /connectors/azurequeues/ "Azure Storage アカウントに接続して、キューとメッセージの作成と管理ができるようにします"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus のキュー、トピック、およびトピック サブスクリプションからのメッセージを管理します"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Azure Synapse Analytics に接続して、データを表示できるようにします"
[azure-table-storage-doc]: /connectors/azuretables/ "Azure Storage アカウントに接続して、テーブルなどの作成、更新、クエリ実行ができるようにします"
[biztalk-server-doc]: /connectors/biztalk/ "BizTalk Server に接続して、BizTalk ベースのアプリケーションを Azure Logic Apps と並行して実行できるようにします"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "オンプレミスのファイル システムに接続します。"
[ftp-doc]: ./connectors-create-api-ftp.md "FTP/FTPS サーバーに接続して、ファイルのアップロード、取得、削除などの FTP タスクを実行できます。"
[github-doc]: ./connectors-create-api-github.md "GitHub に接続して、問題を追跡できます。"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google カレンダーに接続してカレンダーを管理できます"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google スプレッドシートに接続して、シートを変更できるようにします"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Google タスクに接続して、タスクを管理できるようにします"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM メインフレーム上の 3270 アプリに接続する"
[ibm-db2-doc]: ./connectors-create-api-db2.md "クラウドまたはオンプレミスの IBM DB2 に接続します。行の更新、テーブルの取得などを行います。"
[ibm-informix-doc]: ./connectors-create-api-informix.md "クラウドまたはオンプレミスの Informix に接続します。行の読み取り、テーブルの一覧表示などを実行します。"
[ibm-mq-doc]: ./connectors-create-api-mq.md "オンプレミスまたは Azure 内の IBM MQ に接続し、メッセージを送受信します。"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram に接続します。イベントをトリガーしたり、イベントに対するアクションを実行したりします。"
[mandrill-doc]: ./connectors-create-api-mandrill.md "通信のために Mandrill に接続します。"
[mysql-doc]: /connectors/mysql/ "オンプレミスの MySQL データベースに接続して、データの読み取りと書き込みができるようにします"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "職場または学校アカウントに接続して、電子メールの送受信、予定表と連絡先の管理などができるようにします"
[onedrive-doc]: ./connectors-create-api-onedrive.md "個人用の Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧表示などができるようにします"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "ビジネス向けの Microsoft OneDrive に接続して、ファイルのアップロード、削除、一覧表示などができるようにします"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle データベースに接続して、行の追加、挿入、削除などができるようにします"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Outlook メールボックスに接続して、電子メール、予定表、連絡先などを管理できるようにします"
[postgre-sql-doc]: /connectors/postgresql/ "PostgreSQL データベースに接続して、テーブルからデータを読み取ることができるようにします"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce アカウントに接続します。アカウント、潜在顧客、営業案件などを管理します。"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "オンプレミスの SAP システムに接続します。"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid に接続します。メールの送信と受信者リストの管理を行います。"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH を使用して SFTP アカウントに接続します。ファイルのアップロード、取得、削除などを実行します。"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "SharePoint オンプレミス サーバーに接続します。ドキュメント、リスト アイテムなどを管理します。"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "SharePoint Online に接続します。ドキュメント、リスト アイテムなどを管理します。"
[slack-doc]: ./connectors-create-api-slack.md "Slack に接続し、Slack チャンネルにメッセージを投稿します。"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP サーバーに接続し、添付ファイルを含むメールを送信します。"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "通信のために SparkPost に接続します。"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database または SQL Server に接続します。SQL データベース テーブルのエントリを作成、更新、取得、削除します"
[teradata-doc]: /connectors/teradata/ "Teradata データベースに接続して、テーブルからデータを読み取ります"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio に接続します。メッセージの送信と取得、利用可能な番号の取得、着信電話番号の管理などを実行します。"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube に接続します。ビデオとチャンネルを管理します。"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[x12-icon]: ./media/apis-list/x12.png

<!-- Integration account connector docs -->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 プロトコルを使用するメッセージをエンコードおよびデコードします"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT プロトコルを使用するメッセージをエンコードおよびデコードします"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT プロトコルを使用するメッセージをデコードします"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT プロトコルを使用するメッセージをエンコードします"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 プロトコルを使用するメッセージをエンコードおよびデコードします"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 プロトコルを使用するメッセージをデコードします"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 プロトコルを使用するメッセージをエンコードします"

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "オンプレミス データ ゲートウェイを使用して、ロジック アプリからオンプレミスのデータに接続する"