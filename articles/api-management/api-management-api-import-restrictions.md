---
title: Azure API Management での API のインポートの制限事項と既知の問題 | Microsoft Docs
description: Open API、WSDL、または WADL 形式での Azure API Management へのインポートに関する既知の問題と制限事項の詳細です。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: a9f4a4ed4a8771f32a4d66aed2457a43abb92a63
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295331"
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題
## <a name="about-this-list"></a>このリストについて
API をインポートするときに、制限や ID の問題が発生し、修正しないと正常にインポートできない場合があります。 この記事ではこうした内容について、API のインポート形式別に説明します。

## <a name="open-api"> </a>API を開く/Swagger
Open API ドキュメントのインポートでエラーが発生した場合は、Azure Portal のデザイナー (デザイン - フロント エンド - Open API 仕様エディター)、または <a href="http://editor.swagger.io">Swagger Editor</a> などのサードパーティ ツールを使用して、そのドキュメントが有効であることを確認してください。

* OpenAPI 用の JSON 形式のみがサポートされています。
* パスとクエリの両方の間で必要なパラメーターは、一意の名前が必要です。 ( OpenAPI では、 パラメーター名が、パス、クエリ、ヘッダーなどの場所内で一意であることだけが必要です。  しかし、 API Management では、パスとクエリの両方のパラメーターで判別する操作を許可します ( OpenAPI はサポートしません)。 したがって、 URL テンプレート　全体の中で、一意であるパラメーターの名前が必要です。)
* **$ref** プロパティを使用して参照されるスキーマには、他の **$ref** プロパティを含めることができません。
* **$ref** ポインターは、外部ファイルを参照できません。
* サポートされている拡張子は、**x-ms-paths** と **x-servers** のみです。
* カスタム拡張子は、インポート時に無視され、エクスポートでは保存されず、保持もされません。

> [!IMPORTANT]
> OpenAPI のインポートに関する重要な情報とヒントについては、この[ドキュメント](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)を参照してください。

## <a name="wsdl"> </a>WSDL
WSDL ファイルは、SOAP パススルー API の作成に使用されるか、SOAP-to-REST API のバックエンドとして機能します。
* **SOAP バインド** - "document" および "literal" エンコード スタイルの SOAP バインドのみがサポートされています。 "rpc" スタイルまたは SOAP エンコードはサポートされていません。
* **WSDL:Import** - この属性はサポートされていません。 インポートを 1 つのドキュメントに結合する必要があります。
* **複数の部分で構成されたメッセージ** - この種のメッセージはサポートされていません。
* **WCF wsHttpBinding** - Windows Communication Foundation を使用して作成した SOAP サービスでは、basicHttpBinding を使用する必要があります。wsHttpBinding はサポートされていません。
* **MTOM** - MTOM を使用するサービスは、動作する "<em>可能性があります</em>"。 現時点では、正式なサポートは提供されていません。
* **再帰** - APIM では、再帰的に定義される型 (自身の配列を参照する型など) はサポートされていません。

## <a name="wadl"> </a>WADL
現時点では、WADL のインポートに関する既知の問題はありません。
