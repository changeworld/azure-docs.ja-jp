---
title: Azure Table Storage と Service Connector を統合する
description: Service Connector を使用したアプリケーションへの Azure Table Storage の統合
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 646abc1fc11de0c1d9d5f3c6382e0824c020e85c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089747"
---
# <a name="integrate-azure-table-storage-with-service-connector"></a>Azure Table Storage と Service Connector を統合する

このページでは、Service Connector を使用した Azure Table Storage でサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語で Azure Table Storage に接続できる可能性があります。 このページでは、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) についても説明します。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | | | ![[はい] アイコン](./media/green-check.png) | |
| Java | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js | | | ![[はい] アイコン](./media/green-check.png) | |
| Python | | | ![[はい] アイコン](./media/green-check.png) | |


## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-java-nodejs-and-python"></a>.NET、Java、Node.JS、Python

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGETABLE_CONNECTIONSTRING | テーブル ストレージ接続文字列 | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
