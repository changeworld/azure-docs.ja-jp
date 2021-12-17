---
title: FHIR サービスでサポートされている FHIR 機能
description: この記事では、医療 API に実装されている FHIR 仕様の機能について説明します
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: 4c1b9efce8ad29eeb9ebca6285849760f20fbc17
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399903"
---
# <a name="supported-fhir-features"></a>サポートされている FHIR 機能

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

Azure Healthcare API の FHIR サービス (ここでは FHIR サービスと呼ばれる) は、オープンソースの FHIR Server の完全に管理されたデプロイを提供し &reg; [、FHIR](https://hl7.org/fhir)[](https://github.com/microsoft/fhir-server)標準の実装です。 このドキュメントでは、FHIR サービスの主な機能の一覧を示します。

## <a name="fhir-version"></a>FHIR のバージョン

サポートされている最新バージョン: `4.0.1`

現在サポートされている以前のバージョンは次のとおりです: `3.0.2`

## <a name="rest-api"></a>REST API

サポートされている RESTful 機能の概要を次に示します。 これらの機能の実装の詳細については [、「FHIR REST APIを参照してください](fhir-rest-api-capabilities.md)。 

| API    | FHIR 用の Azure API | 医療 API の FHIR サービス | 解説 |
|--------|--------------------|---------------------------------|---------|
| 読み取り   | はい                | はい                             |         |
| vread  | はい                | はい                             |         |
| update | はい                | はい                             |         | 
| オプティミスティック ロック付きの update | はい       | はい       |
| update (条件付き)           | はい       | はい       |
| patch                          | はい       | はい       | JSON パッチ [のみサポート](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch) 。 |
| patch (条件付き)            | はい       | はい       |
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

## <a name="extended-operations"></a>拡張操作

アプリケーションを拡張する、サポートREST API。

| 検索パラメーターの種類 | FHIR 用の Azure API | 医療 API の FHIR サービス| 解説 |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (システム全体) | はい       | はい       | システム、グループ、患者をサポートします。 |
| [$convert-data](../../healthcare-apis/data-transformation/convert-data.md)          | はい       | はい       |         |
| [$validate](validation-against-profiles.md)              | はい       | はい       |         |
| [$member-match](tutorial-member-match.md)          | はい       | はい       |         |
| [$patient-everything](patient-everything.md)    | はい       | はい       |         |
| $purge履歴         | はい       | はい       |         |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

FHIR サービスは、アクセス制御[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)を使用します。 

## <a name="service-limits"></a>サービスの制限

* **バンドル サイズ** - 各バンドルは 500 項目に制限されています。

* **サブスクリプションの制限** -既定では、各サブスクリプションは最大10個の FHIR サービスに制限されています。 この制限は、1つまたは複数のワークスペースで使用できます。 

## <a name="next-steps"></a>次のステップ

この記事では、FHIR サービスでサポートされている FHIR の機能について説明します。 FHIR サービスのデプロイの詳細については、「」を参照してください。
 
>[!div class="nextstepaction"]
>[FHIR サービスをデプロイする](fhir-portal-quickstart.md)
