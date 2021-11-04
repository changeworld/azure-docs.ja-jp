---
title: Azure SignalR Service と Service Connector を統合する
description: Service Connector を使用して Azure SignalR Service をアプリケーションに統合する
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 688321101923577fb5ef44ac8d7f23a63bf4b08c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017439"
---
# <a name="integrate-azure-signalr-service-with-service-connector"></a>Azure SignalR Service と Service Connector を統合する

このページでは、Service Connector を使用した Azure SignalR Service でサポートされている認証の種類とクライアントの種類を示します。 Service Connector を使用しなくても、他のプログラミング言語で Azure SignalR Service に接続できる可能性があります。 このページには、サービス接続を作成するときに取得する既定の環境変数の名前と値 (Spring Boot 構成) も示されています。 [Service Connector 環境変数の名前付け規則](concept-service-connector-internals.md)の詳細を参照してください。

## <a name="supported-compute-service"></a>サポートされているコンピューティング サービス

- Azure App Service

## <a name="supported-authentication-types-and-client-types"></a>サポートされている認証の種類とクライアントの種類

| クライアントの種類 | システム割り当てマネージド ID | ユーザー割り当てマネージド ID | Secret/ConnectionString | サービス プリンシパル |
| --- | --- | --- | --- | --- |
| .NET | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) | ![[はい] アイコン](./media/green-check.png) |

## <a name="default-environment-variable-names-or-application-properties"></a>既定の環境変数名またはアプリケーション プロパティ

### <a name="net"></a>.NET

**Secret/ConnectionString**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR Service 接続文字列 | `Endpoint=https://{signalrName}.service.signalr.net;AccessKey={};Version=1.0;` |

**システム割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR Service 接続文字列とマネージド ID | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**ユーザー割り当てマネージド ID**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | SignalR Service 接続文字列とマネージド ID | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};Version=1.0;` |

**サービス プリンシパル**

| 既定の環境変数名 | 説明 | 値の例 |
| --- | --- | --- |
| AZURE_SIGNALR_CONNECTIONSTRING | サービス プリンシパルを使用した SignalR Service 接続文字列 | `Endpoint=https://{signalrName}.service.signalr.net;AuthType=aad;ClientId={};ClientSecret={};TenantId={};Version=1.0;` |

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
