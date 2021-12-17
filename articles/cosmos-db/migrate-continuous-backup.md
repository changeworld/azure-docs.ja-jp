---
title: Azure Cosmos DB アカウントを定期バックアップ モードから継続的バックアップ モードに移行する
description: Azure Cosmos DB では現在、定期モードから継続モードへの一方向の移行をサポートしており、この移行は元に戻すことはできません。 定期モードから継続モードに移行すると、継続的モードのベネフィットを活用できます。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 3ac1ae98dfa920a4b4060905e99b7378fcdb3414
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476302"
---
# <a name="migrate-an-azure-cosmos-db-account-from-periodic-to-continuous-backup-mode"></a>Azure Cosmos DB アカウントを定期バックアップ モードから継続的バックアップ モードに移行する
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

定期モードのバックアップ ポリシーを持つ Azure CosmosDB アカウントは、[Azure portal](#portal)、[CLI](#cli)、[PowerShell](#powershell)、または [Resource Manager テンプレート](#ARM-template)を使用して継続モードに移行できます。 定期モードから継続モードへの移行は一方向の移行であり、元に戻すことはできません。 定期モードから継続モードに移行すると、継続的モードのベネフィットを活用できます。

継続モードに移行する主な理由は次のとおりです。

* Azure portal、CLI、または PowerShell を使用してセルフサービス復元を実行できます。
* 過去 30 日以内の日時を秒単位で指定して復元を実行できます。
* ある期間内のシャードまたはパーティション キー範囲全体でバックアップに一貫性があることを確認できます。
* コンテナー、データベース、またはアカウント全体が削除または変更されたときにそれらを復元できます。
* コンテナー、データベース、またはアカウントのイベントを選択して、いつ復元を開始するのかを決定できます。

> [!NOTE]
> 移行機能は一方向のみであり、元に戻すことができない操作です。 つまり、定義モードから継続モードに移行した後、定期モードに戻すことはできません。
>
> 次の条件に該当する場合にのみ、アカウントを継続的バックアップ モードに移行できます。 アカウントを移行する前に、[ポイントインタイム リストアの制限事項](continuous-backup-restore-introduction.md#current-limitations)も確認してください。
>
> * アカウントの種類が SQL API または MongoDB の API の場合。
> * アカウントに単一の書き込みリージョンがある場合。
> * アカウントがカスタマー マネージド キー (CMK) で有効になっていない場合。
> * 分析ストアでアカウントが有効になっていない場合。

## <a name="permissions"></a>アクセス許可

移行を実行するには、移行するアカウントに対する `Microsoft.DocumentDB/databaseAccounts/write` アクセス許可が必要です。  

## <a name="pricing-after-migration"></a>移行後の価格

アカウントを継続的バックアップモードに移行した後、このモードのコストは、定期バックアップ モードに比べると異なります。 継続モードのバックアップ コストは、定期モードよりもずっと安価です。 詳細については、[継続的バックアップ モードの価格](continuous-backup-restore-introduction.md#continuous-backup-pricing)の例を参照してください。

## <a name="migrate-using-portal"></a><a id="portal"></a> ポータルを使用した移行

アカウントを定期バックアップから継続的バックアップ モードに移行するには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[機能]** ペインを開きます。 **[Continuous Backup]\(継続的バックアップ\)** を選択し、 **[Enable]\(有効化\)** を選択します。

   :::image type="content" source="./media/migrate-continuous-backup/enable-backup-migration.png" alt-text="Azure portal を使用して継続モードに移行する" lightbox="./media/migrate-continuous-backup/enable-backup-migration.png":::

1. 移行が進行中の場合、状態は **[Pending]\(保留中\)** と表示されます。 完了後、状態が **[オン]** に変わります。 移行にかかる時間は、アカウントのデータのサイズによって異なります。

   :::image type="content" source="./media/migrate-continuous-backup/migration-status.png" alt-text="Azure portal から移行の状態を確認する" lightbox="./media/migrate-continuous-backup/migration-status.png":::

## <a name="migrate-using-powershell"></a><a id="powershell"></a>PowerShell を使用して移行する

[PowerShell の最新バージョン](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true)または 6.2.0 より上位のバージョンをインストールします。 次に、以下の手順を実行します。

1. Azure アカウントに接続します。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

1. アカウントを定期バックアップ モードから継続的バックアップ モードに移行します。

   ```azurepowershell-interactive
   Update-AzCosmosDBAccount ` 
     -ResourceGroupName "myrg" ` 
     -Name "myAccount" `
     -BackupPolicyType Continuous
   ```

### <a name="check-the-migration-status"></a>移行の状態を確認する

**backupPolicy** オブジェクトの **status** または **targetType** プロパティを確認するには、次のコマンドを実行します。 移行を開始すると、以下のようにステータスは進行中となります。

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-started-powershell.png" alt-text="PowerShell コマンドを使用して移行の状態を確認する":::

移行が完了すると、バックアップの種類が **Continuous** に変わります。 同じコマンドを再度実行して、状態を確認します。

```azurepowershell-interactive
az cosmosdb show -n "myAccount" -g "myrg"
```

:::image type="content" source="./media/migrate-continuous-backup/migration-status-complete-powershell.png" alt-text="移行が完了すると、バックアップの種類が continuous に変更される":::

## <a name="migrate-using-cli"></a><a id="cli"></a>CLI を使用した移行

1. Azure CLI の最新バージョンをインストールします。

   * CLI がない場合は、Azure CLI の最新バージョンまたは 2.26.0 より上位のバージョンを[インストール](/cli/azure/)します。
   * 既に Azure CLI がインストールされている場合は、`az upgrade` コマンドを使用して最新バージョンにアップグレードします。
   * ユーザーは Azure portal から Cloud Shell を使用することもできます。

1. Azure アカウントにログインし、次のコマンドを実行してアカウントを継続モードに移行します。

   ```azurecli-interactive
   az login

   az cosmosdb update -n <myaccount> -g <myresourcegroup> --backup-policy-type continuous
   ```

1. 移行が正常に完了すると、backupPolicy オブジェクトの type プロパティが Continuous に設定されていることが出力に示されます。

   ```console
    {
      "apiProperties": null,
      "backupPolicy": {
        "type": "Continuous"
      },
      "capabilities": [],
      "connectorOffer": null,
      "consistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxIntervalInSeconds": 5,
        "maxStalenessPrefix": 100
      },
    …
    }
   ```

## <a name="migrate-using-resource-manager-template"></a><a id="ARM-template"></a> Resource Manager テンプレートを使用して移行する

ARM テンプレートを使用して継続的バックアップ モードに移行するには、テンプレートの backupPolicy セクションを見つけて、`type` プロパティを更新します。 たとえば、既存のテンプレートに次の JSON オブジェクトのようなバックアップ ポリシーがあるとします。

```json
"backupPolicy": {
   "type": "Periodic",
   "periodicModeProperties": {
   "backupIntervalInMinutes": 240,
   "backupRetentionIntervalInHours": 8
   }
},
```

これを次の JSON オブジェクトに置き換えます。

```json
"backupPolicy": {
   "type": "Continuous"
},
```

次に、Azure PowerShell または CLI を使用してテンプレートをデプロイします。 次の例は、CLI コマンドを使用してテンプレートをデプロイする方法を示しています。

```azurecli
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="what-to-expect-during-and-after-migration"></a>移行中と移行後に予期されること

定期モードから継続モードに移行するとき、アカウント レベルの更新または削除を実行するコントロール プレーン操作は実行できません。 たとえば、リージョンの追加または削除、アカウントのフェールオーバー、バックアップ ポリシーの更新などの操作は、移行の進行中は実行できません。 移行にかかる時間は、アカウントのデータのサイズとリージョンの数によって異なります。 移行したアカウントの復元操作は、移行が正常に完了するまでは成功しません。

移行が完了した後にアカウントを復元することができます。 移行が太平洋標準時の午後 1 時に完了する場合、太平洋標準時の午後 1 時からポイントインタイム リストアを実行できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="does-the-migration-only-happen-at-the-account-level"></a>移行はアカウント レベルでしか行われないのですか?
はい。

#### <a name="which-accounts-can-be-targeted-for-backup-migration"></a>どのアカウントをバックアップ移行のターゲットにすることができますか?
現時点で、移行をサポートしているのは、書き込みリージョンが 1 つで、スループットが共有、プロビジョニング、自動スケーリング プロビジョニングのいずれかである SQL API アカウントおよび API for MongoDB アカウントです。

分析ストレージ、複数の書き込みリージョン、カスタマー マネージド キー (CMK) が有効になっているアカウントは、移行に関してサポートされていません。

#### <a name="does-the-migration-take-time-what-is-the-typical-time"></a>移行には時間がかかりますか? 通常はどのくらいかかりますか?
移行には時間がかかり、その時間は、アカウントのデータのサイズとリージョンの数によって異なります。 移行の状態は Azure CLI または PowerShell コマンドを使用して取得できます。 数十テラバイトのデータがある大規模なアカウントの場合、移行が完了するまで最大で数日かかる可能性があります。

#### <a name="does-the-migration-cause-any-availability-impactdowntime"></a>移行による可用性への影響はありますか? ダウンタイムは発生しますか?
いいえ。移行操作はバックグラウンドで実行されるため、クライアントの要求は影響を受けません。 ただし、移行中はいくつかのバックエンド操作を実行する必要があり、アカウントの負荷が高い場合は所要時間が延びることがあります。

#### <a name="what-happens-if-the-migration-fails-will-i-still-get-the-periodic-backups-or-get-the-continuous-backups"></a>移行が失敗したらどうなりますか? 定期バックアップのままですか? それとも継続的バックアップに切り替わるのですか?
移行プロセスが開始すると、アカウントは継続モードへの移行を開始します。  移行が失敗した場合、成功するまでもう一度移行を開始する必要があります。

#### <a name="how-do-i-perform-a-restore-to-a-timestamp-beforeduringafter-the-migration"></a>移行前、移行中、移行後にタイムスタンプへの復元を実行するにはどうすればよいですか?
t1 に移行を開始して t5 に終了すると仮定した場合、t1 から t5 までの間は復元タイムスタンプを使用できません。

アカウントが継続モードになった t5 よりも後の時点に復元する場合は、継続的アカウントで通常行うように、Azure portal、CLI、または PowerShell を使用して復元を実行できます。 このセルフサービス復元要求は、移行が完了した後でないと実行できません。

t1 より前の時点に復元する場合は、定期バックアップ アカウントで通常行うように、サポート チケットを開くことができます。 移行後、最大 30 日間は定期的な復元を実行できます。  この 30 日の間は、移行前のアカウントのバックアップ保持/間隔に基づいて復元を実行できます。  たとえば、1 時間の間隔で 24 個のコピーを保持するバックアップ構成であった場合、t1 の 24 時間前から t1 までの任意の時点に復元できます。

#### <a name="which-account-level-control-plane-operations-are-blocked-during-migration"></a>移行中にブロックされるアカウント レベルのコントロール プレーン操作はどれですか?
リージョンの追加または削除、フェールオーバー、バックアップ ポリシーの変更、スループットの変更など、結果としてデータが移動される操作は、移行中、ブロックされます。

#### <a name="if-the-migration-fails-for-some-underlying-issue-would-it-still-block-the-control-plane-operation-until-it-is-retried-and-completed-successfully"></a>なんらかの根本的な問題により移行が失敗した場合、再試行して正常に完了するまで、コントロール プレーン操作はブロックされますか?
移行に失敗しても、コントロール プレーン操作がブロックされることはありません。 移行に失敗した場合は、成功するまで再試行してから、他のコントロール プレーン操作を実行することをお勧めします。

#### <a name="is-it-possible-to-cancel-the-migration"></a>移行をキャンセルすることはできますか?
移行は、元に戻すことができない操作のため、キャンセルすることはできません。

#### <a name="is-there-a-tool-that-can-help-estimate-migration-time-based-on-the-data-usage-and-number-of-regions"></a>データの使用量とリージョンの数に基づいて移行時間を見積もる際に役立つツールはありますか?
時間を見積もるためのツールはありません。 ただし、今回の規模での実行により、単一のリージョンに 1 TB のデータがある場合は約 1 時間半かかることが示されています。

複数リージョンのアカウントの場合は、データの合計サイズを `Number_of_regions * Data_in_single_region` として計算します。

#### <a name="since-the-continuous-backup-mode-is-now-ga-would-you-still-recommend-restoring-a-copy-of-your-account-and-try-migration-on-the-copy-before-deciding-to-migrate-the-production-account"></a>継続的バックアップ モードの一般提供が開始されましたが、引き続き、実稼働アカウントの移行を決定する前にアカウントのコピーを復元し、そのコピーで移行を試すことを推奨しますか?
実稼働アカウントを移行する前に、継続的バックアップ モードの機能をテストして、予想どおりに動作することを確認することをお勧めします。 移行は一方向の操作であり、元に戻すことができないためです。

## <a name="next-steps"></a>次の手順

継続的バックアップ モードの詳細については、次の記事を参照してください。

* [ポイントインタイム リストアを使用した継続的バックアップ モードの概要](continuous-backup-restore-introduction.md)

* [継続的バックアップ モードのリソース モデル。](continuous-backup-restore-resource-model.md)

* [Azure portal](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli)、または [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template) を使用してアカウントを復元します。

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか?
   * 知っていることが既存のデータベース クラスター内の仮想コアとサーバーの数のみである場合は、[仮想コアまたは仮想 CPU の数を使用した要求ユニットの見積もり](convert-vcore-to-request-unit.md)に関するページを参照してください 
   * 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください
