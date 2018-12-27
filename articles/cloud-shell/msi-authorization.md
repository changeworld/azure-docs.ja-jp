---
title: Azure Cloud Shell で Azure リソースのマネージド ID を使用する | Microsoft Docs
description: Azure Cloud Shell の MSI でコードを認証する
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: fe77deeedc34bf769065e34ac2f81d631b0004d6
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352950"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Azure Cloud Shell で Azure リソースのマネージド ID を使用する

Azure Cloud Shell は、Azure リソースのマネージド ID による承認をサポートします。 これを使用して、安全に Azure サービスと通信するためにアクセス トークンを取得します。

## <a name="about-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID について
クラウド アプリケーションの構築時における一般的な課題は、クラウド サービスへの認証用のコードに必要な資格情報を安全に管理する方法です。 Cloud Shell では、スクリプトに必要な資格情報の Key Vault からの取得を認証する必要がある場合があります。

Azure リソースのマネージド ID は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

## <a name="acquire-access-token-in-cloud-shell"></a>Cloud Shell でアクセス トークンを取得する

次のコマンドを実行し、環境変数 `access_token` として MSI アクセス トークンを設定します。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>トークンの有効期限の処理

ローカル MSI サブシステムではトークンがキャッシュされます。 したがって、トークンは好きなだけ呼び出すことができます。また、次の場合にのみ、Azure AD の結果に対するネットワーク呼び出しが可能です。
- キャッシュにトークンがないため、キャッシュ ミスが発生した
- トークンの有効期限が切れている

コードでトークンをキャッシュする場合は、リソースがトークンの期限切れ示している場合のシナリオを処理できるよう準備する必要があります。

トークンのエラーを処理するには、[MSI アクセス トークンの使用に関する MSI ページ](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)を参照してください。

## <a name="next-steps"></a>次の手順
[MSI の詳細情報](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[MSI VM からアクセス トークンを取得する](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
