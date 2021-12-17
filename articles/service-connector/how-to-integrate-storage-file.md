---
title: Azure File Storage を Service Connector と統合する
description: Service Connector を使用して Azure File Storage を アプリケーションに統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: dd6f94a65c1f4b04a81b1604109c6562b37826f4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017435"
---
# <a name="integrate-azure-file-storage-with-service-connector"></a>Azure File Storage を Service Connector と統合する

このページでは、Service Connector を使用する Azure File Storage のサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語内で Azure File Storage に接続できる場合があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | | | ![[はい] アイコン](./media/green-check.png) | |
| Java | | | ![[はい] アイコン](./media/green-check.png) | |
| Java - Spring Boot | | | ![[はい] アイコン](./media/green-check.png) | |
| Node.js | | | ![[はい] アイコン](./media/green-check.png) | |
| Python | | | ![[はい] アイコン](./media/green-check.png) | |
| PHP | | | ![[はい] アイコン](./media/green-check.png) | |
| Ruby | | | ![[はい] アイコン](./media/green-check.png) | |



## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net-java-nodejs-python-php-and-ruby"></a>.NET、Java、Node.JS、Python、PHP、Ruby

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_STORAGEFILE_CONNECTIONSTRING | ファイル ストレージ接続文字列 | `DefaultEndpointsProtocol=https;AccountName={accountName};AccountKey={****};EndpointSuffix=core.windows.net` |


### <a name="java---spring-boot"></a>Java - Spring Boot

**Secret/ConnectionString**

| Application properties | 説明 | 値の例 |
| --- | --- | --- |
| azure.storage.account-name | Azure File Storage アカウント名 | `{storageAccountName}` |
| azure.storage.account-key | Azure File Storage アカウント キー | `{yourSecret}` |
| azure.storage.file-endpoint | Azure File Storage エンドポイント | `https://{storageAccountName}.file.core.windows.net/` |


## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
