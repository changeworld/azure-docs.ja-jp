---
title: FHIR サービスでサポートされている FHIR の機能
description: この記事では、医療 Api で実装されている FHIR 仕様の機能について説明します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045845"
---
# <a name="supported-fhir-features"></a>サポートされている FHIR の機能

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

&reg;Azure の医療 api (fhir サービスと呼ばれます) の FHIR サービスは、[オープンソースの Fhir サーバー](https://github.com/microsoft/fhir-server)の完全に管理されたデプロイを提供します。これは[fhir](https://hl7.org/fhir)標準の実装です。 このドキュメントでは、FHIR サービスの主な機能の一覧を示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

| API    | FHIR 用の Azure API | 医療 Api における FHIR サービス | 解説 |
|--------|--------------------|---------------------------------|---------|
| 読み取り   | はい                | はい                             |         |
| vread  | はい                | はい                             |         |
| update | はい                | はい                             |         | 
| オプティミスティック ロック付きの update | はい       | はい       |
| update (条件付き)           | はい       | はい       |
| patch                          | はい       | はい       | [JSON 修正プログラム](https://www.hl7.org/fhir/http.html#patch)のみのサポート。 [この PR](https://github.com/microsoft/fhir-server/pull/2143)のバンドルで JSON 修正プログラムを使用するための回避策が含まれています。|
| patch (条件付き)            | はい       | はい       |
| delete                         | はい       | はい       | 詳細については、以下の「削除」セクションを参照してください。 |
| delete (条件付き)           | はい       | はい       | 詳細については、以下の「削除」セクションを参照してください。 |
| history                        | はい       | はい       |
| create                         | はい       | はい       | POST/PUT の両方をサポートします |
| create (条件付き)           | はい       | はい       | イシュー [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| 検索                         | Partial   | 部分的   | 「[FHIR 検索の概要](overview-of-search.md)」を参照してください。 |
| chained search                 | はい       | はい       | |
| reverse chained search         | はい       | はい       | |
| batch (バッチ)                          | はい       | はい       |
| transaction                    | いいえ        | はい       |
| paging                         | 部分的   | 部分的   | `self` と `next` がサポートされています                     |
| intermediaries                 | いいえ        | いいえ        |

### <a name="delete-and-conditional-delete"></a>Delete および条件付き削除

FHIR 仕様で定義された削除では、仕様の要件により、削除後にリソースに対して後続の非バージョン固有読み取りを行うと、410 HTTP 状態コードが返され、検索によってリソースを見つけられなくなります。 Azure API for FHIR および FHIR サービスでは、リソースを完全に削除することもできます (すべての履歴を含む)。 リソースを完全に削除するには、パラメーター設定 `hardDelete` を true (`DELETE {server}/{resource}/{id}?hardDelete=true`) にします。 このパラメーターを指定しなかった場合や、`hardDelete` を false に設定した場合は、その後もリソースの履歴バージョンを利用できます。

Delete に加えて、Azure API for FHIR および FHIR サービスは条件付き削除をサポートしています。これにより、リソースを削除するための検索条件を渡すことができます。 既定では、条件付き削除を使用すると、一度に1つの項目を削除できます。 パラメーターを指定して `_count` 、最大100個の項目を一度に削除することもできます。 条件付き削除の使用例を次に示します。

条件付き削除を使用して1つの項目を削除するには、1つの項目を返す検索条件を指定する必要があります。
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

同じ検索を実行できますが、すべての履歴も削除するには、"ハード削除 = true" を含める必要があります。
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

複数のリソースを削除する場合は、を含めることができます `_count=100` 。これにより、検索条件に一致する最大100のリソースが削除されます。 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>拡張操作

REST API を拡張する、サポートされているすべての操作。

| 検索パラメーターの種類 | FHIR 用の Azure API | 医療 Api における FHIR サービス| 解説 |
|------------------------|-----------|-----------|---------|
| $export (システム全体) | はい       | はい       |         |
| Patient/$export        | はい       | はい       |         |
| Group/$export          | はい       | Yes       |         |
| $convert-data          | Yes       | はい       |         |
| $validate              | はい       | はい       |         |
| $member-match          | はい       | はい       |         |
| $patient-everything    | はい       | はい       |         |
| $purge-履歴         | はい       | はい       |         |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

fhir サービスは、アクセス制御のために[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)を使用します。 

## <a name="service-limits"></a>サービスの制限

* **バンドル サイズ** - 各バンドルは 500 項目に制限されています。

* **サブスクリプションの制限** -既定では、各サブスクリプションは最大10個の FHIR サービスに制限されています。 この制限は、1つまたは複数のワークスペースで使用できます。 

## <a name="next-steps"></a>次の手順

この記事では、FHIR サービスでサポートされている FHIR の機能について説明します。 次に、FHIR サービスをデプロイします。
 
>[!div class="nextstepaction"]
>[FHIR サービスをデプロイする](fhir-portal-quickstart.md)
