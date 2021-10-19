---
title: Azure の医療 Api の DICOM サービスでの DICOMweb Standard Api の使用
description: このチュートリアルでは、DICOMweb Standard Api と DICOM サービスを使用する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631989"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>&trade;DICOM サービスでの DICOMweb Standard api の使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このチュートリアルでは、DICOMweb Standard Api と DICOM サービスを使用する方法の概要につい &trade; て説明します。

DICOM サービスは、次のような、DICOMweb 標準のサブセットをサポートしてい &trade; ます。

* 格納 (STOW-RS)
* 取得 (WADO-RS)
* 検索 (QIDO-RS)

また、次の非標準 API がサポートされています。

* 削除
* 変更フィード

DICOM Web Standard Api のサポートの詳細については、「 [Dicom 準拠ステートメント](dicom-services-conformance-statement.md) のリファレンス」ドキュメントを参照してください。

## <a name="prerequisites"></a>前提条件

DICOMweb Standard Api を使用するには、 &trade; DICOM サービスのインスタンスがデプロイされている必要があります。 まだ DICOM サービスのインスタンスをデプロイしていない場合は、「 [Azure portal を使用した dicom サービスのデプロイ](deploy-dicom-services-in-azure.md)」を参照してください。

デプロイが完了したら、Azure portal を使用して、新しく作成された DICOM サービスに移動し、サービス URL などの詳細を確認できます。 DICOM サービスにアクセスするためのサービス URL は次のようになり ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` ます。 要求を行うときは、url の一部としてバージョンを指定してください。 詳細については、DICOM サービスの [API バージョン管理に関するドキュメント](api-versioning-dicom-service.md)を参照してください。

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>DICOM サービスで使用するさまざまな方法の概要

DICOM サービスは REST API として公開されているため、最新の開発言語を使用してアクセスできます。 サービスの使用に関する言語に依存しない情報については、「 [DICOM 準拠ステートメント](dicom-services-conformance-statement.md)」を参照してください。

言語固有の例については、以下の例を参照してください。 Postman collection の例は、次のようないくつかの言語で表示できます。

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* Swift.

### <a name="c"></a>C#

[「C# での DICOMweb™標準 api](dicomweb-standard-apis-c-sharp.md)の使用」のチュートリアルを参照して、DICOM サービスで c# を使用する方法を学習してください。

### <a name="curl"></a>cURL

cURL は、ほぼすべてのオペレーティングシステムで使用できる web エンドポイントを呼び出すための共通コマンドラインツールです。 開始するには[cURL をダウンロード](https://curl.haxx.se/download.html)してください。

CURL を DICOM サービスと共に使用する方法については、「cURL を使用 [した web™の標準 api](dicomweb-standard-apis-curl.md) 」を参照してください。

### <a name="python"></a>Python

DICOM サービスで Python を使用する方法については、 [「python での DICOMWeb™標準 api の使用](dicomweb-standard-apis-python.md) 」のチュートリアルを参照してください。

### <a name="postman"></a>postman

Postman は、REST Api の設計、構築、およびテストを行うための優れたツールです。 [Postman をダウンロード](https://www.postman.com/downloads/) して始めましょう。 Postman を効果的に使用する方法については、 [Postman learning サイト](https://learning.postman.com/)を参照してください。

Postman と DICOMweb 標準に関する重要な注意事項の1つ &trade; は、Postman がサポートするのは、dicom 標準で定義されている単一のパートペイロードを使用した dicom ファイルのアップロードのみです。 これは、Postman がマルチパート/関連する POST 要求でカスタムの区切り記号をサポートできないためです。 詳細については、「 [マルチパート POST が機能しない](https://github.com/postmanlabs/postman-app-support/issues/576)」を参照してください # 576。 したがって、マルチパート要求を使用して、DICOM ドキュメントをアップロードするための Postman collection のすべての例には、プレフィックス [は使用できません。説明を参照してください。 単一のパート要求を使用してアップロードする例は、コレクションに含まれ、プレフィックスとして "ストア-単一インスタンス" が付いています。

Postman コレクションを使用するには、コレクションをローカルでダウンロードし、Postman を使用してコレクションをインポートする必要があります。 このコレクションにアクセスするには、「 [Postman collection の例](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)」を参照してください。

## <a name="summary"></a>まとめ

このチュートリアルでは、DICOM サービスでサポートされている Api の概要を説明しました。 これらの Api の使用を開始するには、次のツールを使用します。

- [C での DICOMweb™標準 Api の使用#](dicomweb-standard-apis-c-sharp.md)
- [CURL での DICOMWeb™標準 Api の使用](dicomweb-standard-apis-curl.md)
- [Python での DICOMWeb™標準 Api の使用](dicomweb-standard-apis-python.md)
- [Postman サンプルコレクションでの DICOM Web Standard Api の使用](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>次の手順

詳細については、「

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
