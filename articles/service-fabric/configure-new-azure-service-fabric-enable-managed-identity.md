---
title: Azure Service Fabric - マネージド ID をサポートする新しい Azure Service Fabric クラスターをデプロイする |Microsoft Docs
description: この記事では、マネージド ID を有効にして新しい Service Fabric クラスターを作成する方法について説明します
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 24fcdaf612a26109194524733e1fb9069dc664e0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965568"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support"></a>マネージド ID サポートを備えた新しい Azure Service Fabric クラスターを作成する

Azure Service Fabric アプリケーションのマネージド ID 機能にアクセスするには、まずクラスターでマネージド ID トークン サービスを有効にする必要があります。 このサービスは、マネージド ID を使用して Service Fabric アプリケーションの認証を実行し、アクセス トークンを代理で取得します。 サービスが有効になると、Service Fabric Explorer の左側のウィンドウの **[システム]** セクションに表示され、他のシステム サービスの横で **fabric:/System/ManagedIdentityTokenService** という名前で実行されます。

> [!NOTE]
> **マネージド ID トークン サービス**を有効にするには、Service Fabric ランタイム バージョン 6.5.658.9590 以降が必要です。  

## <a name="enable-the-managed-identity-token-service"></a>マネージド ID トークン サービスを有効にする 
クラスターの作成時にマネージド ID トークン サービスを有効にするには、Azure Resource Manager テンプレートで次のスニペットを使用できます。

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

このメッセージでデプロイが失敗する場合は、クラスターが、必要な Service Fabric バージョン (サポートされている最小ランタイムは 6.5 CU2) になっていないことを意味します。



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>次の手順
* [システム割り当てのマネージド ID を持つアプリケーションのデプロイ方法](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [ユーザー割り当てのマネージド ID を持つアプリケーションのデプロイ方法](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [サービス コードから Service Fabric アプリケーションのマネージド ID を活用する](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric アプリケーションに他の Azure リソースへのアクセスを許可する](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>関連記事
* Azure Service Fabric で[マネージド ID のサポート](./concepts-managed-identity.md)を確認します

* [既存の Azure Service Fabric クラスターでマネージド ID のサポートを有効にする](./configure-existing-cluster-enable-managed-identity-token-service.md)
