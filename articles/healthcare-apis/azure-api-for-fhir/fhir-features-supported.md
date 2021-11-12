---
title: Azure でサポートされる FHIR 機能 - Azure API for FHIR
description: この記事では、Azure API for FHIR で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: b6c170a7b2f46adeb4b287424601455bb078aba7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397302"
---
# <a name="features"></a>特徴

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、FHIR Server の主な機能を一覧表示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

サポートされている RESTful 機能の概要を次に示します。 これらの機能の実装の詳細については、「 [Fhir REST API 機能](fhir-rest-api-capabilities.md)」を参照してください。

| API    | FHIR 用の Azure API | 医療 Api における FHIR サービス | 解説 |
|--------|--------------------|---------------------------------|---------|
| 読み取り   | はい                | はい                             |         |
| vread  | はい                | はい                             |         |
| update | はい                | はい                             |         | 
| オプティミスティック ロック付きの update | はい       | はい       |
| update (条件付き)           | はい       | はい       |
| patch                          | はい       | はい       | [JSON 修正プログラム](https://www.hl7.org/fhir/http.html#patch)のみのサポート。 [この PR](https://github.com/microsoft/fhir-server/pull/2143)のバンドルで JSON 修正プログラムを使用するための回避策が含まれています。|
| patch (条件付き)            | はい       | はい       |
| history                        | はい       | はい       |
| create                         | はい       | はい       | POST/PUT の両方をサポートします |
| create (条件付き)           | はい       | はい       | イシュー [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| 検索                         | Partial   | 部分的   | 「[FHIR 検索の概要](overview-of-search.md)」を参照してください。 |
| chained search                 | はい       | はい       | 下記のメモを参照してください。 |
| reverse chained search         | はい       | はい       | 下記のメモを参照してください。 |
| batch (バッチ)                          | はい       | はい       |
| transaction                    | いいえ        | はい       |
| paging                         | 部分的   | 部分的   | `self` と `next` がサポートされています                     |
| intermediaries                 | いいえ        | いいえ        |

> [!Note] 
> Azure API for FHIR と、Cosmos によってサポートされるオープンソースの FHIR サーバーでは、チェーン検索と逆チェーン検索は MVP 実装です。 Cosmos DB でチェーン検索を実現するために、この実装では、検索式を処理し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリが1000を超える結果を返す場合は、エラーがスローされます。

## <a name="extended-operations"></a>拡張操作

REST API を拡張する、サポートされているすべての操作。

| 検索パラメーターの種類 | FHIR 用の Azure API | 医療 Api における FHIR サービス| 解説 |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (システム全体) | はい       | はい       | システム、グループ、および患者をサポートします。   |
| [$convert-data](convert-data.md)          | はい       | はい       |         |
| [$validate](validation-against-profiles.md)              | はい       | はい       |         |
| [$member-match](tutorial-member-match.md)          | はい       | はい       |         |
| [$patient-everything](patient-everything.md)    | はい       | はい       |         |
| $purge履歴         | はい       | はい       |         |

## <a name="persistence"></a>永続化

Microsoft FHIR Server には、プラグ可能な永続化モジュールがあります ([`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) を参照)。

現在、FHIR Server のオープン ソース コードには [Azure Cosmos DB](../../cosmos-db/index-overview.md) および [SQL Database](https://azure.microsoft.com/services/sql-database/) 用の実装が含まれています。

Cosmos DB は、グローバル分散型のマルチモデル (SQL API、MongoDB API など) データベースです。 これは、さまざまな[整合性レベル](../../cosmos-db/consistency-levels.md)をサポートしています。 既定のデプロイ テンプレートでは `Strong` の整合性で FHIR Server を構成しますが、整合性ポリシーは、`x-ms-consistency-level` 要求ヘッダーを使用して要求ごとに変更 (一般には緩和) できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR Server は、アクセス制御のために [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。 特に、`FhirServer:Security:Enabled` 構成パラメーターが `true` に設定され、FHIR Server へのすべての要求 (`/metadata` を除く) で `Authorization` 要求ヘッダーが `Bearer <TOKEN>` に設定されている必要がある場合は、ロールベースのアクセス制御 (RBAC) が適用されます。 トークンには、`roles` 要求で定義されている 1 つ以上のロールが含まれている必要があります。 トークンに、指定されたリソースに対する指定されたアクションを許可するロールが含まれている場合は、要求が許可されます。

現在、特定のロールに対して許可されるアクションは API で *グローバルに* 適用されます。

## <a name="service-limits"></a>サービスの制限

* [**要求ユニット (RU)**](../../cosmos-db/concepts-limits.md) - Azure API for FHIR のポータルで最大 10,000 RU を構成できます。 少なくとも 400 RU か 40 RU/GB (いずれか大きい方) が必要になります。 10,000 を超える RUs が必要な場合は、サポート チケットを入れて、RUs を増やします。 利用できる最大値は 1,000,000 です。

* **バンドル サイズ** - 各バンドルは 500 項目に制限されています。

* **データ サイズ** - データとドキュメントはそれぞれ 2 MB より少しばかり少なくする必要があります。

* **サブスクリプションの制限** - 既定では、各サブスクリプションは最大 10 個の FHIR サーバー インスタンスに制限されています。 それを超えるインスタンス数が各サブスクリプションに必要な場合は、サポート チケットを開き、ニーズの詳細を伝えてください。

* **コンカレント接続とインスタンス** - 既定では、クラスター内の 2 つのインスタンス上で 15 個のコンカレント接続を利用できます (同時要求は合計で 30)。 それを超える同時要求が必要な場合は、サポート チケットを開き、ニーズの詳細を伝えてください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR でサポートされる FHIR 機能について学習しました。 アプリケーションのデプロイの詳細についてはAzure API for FHIR参照してください。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)
