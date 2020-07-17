---
title: Azure Automation のデータの管理
description: この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれています。  現在は、データの保持、Azure Automation のバックアップ、Azure Automation でのディザスター リカバリー が含まれています。
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984659"
---
# <a name="managing-azure-automation-data"></a>Azure Automation のデータの管理

この記事には、Azure Automation 環境でのデータの管理に関する複数のトピックが含まれています。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="data-retention"></a>データの保持

Azure Automation でリソースを削除すると、完全に削除される前に、監査目的に応じた日数だけ保持されます。 この期間にリソースを表示または使用することはできません。 このポリシーは、削除された Automation アカウントに属するリソースにも適用されます。

次の表は、さまざまなリソースの保持ポリシーをまとめたものです。

| Data | ポリシー |
|:--- |:--- |
| アカウント |アカウントは、ユーザーによって削除された日から 30 日後に完全に消去されます。 |
| アセット |アセットは、ユーザーによって削除された日から 30 日後、またはアセットを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます。 |
| DSC ノード |DSC ノード は、Azure portal または Windows PowerShell の [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) コマンドレットを使用して、Automation アカウントから登録解除された日から 30 日後に、完全に消去されます。 また、ノードは、ノードを保持するアカウントがユーザーによって削除されてから 30 日後に、完全に消去されます。 |
| ジョブ |ジョブは、変更 (ジョブの完了など) が停止または中断された日から 30 日後に、削除されて完全に消去されます。 |
| モジュール |モジュールは、ユーザーによって削除された日から 30 日後、またはモジュールを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます。 |
| ノード構成/MOF ファイル |古いノード構成は、新しいノード構成が生成された日から 30 日後に、完全に消去されます。 |
| ノード レポート |ノード レポートは、そのノードの新しいレポートが生成されてから 90 日後に、完全に消去されます。 |
| Runbooks |Runbook は、ユーザーによってリソースが削除された日から 30 日後、またはリソースを保持するアカウントがユーザーによって削除された日から 30 日後に、完全に消去されます。 |

保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。 ただし、さらに長くデータを保持する必要がある場合は、[Azure Automation ジョブのデータを Azure Monitor ログに転送する](automation-manage-send-joblogs-log-analytics.md)ことができます。

## <a name="data-backup"></a>[データ バックアップ]

Azure で Automation アカウントを削除すると、そのアカウント内のすべてのオブジェクトが削除されます。 オブジェクトには、Runbook、モジュール、構成、設定、ジョブ、アセットが含まれます。 アカウントを削除した後で、それらを復旧することはできません。 削除する前に、以下の情報を使用して Automation アカウントの内容をバックアップできます。

### <a name="runbooks"></a>Runbooks

Azure Portal または Windows PowerShell の [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。 「[Azure Automation で Runbook を管理する](manage-runbooks.md)」で説明されているようにして、これらのスクリプト ファイルを別の Automation アカウントにインポートすることができます。

### <a name="integration-modules"></a>統合モジュール

Azure Automation から統合モジュールをエクスポートすることはできません。 Automation アカウントの外部で使用できるようにする必要があります。

### <a name="assets"></a>アセット

Azure Automation のアセット (証明書、接続、資格情報、スケジュール、変数) をエクスポートすることはできません。 代わりに、Azure portal と Azure コマンドレットを使用して、これらのアセットの詳細を記録できます。 その後、これらの詳細を使用して、Runbook で使用されるアセットを作成し、別の Automation アカウントにインポートします。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。 これらの値がわからない場合は、Runbook で取得できます。 変数の値を取得する方法については、「[Azure Automation での変数アセット](shared-resources/variables.md)」を参照してください。 資格情報の値の取得の詳細については、「[Azure Automation での資格情報資産](shared-resources/credentials.md)」を参照してください。

 ### <a name="dsc-configurations"></a>DSC の構成

Azure portal または Windows PowerShell の [Export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) コマンドレットを使用して、DSC の構成をスクリプト ファイルにエクスポートできます。 これらの構成を別の Automation アカウントにインポートして使用できます。

## <a name="geo-replication-in-azure-automation"></a>Azure Automation での geo レプリケーション

Azure Automation アカウントでは geo レプリケーションが標準です。 アカウントを設定するときに、プライマリ リージョンを選択します。 内部の Automation geo レプリケーション サービスによって、セカンダリ リージョンが自動的にアカウントに割り当てられます。 その後、サービスによって、プライマリ リージョンからセカンダリ リージョンにアカウント データが継続的にバックアップされます。 プライマリ リージョンとセカンダリ リージョンの完全な一覧については、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」をご覧ください。 

Automation の geo レプリケーション サービスによって作成されるバックアップは、Automation のアセットや構成などの完全なコピーです。 プライマリ リージョンがダウンしてデータが失われた場合、このバックアップを使用できます。 万一、プライマリ リージョンのデータが失われた場合、Microsoft によってその復旧が試みられます。 プライマリ データを復旧できない場合は、自動フェールオーバーが使用され、Azure サブスクリプションを通じて状況が通知されます。 

リージョンで障害が発生した場合、外部のお客様が Automation の geo レプリケーション サービスに直接アクセスすることはできません。 リージョンで障害が発生したときに Automation の構成と Runbook を維持した場合は、次のようにします。

1. プライマリ Automation アカウントの地理的リージョンとペアにするセカンダリ リージョンを選択します。

2. セカンダリ リージョンに Automation アカウントを作成します。

3. プライマリ アカウントで、Runbook をスクリプト ファイルとしてエクスポートします。

4. セカンダリ リージョンの Automation アカウントに Runbook をインポートします。

## <a name="next-steps"></a>次のステップ

* Azure Automation でのセキュリティ保護されたアセットの詳細については、「[Azure Automation でセキュリティで保護された資産を暗号化する](automation-secure-asset-encryption.md)」を参照してください。

* geo レプリケーションの詳細については、「[アクティブ geo レプリケーションの作成と使用](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)」を参照してください。