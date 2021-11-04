---
title: Hyperscale セカンダリ レプリカ
description: この記事では、Hyperscale サービス レベルで利用できる各種のセカンダリ レプリカについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.topic: overview
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 9/24/2021
ms.openlocfilehash: b87c8dcfab561a010019a71f59307bdc8943761d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439521"
---
# <a name="hyperscale-secondary-replicas"></a>Hyperscale セカンダリ レプリカ
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[分散機能アーキテクチャ](service-tier-hyperscale.md)に関するページで説明したように、Azure SQL Database Hyperscale には、2 種類のコンピューティング ノード (レプリカとも呼ばれます) があります。

- プライマリ: 読み取り操作と書き込み操作を処理
- セカンダリ: [読み取りスケールアウト](read-scale-out.md)、[高可用性](high-availability-sla.md)、[geo レプリケーション](active-geo-replication-overview.md)を提供

セカンダリ レプリカは常に読み取り専用で、次の 3 種類があります。

- 高可用性レプリカ
- 名前付きレプリカ ([プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))
- geo レプリカ ([プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/))

アーキテクチャ、機能セット、目的、コストは種類ごとに異なります。 必要な機能に応じて、1 つだけ使用することも、3 つすべてを組み合わせて使用することもできます。

## <a name="high-availability-replica"></a>高可用性レプリカ

高可用性 (HA) レプリカでは、プライマリ レプリカと同じページ サーバーが使用されるため、HA レプリカを追加するためにデータのコピーは必要ありません。 HA レプリカは主にデータベースの可用性を高めるために使用され、フェールオーバー用のホット スタンバイとして機能します。 プライマリ レプリカが利用不可の状態に陥った場合、既存の HA レプリカへのフェールオーバーが自動的にすばやく実行されます。 接続文字列が変化するとは限りません。フェールオーバー中、アクティブな接続が切断されることで、アプリケーションに最小限のダウンタイムが生じることがあります。 そのような場合は通常と同様、適切な再試行ロジックが推奨されます。 いくつかのドライバーには、ある程度の自動再試行ロジックが最初から用意されています。 .NET を使用している場合、構成可能な自動再試行ロジックが、[最新の Microsoft.Data.SqlClient](https://devblogs.microsoft.com/azure-sql/configurable-retry-logic-for-microsoft-data-sqlclient/) ライブラリでネイティブにフル サポートされています。

HA レプリカには、プライマリ レプリカと同じサーバー名とデータベース名が使用されます。 また、そのサービス レベル目標も常にプライマリ レプリカと同じです。 HA レプリカは、ポータルまたは任意の API からスタンドアロン リソースとして表示または管理することはできません。 

使用できる HA レプリカの数は 0 個から 4 個です。 この数は、データベースの作成中や作成後に、一般的な管理エンドポイントとツール (例: PowerShell、AZ CLI、Portal、REST API) を使用して変更できます。 HA レプリカを作成したり削除したりしても、プライマリ レプリカのアクティブな接続には影響しません。

### <a name="connecting-to-an-ha-replica"></a>HA レプリカへの接続

Hyperscale データベースでは、読み取り/書き込みプライマリ レプリカと読み取り専用の HA レプリカのどちらに接続がルーティングされるかが、クライアントで使用される接続文字列の `ApplicationIntent` 引数によって決まります。 `ApplicationIntent` が `ReadOnly` に設定されていても、データベースにセカンダリ レプリカがない場合、接続はプライマリ レプリカにルーティングされ、既定で `ReadWrite` の動作になります。

```csharp
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

すべての HA レプリカのリソース容量は同じです。 複数の HA レプリカが存在する場合、読み取りを目的としたワークロードは、使用可能なすべての HA レプリカに任意に分散されます。 HA レプリカが複数存在するとき、プライマリでのデータ変更に関する待ち時間はレプリカごとに異なる場合があることに注意してください。 各 HA レプリカでは、プライマリと同じ一連のページ サーバー上の同じデータが使用されます。 ただし、各 HA レプリカのローカル データ キャッシュには、トランザクション ログ サービスを介してプライマリで行われた変更が反映されます。トランザクション ログ サービスによって、ログ レコードがプライマリ レプリカから HA レプリカに転送されます。 ログ レコードが適用される速度は、HA レプリカで処理されているワークロードによって異なる場合があり、プライマリ レプリカを基準とするデータの待ち時間もレプリカごとに異なる結果となります。

## <a name="named-replica-in-preview"></a>名前付きレプリカ (プレビュー)

HA レプリカと同様、名前付きレプリカでも、プライマリ レプリカと同じページ サーバーが使用されます。 HA レプリカと同様、名前付きレプリカを追加するためにデータのコピーは必要ありません。 

名前付きレプリカが HA レプリカと異なるのは、次の点です。 

- ポータルおよび API (AZ CLI、PowerShell、T-SQL) 呼び出しからは、通常の (読み取り専用) Azure SQL データベースのように見えます。
- プライマリ レプリカとはデータベース名が異なる場合があります。また、(プライマリ レプリカと同じリージョン内であれば) 必要に応じて異なる論理サーバーに置くこともできます。
- 独自のサービス レベル目標があり、プライマリ レプリカとは無関係に設定したり変更したりできます。
- (プライマリ レプリカ 1 つにつき) 最大 30 個の名前付きレプリカがサポートされます。
- 名前付きレプリカのホストとなる論理サーバーに複数の異なるログインを作成することで、名前付きレプリカごとに異なる認証を使用できます。

そのため、名前付きレプリカには、HA レプリカと比較して、読み取り専用ワークロードに関するいくつかの利点があります。

- プライマリ レプリカがスケールアップまたはダウンされるとき、名前付きレプリカに接続されているユーザーが切断されることはありません。同時に、プライマリ レプリカに接続されているユーザーは、名前付きレプリカのスケールアップまたはダウンによる影響を受けません。
-   プライマリまたは名前付きのレプリカで実行されているワークロードは、他のレプリカで実行されている実行時間の長いクエリによる影響を受けません。

名前付きレプリカの主な目標は、大量の OLTP [読み取りスケールアウト](read-scale-out.md) シナリオへの対応と、Hybrid Transactional and Analytical Processing (HTAP) ワークロードの向上です。 こうしたソリューションを作成する方法の例を次に示します。

- [OLTP スケールアウト サンプル](https://github.com/Azure-Samples/azure-sql-db-named-replica-oltp-scaleout)
- [HTAP スケールアウト サンプル](https://github.com/Azure-Samples/azure-sql-db-named-replica-htap)

上に挙げた主なシナリオに加え、名前付きレプリカが備える柔軟性とエラスティック性は、さまざまなユース ケースに対応します。
- [アクセスの分離](hyperscale-named-replica-security-configure.md): 特定の名前付きレプリカへのアクセスは許可できますが、プライマリ レプリカやその他の名前付きレプリカへのアクセスは許可できません。
- ワークロードに依存するサービス レベル目標: 名前付きレプリカにはそれぞれ固有のサービス レベル目標を設定できるため、ワークロードやユース ケースごとに異なる名前付きレプリカを使用できます。 たとえば、一方の名前付きレプリカは Power BI 要求の処理に使用し、もう一方の名前付きレプリカは、Apache Spark for Data Science タスクにデータを提供する目的に使用することができます。 それぞれに独立したサービス レベル目標を設定し、個別にスケーリングできます。
- ワークロードに依存するルーティング: 名前付きレプリカは最大 30 個使用できるため、複数の名前付きレプリカをグループ化し、グループごとに用途を分けることができます。 たとえば、モバイル アプリケーションから送信される要求の処理には、4 つの名前付きレプリカから成るグループを使用し、Web アプリケーションから送信される要求の処理には、2 つの名前付きレプリカから成るもう 1 つのグループを使用できます。 このアプローチにより、各グループのパフォーマンスとコストを細かな粒度でチューニングすることができます。

次の例では、データベース `WideWorldImporters` の名前付きレプリカ `WideWorldImporters_NR` が作成されます。 プライマリ レプリカでは、サービス レベル目標 HS_Gen5_4 が使用されますが、名前付きレプリカでは HS_Gen5_2 が使用されます。 どちらも同じ論理サーバー `MyServer` を使用します。 REST API を直接使用した方が望ましい場合は、そのような方法もあります。[名前付きレプリカ セカンダリとしてデータベースを作成する](/rest/api/sql/2020-11-01-preview/databases/createorupdate#creates-a-database-as-named-replica-secondary)方法に関するページを参照してください。

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters]
ADD SECONDARY ON SERVER [MyServer] 
WITH (SERVICE_OBJECTIVE = 'HS_Gen5_2', SECONDARY_TYPE = Named, DATABASE_NAME = [WideWorldImporters_NR]);
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
New-AzSqlDatabaseSecondary -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters" -PartnerResourceGroupName "MyResourceGroup" -PartnerServerName "MyServer" -PartnerDatabaseName "WideWorldImporters_NR" -SecondaryType Named -SecondaryServiceObjectiveName HS_Gen5_2
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyServer --secondary-type named --partner-database WideWorldImporters_NR --partner-server MyServer --service-objective HS_Gen5_2
```

---

名前付きレプリカは、データ移動が伴わないので、ほとんどの場合、ほんの一瞬で作成されます。 名前付きレプリカが利用可能になると、ポータルやコマンドライン ツール (AZ CLI、PowerShell など) から見えるようになります。 名前付きレプリカは、通常の読み取り専用データベースとして利用できます。 

> [!NOTE]
> Hyperscale の名前付きレプリカについてよく寄せられる質問については、「[Azure SQL Database Hyperscale 名前付きレプリカに関する FAQ](service-tier-hyperscale-named-replicas-faq.yml)」を参照してください。

### <a name="connecting-to-a-named-replica"></a>名前付きレプリカに接続する

名前付きレプリカに接続するには、そのサーバー名とデータベース名を参照して、その名前付きレプリカの接続文字列を使用する必要があります。 名前付きレプリカは常に読み取り専用であるため、"ApplicationIntent=ReadOnly" オプションを指定する必要はありません。

プライマリ、HA、名前付きレプリカは、同じ一連のページ サーバー上の同じデータを共有しますが、個々の名前付きレプリカのデータ キャッシュは、HA レプリカの場合と同様、トランザクション ログ サービスが、プライマリから名前付きレプリカにログ レコードを転送することで、プライマリと同期された状態に保たれます。 ログ レコードが適用される速度は、名前付きレプリカで処理されているワークロードによって異なる場合があり、プライマリ レプリカを基準とするデータの待ち時間もレプリカごとに異なる結果となります。

### <a name="modifying-a-named-replica"></a>名前付きレプリカに変更を加える

名前付きレプリカのサービス レベル目標は、その作成時に、`ALTER DATABASE` コマンドのほか、サポートされているあらゆる手段 (AZ CLI、PowerShell、REST API) を用いて定義できます。 名前付きレプリカの作成後にサービス レベル目標を変更する必要が生じた場合は、その名前付きレプリカ自体に対して `ALTER DATABASE ... MODIFY` コマンドを使用して変更できます。 たとえば、`WideWorldImporters_NR` が `WideWorldImporters` データベースの名前付きレプリカである場合は、次のコマンドを実行します。 

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
ALTER DATABASE [WideWorldImporters_NR] MODIFY (SERVICE_OBJECTIVE = 'HS_Gen5_4')
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Set-AzSqlDatabase -ResourceGroup "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR" -RequestedServiceObjectiveName "HS_Gen5_4"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db update -g MyResourceGroup -s MyServer -n WideWorldImporters_NR --service-objective HS_Gen5_4
```

---

### <a name="removing-a-named-replica"></a>名前付きレプリカを削除する

名前付きレプリカを削除するには、通常のデータベースと同様にドロップします。 ドロップする名前付きレプリカがあるサーバーの `master` データベースに接続されていることを確認してから、次のコマンドを使用します。

# <a name="t-sql"></a>[T-SQL](#tab/tsql)
```sql
DROP DATABASE [WideWorldImporters_NR];
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Remove-AzSqlDatabase -ResourceGroupName "MyResourceGroup" -ServerName "MyServer" -DatabaseName "WideWorldImporters_NR"
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az sql db delete -g MyResourceGroup -s MyServer -n WideWorldImporters_NR
```
---

> [!IMPORTANT]
> 名前付きレプリカの作成元となったプライマリ レプリカが削除されると、その名前付きレプリカは自動的に削除されます。

### <a name="known-issues"></a>既知の問題

#### <a name="partially-incorrect-data-returned-from-sysdatabases"></a>sys.databases から一部間違ったデータが返される
パブリック プレビュー段階では、名前付きレプリカの `sys.databases` から返される行の、`name` 列と `database_id` 列以外の値が矛盾していたり間違っていたりする場合があります。 たとえば、名前付きレプリカの作成元となったプライマリ データベースが 150 に設定されているにもかかわらず、その名前付きレプリカの `compatibility_level` 列が 140 とレポートされる場合があります。 これを回避するために、同じデータは可能な限り、`DATABASEPROPERTYEX()` 関数を使用して取得してください。これにより正しいデータが返されます。

## <a name="geo-replica-in-preview"></a>geo レプリカ (プレビュー)

[アクティブ geo レプリケーション](active-geo-replication-overview.md)を使用すると、プライマリ Hyperscale データベースの読み取り可能なセカンダリ レプリカを同じまたは異なる Azure リージョンに作成できます。 geo レプリカは、異なる論理サーバー上に作成する必要があります。 geo レプリカのデータベース名は常にプライマリのデータベース名と一致します。

geo レプリカを作成すると、プライマリのすべてのデータが、別の一連のページ サーバーにコピーされます。 geo レプリカとプライマリはページ サーバーを共有しません。これは両者が同じリージョンに存在していても同様です。 このアーキテクチャによって、geo フェールオーバーに必要な冗長性が確保されます。

geo レプリカは、非同期レプリケーションを介してデータベースのトランザクション上一貫性のあるコピーを維持するために使用されます。 geo レプリカが別の Azure リージョンにある場合は、プライマリ リージョンで災害や障害が発生した場合に、ディザスター リカバリーに使用できます。 geo レプリカは、地理的な読み取りスケールアウトのシナリオにも使用できます。

Hyperscale では、geo フェールオーバーを手動で開始する必要があります。 フェールオーバー後、新しいプライマリには別の接続エンド ポイントが割り当てられ、新しいプライマリ レプリカのホストとなる論理サーバーの名前が参照されます。 詳細については、[アクティブ geo レプリケーション](active-geo-replication-overview.md)に関するページを参照してください。

Hyperscale データベースの geo レプリケーションは現在プレビュー段階であり、次の制限があります。
- 作成できる geo レプリカは 1 つだけです (リージョンは同じでも、異なっていてもかまいません)。
- フェールオーバー グループはサポートされません。 
- 計画フェールオーバーはサポートされません。
- geo レプリカのポイントインタイム リストアはサポートされていません。
- geo レプリカのデータベース コピーの作成はサポートされません。 
- セカンダリのセカンダリ ("geo レプリカのチェーン") はサポートされません。 

## <a name="next-steps"></a>次のステップ

- [ハイパースケール サービス レベル](service-tier-hyperscale.md)
- [アクティブ geo レプリケーション](active-geo-replication-overview.md)
- [Azure SQL Database Hyperscale 名前付きレプリカへの分離アクセスが可能となるようにセキュリティを構成する](hyperscale-named-replica-security-configure.md)
- [Azure SQL Database Hyperscale 名前付きレプリカに関する FAQ](service-tier-hyperscale-named-replicas-faq.yml)
