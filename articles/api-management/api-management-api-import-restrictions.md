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
ms.openlocfilehash: d4229a3ecbcf8aa044eb448290c243e9920bd5cb
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>API のインポートに関する制限事項と既知の問題
## <a name="about-this-list"></a>このリストについて
API をインポートするときに、制限や ID の問題が発生し、修正しないと正常にインポートできない場合があります。 この記事ではこうした内容について、API のインポート形式別に説明します。

## <a name="open-api"> </a>Open API/Swagger
Open API ドキュメントのインポートでエラーが発生した場合は、Azure Portal のデザイナー ([Design]\(設計\) - [フロント エンド] - [Open API Specification Editor]\(Open API 仕様エディター\))、または <a href="http://www.swagger.io">Swagger Editor</a> などのサードパーティ ツールを使用して、そのドキュメントが有効であることを確認してください。

* **ホスト名** APIM ではホスト名属性が必要です。
* **ベース パス**  APIM ではベース パス属性が必要です。
* **スキーム**  APIM ではスキーム配列が必要です。

> [!IMPORTANT]
> OpenAPI のインポートに関する重要な情報とヒントについては、この[ドキュメント](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/)を参照してください。

## <a name="wsdl"> </a>WSDL
WSDL ファイルは、SOAP パススルー API の作成に使用されるか、SOAP-to-REST API のバックエンドとして機能します。

* **WSDL:Import** - 現在、APIM では、この属性を使用する API はサポートされていません。 インポートした要素を 1 つのドキュメントに結合する必要があります。
* **複数の部分を持つメッセージ** 現在、APIM では、これらの種類のメッセージはサポートされていません。
* **WCF wsHttpBinding**: Windows Communication Foundation を使用して作成した SOAP サービスでは、basicHttpBinding を使用する必要があります。wsHttpBinding はサポートされていません。
* **MTOM**: MTOM を使用するサービスは、動作する ”<em>可能性があります</em>”。 現時点では、正式なサポートは提供されていません。
* **再帰** APIM では、再帰的に定義される (たとえば、自身の配列を参照する) 型はサポートされていません。

## <a name="wadl"> </a>WADL
現時点では、WADL のインポートに関する既知の問題はありません。
