---
title: DICOM サービスの API バージョン管理 - Azure Healthcare APIs
description: このガイドでは、DICOM サービスの API バージョン ポリシーの概要について説明します。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: c07a963a9dfeaa8e05d79c43cff18ab934f44f1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779501"
---
# <a name="api-versioning-for-dicom-service"></a>DICOM サービスの API バージョン管理

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このリファレンス ガイドでは、DICOM サービスの API バージョン ポリシーの概要について説明します。 

DICOM API のすべてのバージョンは常に DICOMweb™ Standard 仕様に準拠しますが、バージョンごとに [DICOM 適合性宣言書](dicom-services-conformance-statement.md)に基づいて異なる API が公開される場合があります。

## <a name="specifying-version-of-rest-api-in-requests"></a>要求での REST API バージョンの指定

次の例のように、REST API のバージョンは、要求 URL で明示的に指定する必要があります。

`<service_url>/v<version>/studies`

現在、バージョンのないルートは引き続きサポートされています。 たとえば、`<service_url>/studies` では、バージョンを v1.0-prerelease として指定するのと同じ動作が行われます。 ただし、URL を使用してすべての要求でバージョンを指定することを強く推奨します。

## <a name="supported-versions"></a>サポートされているバージョン

現時点では、次のバージョンがサポートされています。

* v1.0-prerelease

サポートされているバージョンの OpenApi ドキュメントは、次の URL で確認できます。
 
`<service_url>/{version}/api.yaml`

## <a name="prerelease-versions"></a>プレリリース バージョン

"prerelease" というラベルが付いた API バージョンは、バージョンが実稼働の準備ができていないことを示し、テスト環境でのみ使用する必要があります。 これらのエンドポイントでは、予告なしに破壊的変更が発生する可能性があります。

## <a name="how-versions-are-incremented"></a>バージョンの増分方法

現時点では、下位互換性がないと見なされる、破壊的変更がある場合にのみメジャー バージョンを増分しています。 すべてのマイナー バージョンは、0 に指定されます。 すべてのバージョンは、`Major.0` の形式です。

次に、破壊的変更の例を示します (メジャー バージョンは増分されます)。

1. エンドポイントの名前変更または削除。
2. パラメーターの削除または必須パラメーターの追加。
3. 状態コードの変更。
4. すべての応答内のプロパティを削除または応答の種類の変更。ただし、応答にプロパティを追加しても問題ありません。
5. プロパティの型の変更。
6. API が変更された場合の動作 (ビジネス ロジックの変更で、foo を実行していたものが、現在は bar を実行する場合など)。

非破壊的変更 (バージョンは増分されません):

1. null 許容または既定値を持つプロパティの追加。
2. 応答モデルへのプロパティの追加。
3. プロパティの順序の変更。

## <a name="header-in-response"></a>応答のヘッダー

ReportApiVersions がオンで、必要に応じてヘッダー api-supported-versions および api-deprecated-versions が返されます。

* api-supported-versions には、要求された API でサポートされているバージョンが一覧表示されます。 これは、`ApiVersion("<someVersion>")` で注釈付けされたエンドポイントを呼び出す場合にのみ返されます。

* api-deprecated-versions には、要求された API で非推奨になったバージョンが一覧表示されます。 これは、`ApiVersion("<someVersion>", Deprecated = true)` で注釈付けされたエンドポイントを呼び出す場合にのみ返されます。

例:

ApiVersion("1.0")

ApiVersion("1.0-prerelease", Deprecated = true)

[ ![API でサポートされているバージョンと非推奨のバージョン。](media/api-supported-deprecated-versions.png) ](media/api-supported-deprecated-versions.png#lightbox)

