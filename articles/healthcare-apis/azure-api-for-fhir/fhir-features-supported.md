---
title: Azure でサポートされる FHIR 機能 - Azure API for FHIR
description: この記事では、Azure API for FHIR で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 6/16/2021
ms.author: cavoeg
ms.openlocfilehash: cbc4b8b81bf2732c3646e4bd04e0652a4b8abf1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779109"
---
# <a name="features"></a>特徴

Azure API for FHIR は、Microsoft FHIR Server for Azure の完全管理型デプロイを提供します。 このサーバーは、[FHIR](https://hl7.org/fhir) 標準の実装です。 このドキュメントでは、FHIR Server の主な機能を一覧表示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 読み取り                           | はい       | はい       | はい       |                                                     |
| vread                          | はい       | はい       | はい       |                                                     |
| update                         | はい       | はい       | はい       |                                                     |
| オプティミスティック ロック付きの update | はい       | はい       | はい       |                                                     |
| update (条件付き)           | はい       | はい       | はい       |                                                     |
| patch                          | いいえ        | いいえ        | いいえ        |                                                     |
| delete                         | はい       | はい       | はい       |  下記の「注」を参照してください。                                   |
| delete (条件付き)           | はい       | はい        | はい        |                                                     |
| history                        | はい       | はい       | はい       |                                                     |
| create                         | はい       | はい       | はい       | POST/PUT の両方をサポートします                               |
| create (条件付き)           | はい       | はい       | はい       | イシュー [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| 検索                         | 部分的   | 部分的   | 部分的   | 「[FHIR 検索の概要](overview-of-search.md)」を参照してください。                           |
| chained search                 | 部分的       | はい       | 部分的   | 下記の「注 2」を参照してください。                                   |
| reverse chained search         | 部分的       | はい       | 部分的   | 下記の「注 2」を参照してください。                                   |
| capabilities                   | はい       | はい       | はい       |                                                     |
| batch (バッチ)                          | はい       | はい       | はい       |                                                     |
| transaction                    | いいえ        | はい       | いいえ        |                                                     |
| paging                         | 部分的   | 部分的   | 部分的   | `self` と `next` がサポートされています                     |
| intermediaries                 | いいえ        | いいえ        | いいえ        |                                                     |

> [!Note]
> FHIR 仕様で定義された削除では、仕様の要件により、削除後にリソースに対して後続の非バージョン固有読み取りを行うと、410 HTTP 状態コードが返され、検索によってリソースを見つけられなくなります。 Azure API for FHIR でも、リソース (すべての履歴を含む) を完全に削除することができます。 リソースを完全に削除するには、パラメーター設定 `hardDelete` を true (`DELETE {server}/{resource}/{id}?hardDelete=true`) にします。 このパラメーターを指定しなかった場合や、`hardDelete` を false に設定した場合は、その後もリソースの履歴バージョンを利用できます。
> 
> Azure API for FHIR と、Cosmos によってサポートされるオープンソースの FHIR サーバーでは、チェーン検索と逆チェーン検索は MVP 実装です。 Cosmos DB でチェーン検索を実現するために、この実装では、検索式を処理し、サブクエリを発行して、一致したリソースを解決します。 これは、式の各レベルに対して行われます。 クエリで 100 を超える結果が返される場合は、エラーがスローされます。 既定では、チェーン検索は機能フラグの背後にあります。 Cosmos DB 上でチェーン検索を使用するには、ヘッダー `x-ms-enable-chained-search: true` を使用します。 詳細については、[PR 1695](https://github.com/microsoft/fhir-server/pull/1695) に関するページを参照してください。

## <a name="extended-operations"></a>拡張操作

サポートされており、RESTful API を拡張するあらゆる操作。

| 検索パラメーターの種類 | サポート対象 - PaaS | サポート対象 - OSS (SQL) | サポート対象 - OSS (Cosmos DB) | 解説 |
|------------------------|-----------|-----------|-----------|---------|
| $export (システム全体) | はい       | はい       | はい       |         |
| Patient/$export        | はい       | はい       | はい       |         |
| Group/$export          | はい       | はい       | Yes       |         |
| $convert-data          | Yes       | はい       | はい       |         |
| $validate              | はい       | はい       | はい       |         |
| $member-match          | はい       | はい       | はい       |         |
| $patient-everything    | はい       | はい       | はい       |         |

## <a name="persistence"></a>永続化

Microsoft FHIR Server には、プラグ可能な永続化モジュールがあります ([`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) を参照)。

現在、FHIR Server のオープン ソース コードには [Azure Cosmos DB](../../cosmos-db/index-overview.md) および [SQL Database](https://azure.microsoft.com/services/sql-database/) 用の実装が含まれています。

Cosmos DB は、グローバル分散型のマルチモデル (SQL API、MongoDB API など) データベースです。 これは、さまざまな[整合性レベル](../../cosmos-db/consistency-levels.md)をサポートしています。 既定のデプロイ テンプレートでは `Strong` の整合性で FHIR Server を構成しますが、整合性ポリシーは、`x-ms-consistency-level` 要求ヘッダーを使用して要求ごとに変更 (一般には緩和) できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR Server は、アクセス制御のために [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。 特に、`FhirServer:Security:Enabled` 構成パラメーターが `true` に設定され、FHIR Server へのすべての要求 (`/metadata` を除く) で `Authorization` 要求ヘッダーが `Bearer <TOKEN>` に設定されている必要がある場合は、ロールベースのアクセス制御 (RBAC) が適用されます。 トークンには、`roles` 要求で定義されている 1 つ以上のロールが含まれている必要があります。 トークンに、指定されたリソースに対する指定されたアクションを許可するロールが含まれている場合は、要求が許可されます。

現在、特定のロールに対して許可されるアクションは API で *グローバルに* 適用されます。

## <a name="service-limits"></a>サービスの制限

* [**要求ユニット (RU)**](../../cosmos-db/concepts-limits.md) - Azure API for FHIR のポータルで最大 10,000 RU を構成できます。 少なくとも 400 RU か 40 RU/GB (いずれか大きい方) が必要になります。 必要な単位が 10,000 RU を超える場合、サポート チケットを発行して増やすことができます。 利用できる最大値は 1,000,000 です。

* **バンドル サイズ** - 各バンドルは 500 項目に制限されています。

* **データ サイズ** - データとドキュメントはそれぞれ 2 MB より少しばかり少なくする必要があります。

* **サブスクリプションの制限** - 既定では、各サブスクリプションは最大 10 個の FHIR サーバー インスタンスに制限されています。 それを超えるインスタンス数が各サブスクリプションに必要な場合は、サポート チケットを開き、ニーズの詳細を伝えてください。

* **コンカレント接続とインスタンス** - 既定では、クラスター内の 2 つのインスタンス上で 15 個のコンカレント接続を利用できます (同時要求は合計で 30)。 それを超える同時要求が必要な場合は、サポート チケットを開き、ニーズの詳細を伝えてください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR でサポートされる FHIR 機能について学習しました。 次は Azure API for FHIR をデプロイします。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)
