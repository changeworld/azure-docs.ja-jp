---
title: サービス プリンシパルによる Azure Container Registry 認証
description: Azure Active Directory サービス プリンシパルを使用して、プライベート コンテナー レジストリ内のイメージへのアクセスを提供します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 70ca1b88c653601e077c55a847c13f67efc3e300
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754205"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>サービス プリンシパルによる Azure Container Registry 認証

Azure Active Directory (Azure AD) サービス プリンシパルを使って、コンテナー レジストリへのコンテナー イメージの `docker push` アクセスと `pull` アクセスを提供できます。 サービス プリンシパルを使うことで、「ヘッドレス」のサービスとアプリケーションへのアクセスを提供できます。

## <a name="what-is-a-service-principal"></a>サービス プリンシパルとは

Azure AD の*サービス プリンシパル*は、サブスクリプション内の Azure リソースへのアクセスを提供します。 サービス プリンシパルはサービスのユーザー ID と考えることができます。ここで「サービス」は、リソースへのアクセスが必要なアプリケーション、サービス、またはプラットフォームです。 指定したそれらのリソースのみをスコープとするアクセス権を持つサービス プリンシパルを構成できます。 その後、サービス プリンシパルの資格情報を使用してそれらのリソースにアクセスするようにアプリケーションまたはサービスを構成します。

Azure Container Registry のコンテキストでは、Azure 内のプライベート レジストリに対するプル、プッシュとプル、またはその他のアクセス許可を持つ Azure AD サービス プリンシパルを作成できます。 完全な一覧については、[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)に関するページを参照してください。

## <a name="why-use-a-service-principal"></a>サービス プリンシパルを使う理由

Azure AD サービス プリンシパルを使うことで、スコープを設定したプライベート コンテナー レジストリへのアクセスを提供できます。 各アプリケーションや各サービスに異なるサービス プリンシパルを作成し、それぞれにレジストリへの調整済みのアクセス権を持たせることができます。 また、サービスやアプリケーションの間での資格情報の共有を避けることができるため、資格情報を交換したり、選択したサービス プリンシパル (および対象とするアプリケーション) のみのアクセスを取り消したりできます。

たとえば、Web アプリケーションではイメージの `pull` アクセスのみを提供するサービス プリンシパルを使えますが、ビルド システムでは `push` と `pull` 両方のアクセスを提供するサービス プリンシパルを使えます。 アプリケーション開発の所有者が変わった場合は、ビルド システムに影響を与えずにそのサービス プリンシパルの資格情報を交換することができます。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、**ヘッドレス シナリオ**でレジストリへのアクセスを提供する際に使う必要があります。 つまり、自動的にまたはそれ以外の無人の方法でコンテナー イメージをプッシュまたはプルする必要があるすべてのアプリケーション、サービス、またはスクリプトが対象です。

コンテナー イメージを開発用ワークステーションに手動でプルするときなど、レジストリへの個別アクセスでは、代わりに各自の[Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad) を使ってレジストリにアクセスする必要があります ([az acr login][az-acr-login] を使うなど)。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>サンプルのスクリプト

Azure CLI の以前のサンプル スクリプトを GitHub 上で検索できます。各バージョンのAzure PowerShell についても同様です。

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>次の手順

コンテナー レジストリへのアクセスが許可されたサービス プリンシパルを持っている場合は、アプリケーションおよびサービスでその資格情報を使ってレジストリと通信できます。

サービス プリンシパルの資格情報を使う個々のアプリケーションの構成はこの記事では取り上げませんが、一部の特定のサービスとプラットフォームの手順については、以下をご覧ください。

* [Azure Kubernetes Service (AKS) から Azure Container Registry の認証を受ける](container-registry-auth-aks.md)
* [Azure Container Instances (ACI) から Azure Container Registry の認証を受ける](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login