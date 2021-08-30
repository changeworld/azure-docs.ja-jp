---
title: 共有プライベート リンク リソースのトラブルシューティング
titleSuffix: Azure Cognitive Search
description: 共有プライベート リンク リソースを管理するときの一般的な問題に関するトラブルシューティング ガイド。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/30/2021
ms.openlocfilehash: 81bae18cdc4a977ef03ddf807f9277037d939bd0
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195856"
---
# <a name="troubleshooting-common-issues-with-shared-private-link-resources"></a>共有プライベート リンク リソースに関する一般的な問題のトラブルシューティング

共有プライベート リンク リソースを使用すると、Azure Cognitive Search で安全な送信接続を行って、顧客のリソースにアクセスできます。 ただし、これらのリソースを管理 (作成、削除、または更新) するプロセスでは、いくつかの異なる種類のエラーが発生するおそれがあります。

## <a name="creating-a-shared-private-link-resource"></a>共有プライベート リンク リソースの作成

共有プライベート リンク リソースの作成には、次の 4 つの異なる手順が必要です。

1. 顧客は、Search リソース プロバイダー (RP) で管理プレーン [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) を呼び出し、作成する共有プライベート リンク リソースの詳細を指定します。

2. Search RP によって要求が検証され、検証できた場合は、非同期 Azure Resource Manager 操作が開始されます (顧客はその進行状況に対してクエリを実行できます)。

3. Search により、操作の完了に対してクエリが実行されます (通常は数分かかります)。 この時点で、共有プライベート リンク リソースのプロビジョニング状態は "更新中" になります。

4. 操作が正常に完了すると、プライベート エンドポイント (および DNS ゾーンとマッピング) が作成されます。 この時点で、顧客が共有プライベート リンク リソースの状態に対してクエリを実行すると、プロビジョニング状態は "成功" になります。

![共有プライベート リンク リソースの作成に必要な手順 ](media\troubleshoot-shared-private-link-resources\shared-private-link-states.png)

次に、作成フェーズ中に発生する一般的なエラーをいくつか示します。

### <a name="request-validation-failures"></a>要求の検証エラー

+ サポートされていない SKU: 共有プライベート リンク リソースは有料の SKU に対してのみ作成できます。Free レベルのサービスはサポートされていません。

+ 名前の検証: 共有プライベート リンクのリソース名は、特定の文字セットのみに制限されます。 リソース名に無効な文字が含まれている場合、リソースを作成する要求は受け入れられません。
共有プライベート リンク リソースに名前を付けるための規則は次のとおりです。
  
  + 長さは 1 から 60 文字で指定してください。
  + 英数字またはアンダースコア (_)、ピリオド (.)、ハイフン (-) のみを含める必要があります。

+ `groupId` の検証: 共有プライベート リンク リソースを作成する要求の一部として指定される `groupId` は、(スペルと、大文字と小文字の両方で) 次の表に一致する必要があります。

| Azure リソース | グループ ID | 利用可能な最初の API バージョン |
| --- | --- | --- |
| Azure Storage - BLOB (または) ADLS Gen 2 | `blob`| `2020-08-01` |
| Azure Storage - Tables | `table`| `2020-08-01` |
| Azure Cosmos DB - SQL API | `Sql`| `2020-08-01` |
| Azure SQL データベース | `sqlServer`| `2020-08-01` |
| Azure Database for MySQL (プレビュー) | `mysqlServer`| `2020-08-01-Preview` |
| Azure Key Vault | `vault` | `2020-08-01` |
| Azure Functions (プレビュー) | `sites` | `2020-08-01-Preview` |

"(プレビュー)" とマークされたリソースは、プレビュー管理プレーン API バージョンでのみ使用でき、まだ一般提供されていません。 その他の `groupId` (または、それをサポートしていない API バージョンで使用される `groupId`) は検証に失敗します。

+ `privateLinkResourceId` の種類の検証: `groupId` と同様に、Azure Cognitive Search により、`privateLinkResourceId` で "正しい" リソースの種類が指定されていることが検証されます。 有効なリソースの種類は以下のとおりです。

| Azure リソース | リソースの種類 | 利用可能な最初の API バージョン |
| --- | --- | --- |
| Azure Storage | `Microsoft.Storage/storageAccounts`| `2020-08-01` |
| Azure Cosmos DB | `Microsoft.DocumentDb/databaseAccounts`| `2020-08-01` |
| Azure SQL データベース | `Microsoft.Sql/servers`| `2020-08-01` |
| Azure Database for MySQL (プレビュー) | `Microsoft.DBforMySQL/servers`| `2020-08-01-Preview` |
| Azure Key Vault | `Microsoft.KeyVault/vaults` | `2020-08-01` |
| Azure Functions (プレビュー) | `Microsoft.Web/sites` | `2020-08-01-Preview` |

さらに、指定されたリソースの種類に対して、指定された `groupId` が有効である必要があります。 たとえば、`groupId` "BLOB" は、"Microsoft.Storage/storageAccounts" に対して有効であり、その他のリソースの種類では使用できません。 特定の検索管理 API バージョンについて、顧客は[サポートされている API の一覧](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources/list-supported)を使用して、サポートされている `groupId` およびリソースの種類の詳細を確認できます。

+ クォータ制限の適用: Search Service では、作成できる共有プライベート リンク リソースの個別の数と、使用されているさまざまなターゲット リソースの種類の数 (`groupId` に基づく) に対してクォータが課せられています。 これらは、Azure Cognitive Search のサービスの制限に関するページのセクション「[共有プライベート リンク リソースの制限](search-limits-quotas-capacity.md#shared-private-link-resource-limits)」に記載されています。

### <a name="azure-resource-manager-deployment-failures"></a>Azure Resource Manager デプロイ エラー

検索サービスは、共有プライベート リンクを作成する要求を開始しますが、実際の処理は、Azure Resource Manager が実行します。 [デプロイの状態](search-indexer-howto-access-private.md#step-3-check-the-status-of-the-private-endpoint-creation)は、ポータル内で、またはクエリを使用して確認し、発生する可能性があるエラーに対処できます。

Azure Resource Manager デプロイに失敗した共有プライベート リンク リソースは、[List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) および [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) API 呼び出しに表示されますが、"プロビジョニング状態" は `Failed` になります。 Azure Resource Manager デプロイ エラーの原因が確認されたら、`Failed` リソースを削除し、次の表から適切な解決策を適用した後、再作成します。

| デプロイ エラーの原因 | 説明 | 解像度 |
| --- | --- | --- |
| ネットワーク リソース プロバイダーがターゲット リソースのサブスクリプションに登録されていない | プライベート エンドポイント (および関連付けられている DNS マッピング) は、`Microsoft.Network` リソース プロバイダー (RP) を使用して、ターゲット リソース (ストレージ アカウント、Cosmos DB、SQL サーバーなど) 向けに作成されます。 ターゲット リソースをホストするサブスクリプション ("ターゲット サブスクリプション") に `Microsoft.Network` RP が登録されていない場合、Azure Resource Manager デプロイは失敗するおそれがあります。 | 顧客は、この RP をターゲット サブスクリプションに登録する必要があります。 通常、これは、[このガイド](../azure-resource-manager/management/resource-providers-and-types.md)に記載されているとおり Azure portal、PowerShell、または CLI を使用して実行できます。 |
| ターゲット リソースの `groupId` が無効 | Cosmos DB アカウントが作成されると、顧客はデータベース アカウントの API の種類を指定できます。 Cosmos DB にはいくつかの異なる種類の API が用意されていますが、Azure Cognitive Search では、共有プライベート リンク リソースの `groupId` として "Sql" のみがサポートされています。 Sql 以外のデータベース アカウントを参照する `privateLinkResourceId` に対して "Sql" 共有プライベート リンク リソースが作成されると、`groupId` の不一致が原因で Azure Resource Manager デプロイは失敗します。 Cosmos DB アカウントの Azure リソース ID は、使用されている API の種類を特定するのに十分ではありません。 Azure Cognitive Search によってプライベート エンドポイントの作成が試行され、Cosmos DB によって拒否されます。 | 顧客は、指定された Cosmos DB リソースの `privateLinkResourceId` が、種類が "Sql" である API のデータベース アカウント用であることを確認する必要があります。 |
| ターゲット リソースが見つからない | `privateLinkResourceId` で指定されたターゲット リソースの存在は、Azure Resource Manager デプロイの開始時にのみ確認されます。 ターゲット リソースが使用できなくなった場合、デプロイは失敗します。 | 顧客は、指定されたサブスクリプションとリソース グループにターゲット リソースが存在し、移動または削除されていないことを確保する必要があります。 |
| 一時的、またはその他のエラー | インフラストラクチャの障害が発生した場合、またはその他の予期しない原因により、Azure Resource Manager デプロイが失敗するおそれがあります。 これはまれであり、通常は一時的な状態を示します。 | 後でこのリソースの作成をもう一度お試しください。 問題が引き続き発生する場合は、Azure サポートにお問い合わせください。 |

### <a name="resource-stuck-in-updating-or-incomplete-state"></a>リソースが "更新中" または "未完了" 状態でスタックする

通常、共有プライベート リンク リソースは、Search RP によって要求が受け入れられてから数分後に最終状態 (`Succeeded` または `Failed`) になります。

まれに、Azure Cognitive Search で共有プライベート リンク リソースの状態を最終状態 (`Succeeded` または `Failed`) に正しくマークできない場合があります。 これは通常、Search RP の予期しないエラーまたは重大なエラーが原因で発生します。 共有プライベート リンク リソースは、最終状態以外の状態で 8 時間以上 "スタック" した場合、自動的に `Failed` 状態に遷移します。

共有プライベート リンク リソースが最終状態に遷移していないことが確認された場合は、8 時間待って確実に `Failed` になってから、そのリソースを削除し、再作成します。 または、待機する代わりに、別の共有プライベート リンク リソースを別の名前で作成することもできます (他のすべてのパラメーターは同じままにします)。

## <a name="updating-a-shared-private-link-resource"></a>共有プライベート リンク リソースの更新

既存の共有プライベート リンク リソースは、[Create API または Update API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update) を使用して更新できます。 Search RP では、共有プライベート リンク リソースの限られた更新のみが可能です。この API を使用して更新できるのは、要求メッセージだけです。

+ 既存の共有プライベート リンク リソース (`privateLinkResourceId` や `groupId` など) の "コア" プロパティを更新することはできません。これは常にサポートされません。 要求メッセージ以外のプロパティを変更する必要がある場合は、共有プライベート リンク リソースを削除して再作成することをお勧めします。

+ 共有プライベート リンク リソースの要求メッセージの更新は、`Succeeded` のプロビジョニング状態に達した場合にのみ可能です。

## <a name="deleting-a-shared-private-link-resource"></a>共有プライベート リンク リソースの削除

顧客は、[Delete API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/delete) を使用して、既存の共有プライベート リンク リソースを削除できます。 作成 (または更新) のプロセスと同様に、これは 4 つの手順を含む非同期操作です。

1. 顧客が、共有プライベート リンク リソースを削除するよう Search RP に要求します。

2. Search RP により、リソースが存在しており、削除に有効な状態にあることが検証されます。 その場合は、リソースを削除するために Azure Resource Manager の削除操作が開始されます。

3. Search により、操作の完了に対してクエリが実行されます (通常は数分かかります)。 この時点で、共有プライベート リンク リソースのプロビジョニング状態は "削除中" になります。

4. 操作が正常に完了すると、バッキング プライベート エンドポイントおよび関連付けられているすべての DNS マッピングが削除されます。 リソースは [List](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/list-by-service) 操作の一部として表示されません。このリソースに対して [Get](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/get) 操作を試行すると、"404 Not Found" エラーになります。

![共有プライベート リンク リソースの削除に必要な手順 ](media\troubleshoot-shared-private-link-resources\shared-private-link-delete-states.png)

削除フェーズ中に発生する一般的なエラーを以下に示します。

| 失敗の種類 | 説明 | 解像度 |
| --- | --- | --- |
| リソースが最終状態以外の状態である | 最終状態 (`Succeeded` または `Failed`) ではない共有プライベート リンク リソースは削除できません。 共有プライベート リンク リソースが (まれに) 最大 8 時間、最終状態以外の状態でスタックする場合があります。 | リソースが最終状態に達するまで待ち、削除要求をもう一度お試しください。 |
| 削除操作が "競合" エラーで失敗した | 共有プライベート リンク リソースを削除する Azure Resource Manager 操作が、`privateLinkResourceId` ("ターゲット RP") で指定されたターゲット リソースのリソース プロバイダーに到達してから、プライベート エンドポイントと DNS マッピングを削除できるようになります。 顧客は、[Azure リソース ロック](../azure-resource-manager/management/lock-resources.md)を利用して、リソースに対する変更を防止できます。 Azure Resource Manager がターゲット RP に到達すると、ターゲット RP では、(プライベート エンドポイントに関する詳細をそのメタデータから削除するために) ターゲット リソースの状態を変更する必要があります。 ターゲット リソース (またはそのリソース グループかサブスクリプション) にロックが構成されている場合、Azure Resource Manager 操作は "競合" (および該当する詳細情報) で失敗します。 共有プライベート リンク リソースは削除されません。 | 顧客は、削除操作を再試行する前に、ターゲット リソースのロックを解除する必要があります。 **注**: この問題は、顧客が、"ロックされた" ターゲット リソースを参照する共有プライベート リンク リソースを使用して Search Service を削除しようとした場合にも発生するおそれがあります。 |
| 削除操作に失敗した | まれに、非同期 Azure Resource Manager 削除操作が失敗する場合があります。 この操作が失敗した場合、非同期操作の状態に対してクエリを実行すると、顧客に対してエラー メッセージと該当する詳細情報が表示されます。 | 後で操作をもう一度お試しください。問題が解決しない場合は、Azure サポートにお問い合わせください。
| リソースが "削除中" 状態でスタックする | まれに、共有プライベート リンク リソースが "削除中" 状態で最大 8 時間スタックする場合があります。これは、Search RP で致命的なエラーが発生したことが原因であるおそれがあります。 | 8 時間待って、リソースが `Failed` 状態に遷移してから、要求を再発行します。|

## <a name="next-steps"></a>次のステップ

共有プライベート リンク リソースと、そのリソースを使用して保護されたコンテンツへの安全なアクセスを確保する方法の詳細については、こちらをご覧ください。

+ [インデクサーを使用した保護されたコンテンツへのアクセス](search-indexer-howto-access-private.md)
+ [REST API リファレンス](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources)