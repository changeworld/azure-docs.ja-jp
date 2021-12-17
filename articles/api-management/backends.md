---
title: Azure API Management バックエンド | Microsoft Docs
description: API Management のカスタム バックエンドについて説明します
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 09/21/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 008c5624f621f8598963a8f94261a9e369d0ca0e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647121"
---
# <a name="backends-in-api-management"></a>API Management のバックエンド

API Management の "*バックエンド*" (つまり "*API バックエンド*") は、フロントエンド API とその操作を実装する HTTP サービスです。

特定の API をインポートすると、API Management によって API バックエンドが自動的に構成されます。 たとえば次の API をインポートするときに、API Management によってバックエンドが構成されます。
* [OpenAPI の仕様](import-api-from-oas.md)。
* [SOAP API](import-soap-api.md)。
* Azure リソース (HTTP によってトリガーされる [Azure 関数アプリ](import-function-app-as-api.md)や[ロジック アプリ](import-logic-app-as-api.md)など)。

API Management が API バックエンドとしての使用をサポートする Azure リソースは他にもあります。その例を次に示します。
* [Service Fabric クラスター](how-to-configure-service-fabric-backend.md)。
* カスタム サービス。 

カスタム バックエンドを使用するには、バックエンド サービスへの要求の資格情報を承認したり、API 操作を定義したりするための追加の構成が必要になります。 カスタム バックエンドの構成と管理は、Azure portal で、または Azure API やツールを使用して行います。

バックエンドを作成すると、お使いの API でバックエンド URL を参照できるようになります。 受信 API 要求をその API の既定のバックエンドではなく、カスタム バックエンドにリダイレクトするには、[`set-backend-service`](api-management-transformation-policies.md#SetBackendService) ポリシーを使用します。

## <a name="benefits-of-backends"></a>バックエンドの利点

カスタム バックエンドには、次のようないくつかの利点があります。

* バックエンド サービスに関する情報を抽象化し、API 間での再利用性を促進し、ガバナンスを強化します。  
* 既存の API で変換ポリシーを構成することによって簡単に使用できます。
* ヘッダーまたはクエリ パラメーターの認証用に[名前付きの値](api-management-howto-properties.md)が構成されている場合に、API Management の機能を利用して Azure Key Vault でシークレットを維持できます。

## <a name="limitation"></a>制限事項

**Developer** レベルと **Premium** レベルの場合、ゲートウェイのエンドポイントの URL とバックエンドの URL が同じであるときに、[内部仮想ネットワーク](api-management-using-with-internal-vnet.md)にデプロイされた API Management インスタンスから HTTP 500 `BackendConnectionFailure` エラーがスローされることがあります。 この制限に遭遇した場合は、テクニカル コミュニティ ブログの[内部仮想ネットワーク モードにおける自己連鎖 API Management 要求の制限](https://techcommunity.microsoft.com/t5/azure-paas-blog/self-chained-apim-request-limitation-in-internal-virtual-network/ba-p/1940417)に関する記事の手順に従ってください。 

## <a name="next-steps"></a>次のステップ

* Azure portal を使用して [Service Fabric バックエンド](how-to-configure-service-fabric-backend.md)を設定します。
* バックエンドは、API Management [REST API](/rest/api/apimanagement)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)、または [Azure Resource Manager テンプレート](../service-fabric/service-fabric-tutorial-deploy-api-management.md)を使用して構成することもできます。

