---
title: Durable Functions ストレージ プロバイダー - Azure
description: Durable Functions 用のさまざまなストレージ プロバイダーと、それらの比較方法について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 05/05/2021
ms.author: azfuncdf
ms.openlocfilehash: bbeb730b0e22bd555f06514870448a229fccfbc6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438103"
---
# <a name="durable-functions-storage-providers"></a>Durable Functions ストレージ プロバイダー

Durable Functions は、信頼性の高い実行を実現するために、関数パラメーター、戻り値、その他の状態を永続ストレージに自動的に保存します。 Durable Functions の既定の構成では、このランタイム状態は Azure Storage (クラシック) アカウントに保存されます。 ただし、代替の永続ストレージ プロバイダーを使用するように Durable Functions v2.0 以上を構成できます。

Durable Functions は、内部で [Durable Task Framework](https://github.com/Azure/durabletask) (DTFx) によって支援される一連の Azure Functions トリガーおよびバインディングです。 DTFx では、Durable Functions によって使用される Azure Storage プロバイダーなど、さまざまなバックエンド ストレージ プロバイダーがサポートされています。 Durable Functions **v2.5.0** より、ユーザーはその関数アプリを、Azure Storage プロバイダー以外の DTFx ストレージ プロバイダーを使用するように構成できます。

> [!NOTE]
> Azure Storage 以外のストレージ プロバイダーの使用を選択する場合、慎重に行う必要があります。 Azure で実行されるほとんどの関数アプリでは、Durable Functions に対して既定の Azure Storage プロバイダーを使用する必要があります。 ただし、代替のストレージ プロバイダーを使用するかどうか決定するとき、考慮する必要がある重要なコスト、スケーラビリティ、データ管理のトレードオフがあります。 この記事では、これらの多くのトレードオフについて詳しく説明します。
>
> また現在、データをあるストレージ プロバイダーから別のストレージ プロバイダーに移行できないことにも注意してください。 新しいストレージ プロバイダーを使用する場合は、その新しいストレージ プロバイダーで構成された新しいアプリを作成する必要があります。

Durable Functions での使用のために、2 つの代替 DTFx ストレージ プロバイダー (_Netherite_ ストレージ プロバイダーと _Microsoft SQL Server (MSSQL)_ ストレージ プロバイダー) が開発されました。 この記事では、サポートされる 3 つすべてのプロバイダーについて説明し、それらを比較します。また、それらの使用を開始する方法について基本的な情報を提供します。

## <a name="azure-storage"></a>Azure Storage

Azure Storage は、Durable Functions の既定のストレージ プロバイダーです。 これにより、キュー、テーブル、BLOB が使用され、オーケストレーションとエンティティの状態が保持されます。 また、パーティション管理のために BLOB と BLOB のリースが使用されます。 多くの場合、Durable Functions のランタイム状態の保存に使用されるストレージ アカウントは、Azure Functions (`AzureWebJobsStorage`) で使用される既定のストレージ アカウントと同じです。 ただし、別のストレージ アカウントを使用して Durable Functions を構成することもできます。 Azure Storage プロバイダーは、Durable Functions 拡張機能に組み込まれており、他の依存関係はありません。

Azure Storage プロバイダーの主な利点を次に示します。

* セットアップが不要 - 関数アプリのセットアップ エクスペリエンスによって作成されたストレージ アカウントを使用できます。
* 最もコストの低いサーバーレス課金モデル - Azure Storage には、使用量に完全に基づいた使用量ベースの価格モデルがあります ([詳細情報](durable-functions-billing.md#azure-storage-transactions))。
* 最適なツールのサポート - Azure Storage では、クロスプラットフォーム ローカル エミュレーションが提供され、Visual Studio、Visual Studio Code、Azure Functions Core Tools と統合されます。
* 最高の成熟度 - Azure Storage は、Durable Functions 用のオリジナルのストレージ バックエンドであり、最も厳しいテストを通過しています。

Azure Storage ストレージ プロバイダーの DTFx コンポーネントのソース コードは、[Azure/durabletask](https://github.com/Azure/durabletask/tree/main/src/DurableTask.AzureStorage) GitHub リポジトリで確認できます。

> [!NOTE]
> Azure Storage プロバイダーを使用する場合、標準の汎用 Azure Storage アカウントが必要です。 他のストレージ アカウント タイプはどれもサポートされていません。 従来の v1 汎用ストレージ アカウントを使用することを強くお勧めします。これは、新しい v2 ストレージ アカウントでは、Durable Functions のワークロードに対して料金が大幅に高額になる可能性があるためです。 Azure Storage アカウントの種類について詳しくは、「[ストレージ アカウントの概要](../../storage/common/storage-account-overview.md)」ドキュメントを参照してください。

## <a name="netherite-preview"></a><a name="netherite"></a>Netherite (プレビュー)

Netherite ストレージ バックエンドは、[Microsoft Research](https://www.microsoft.com/research) によって設計および開発されました。 これは、[Azure ページ BLOB](../../storage/blobs/storage-blob-pageblob-overview.md) 上で [Azure Event Hubs](../../event-hubs/event-hubs-about.md) と [FASTER](https://www.microsoft.com/research/project/faster/) データベース テクノロジを使用します。 Netherite の設計により、他のプロバイダーと比較して、オーケストレーションとエンティティのスループット処理を大幅に向上できます。 一部のベンチマーク シナリオでは、既定の Azure Storage プロバイダーと比較して、桁違いのスループットが記録されました。

Netherite ストレージ プロバイダーの主な利点を次に示します。

* 他のストレージ プロバイダーと比較して、スループットは大幅に高く、コストは低いです。
* 価格 - パフォーマンスの最適化がサポートされ、必要に応じてパフォーマンスをスケールアップできます。
* Event Hubs Basic と Standard SKU を使用した最大 32 のデータ パーティションがサポートされます。
* 高スループットのワークロードに対して、他のプロバイダーよりもコスト効率に優れています。

Netherite ストレージ プロバイダーの使用を開始する方法など、技術的な詳細については、[Netherite のドキュメント](https://microsoft.github.io/durabletask-netherite)を参照してください。 Netherite ストレージ プロバイダーのソース コードは、[microsoft/durabletask-netherite](https://github.com/microsoft/durabletask-netherite) GitHub リポジトリで確認できます。 Netherite ストレージ プロバイダーの詳細な評価については、研究論文「[Serverless Workflows with Durable Functions and Netherite](https://arxiv.org/abs/2103.00033)」を参照してください。

> [!NOTE]
> _Netherite_ という名前は、[Minecraft](https://minecraft.fandom.com/wiki/Netherite) の世界が元になっています。

## <a name="microsoft-sql-server-mssql-preview"></a><a name="mssql"></a>Microsoft SQL Server (MSSQL) (プレビュー)

Microsoft SQL Server (MSSQL) ストレージ プロバイダーは、すべての状態を Microsoft SQL Server データベースに保存します。 これは、SQL Server のオンプレミスとクラウドホステッドの両方のデプロイ ([Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) を含む) と互換性があります。

MSSQL ストレージ プロバイダーの主な利点を次に示します。

* 切断された環境をサポート - SQL Server インストールを使用する場合、Azure 接続は必要ありません。
* Azure ホステッド、オンプレミスなど、複数の環境およびクラウド間で移植可能です。
* 厳密なデータの整合性により、データ損失なしでバックアップ/復元およびフェイルオーバーを実行できます。
* カスタム データ暗号化 (SQL Server の機能) がネイティブ サポートされます。
* 組み込みストアド プロシージャを介して、既存のデータベース アプリケーションと統合されます。

MSSQL ストレージ プロバイダーの使用を開始する方法など、技術的な詳細については、[Microsoft SQL プロバイダーのドキュメント](https://microsoft.github.io/durabletask-mssql)を参照してください。 MSSQL ストレージ プロバイダーのソース コードは、[microsoft/durabletask-mssql](https://github.com/microsoft/durabletask-mssql) GitHub リポジトリで確認できます。

## <a name="configuring-alternate-storage-providers"></a>代替ストレージ プロバイダーの構成

代替ストレージ プロバイダーの構成は、通常、2 段階のプロセスです。

1. 適切な NuGet パッケージを関数アプリに追加します (この要件は、拡張機能バンドルを使用するアプリでは一時的な要件です)。
1. **host.json** ファイルを更新して、使用するストレージ プロバイダーを指定します。

host.json でストレージ プロバイダーが明示的に構成されていない場合、既定では Azure Storage プロバイダーが有効になります。

### <a name="configuring-the-azure-storage-provider"></a>Azure Storage プロバイダーの構成

Azure Storageプロバイダーは既定のストレージ プロバイダーであるため、明示的な構成、NuGet パッケージ参照、または拡張機能バンドル参照は不要です。 [こちら](durable-functions-bindings.md#hostjson-settings)の `extensions/durableTask/storageProvider` パスで、**host.json** 構成オプションの全セットを確認できます。

### <a name="configuring-the-netherite-storage-provider"></a>Netherite ストレージ プロバイダーの構成

Netherite ストレージ プロバイダーを使用するには、最初に、**csproj** ファイル (.NET アプリ) または **extensions.proj** ファイル (JavaScript、Python、PowerShell アプリ) で、[Microsoft.Azure.DurableTask.Netherite.AzureFunctions](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Netherite.AzureFunctions) NuGet パッケージへの参照を追加する必要があります。

> [!NOTE]
> Netherite ストレージ プロバイダーは、[拡張機能バンドル](../functions-bindings-register.md#extension-bundles)を使用するアプリではまだサポートされていません。

次の host.json の例は、Netherite ストレージ プロバイダーを有効にするために必要な最小構成を示しています。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "Netherite",
        "storageConnectionName": "AzureWebJobsStorage",
        "eventHubsConnectionName": "EventHubsConnection"
      }
    }
  }
}
```

詳細なセットアップ手順については、[Netherite の使用開始に関するドキュメント](https://microsoft.github.io/durabletask-netherite/#/?id=getting-started)を参照してください。

### <a name="configuring-the-mssql-storage-provider"></a>MSSQL ストレージ プロバイダーの構成

MSSQL ストレージ プロバイダーを使用するには、最初に、**csproj** ファイル (.NET アプリ) または **extensions.proj** ファイル (JavaScript、Python、PowerShell アプリ) で、[Microsoft.DurableTask.SqlServer.AzureFunctions](https://www.nuget.org/packages/Microsoft.DurableTask.SqlServer.AzureFunctions) NuGet パッケージへの参照を追加する必要があります。

> [!NOTE]
> MSSQL ストレージ プロバイダーは、[拡張機能バンドル](../functions-bindings-register.md#extension-bundles)を使用するアプリではまだサポートされていません。

次の例は、MSSQL ストレージ プロバイダーを有効にするために必要な最小構成を示しています。

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "type": "mssql",
        "connectionStringName": "SQLDB_Connection"
      }
    }
  }
}
```

詳細なセットアップ手順については、[MSSQL プロバイダーの使用開始に関するドキュメント](https://microsoft.github.io/durabletask-mssql/#/quickstart)を参照してください。

## <a name="comparing-storage-providers"></a>ストレージ プロバイダーの比較

サポートされている各種のストレージ プロバイダー間には、重要なトレードオフが多数あります。 次の表を使用すると、これらのトレードオフを理解でき、ニーズに最も合うストレージ プロバイダーを判別できます。

| ストレージ プロバイダー | Azure Storage | Netherite | MSSQL |
|- |-              |-          |-      |
| 公式サポートの状況 | ✅ 一般提供 (GA) | ⚠ パブリック プレビュー | ⚠ パブリック プレビュー |
| 外部依存関係 | Azure Storage アカウント (汎用 v1) | Azure Event Hubs<br/>Azure Storage アカウント (汎用) | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) または Azure SQL Database |
| ローカル開発とエミュレーションのオプション | [Azurite v3.12+](../../storage/common/storage-use-azurite.md) (クロス プラットフォーム)<br/>[Azure Storage Emulator](../../storage/common/storage-use-emulator.md) (Windows のみ) | メモリ内エミュレーション ([詳細情報](https://microsoft.github.io/durabletask-netherite/#/emulation)) | SQL Server Developer エディション ([Windows](/sql/database-engine/install-windows/install-sql-server)、[Linux](/sql/linux/sql-server-linux-setup)、[Docker のコンテナー](/sql/linux/sql-server-linux-docker-container-deployment)をサポート) |
| タスク ハブの構成 | 明示 | 明示 | 既定で暗黙 ([詳細情報](https://microsoft.github.io/durabletask-mssql/#/taskhubs)) |
| 最大スループット | 中 | 非常に高 | 中 |
| 最大オーケストレーション/エンティティ スケールアウト (ノード数) | 16 | 32 | 該当なし |
| 最大アクティビティ スケールアウト (ノード数) | 該当なし | 32 | 該当なし |
| 従量課金プランのサポート | ✅ 完全サポート | ❌ サポート対象外 | ❌ サポート対象外 |
| Elastic Premium プランのサポート | ✅ 完全サポート | ⚠ [ランタイム スケールの監視](../functions-networking-options.md#premium-plan-with-virtual-network-triggers)が必要 | ⚠ [ランタイム スケールの監視](../functions-networking-options.md#premium-plan-with-virtual-network-triggers)が必要 |
| [KEDA 2.0](https://keda.sh/) スケーリングのサポート<br/>([詳細情報](../functions-kubernetes-keda.md)) | ❌ サポート対象外 | ❌ サポート対象外 | ✅ [MSSQL スケーラー](https://keda.sh/docs/scalers/mssql/)を使用する場合にサポート対象 ([詳細情報](https://microsoft.github.io/durabletask-mssql/#/scaling)) |
| [拡張機能バンドルのサポート](../functions-bindings-register.md#extension-bundles) (非 .NET アプリで推奨) | ✅ 完全サポート | ❌ サポート対象外 | ❌ サポート対象外 |
| 価格 - パフォーマンスを構成できるか? | ❌ いいえ | ✅ はい (Event Hubs TU と CU) | ✅ はい (SQL vCPU) |
| 切断された環境のサポート | ❌ Azure 接続が必要 | ❌ Azure 接続が必要 | ✅ 完全サポート |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Durable Functions のパフォーマンスとスケールについて学習する](durable-functions-perf-and-scale.md)
