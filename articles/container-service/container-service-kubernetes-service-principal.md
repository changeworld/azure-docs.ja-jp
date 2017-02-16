---
title: "Azure Kubernetes クラスターのサービス プリンシパル | Microsoft Docs"
description: "Kubernetes を使用して Azure Container Service クラスターの Azure Active Directory サービス プリンシパルを作成および管理します"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 24e12e4606a5ec4fabf7046fe9847123033bb70a
ms.openlocfilehash: 073a9e66ac68643b27ecdd44a4ecac3ad79ec218


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service の Kubernetes クラスターの Azure Active Directory サービス プリンシパルについて



Azure Container Service で Kubernetes を使用するには、Azure API と対話するためのサービス アカウントとして [Azure Active Directory サービス プリンシパル](../active-directory/active-directory-application-objects.md)が必要です。 サービス プリンシパルは、[ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md)やレイヤー 4 の [Azure Load Balancer](../load-balancer/load-balancer-overview.md) などのリソースを動的に管理するために必要です。

この記事では、Kubernetes クラスターのサービス プリンシパルを指定するためのさまざまなオプションを紹介します。 たとえば、[Azure CLI 2.0 (プレビュー)](https://docs.microsoft.com/cli/azure/install-az-cli2) をインストールしてセットアップした場合は、[`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) コマンドを実行して、Kubernetes クラスターとサービス プリンシパルを同時に作成できます。

> [!NOTE]
> Azure Container Service での Kubernetes のサポートは、現在はプレビューの段階です。


## <a name="requirements-for-the-service-principal"></a>サービス プリンシパルの要件

Azure Container Service の Kubernetes クラスターの Azure Active Directory サービス プリンシパルに対する要件は次のとおりです。 

* **スコープ** - クラスターがデプロイされる Azure サブスクリプション

* **ロール** - **共同作成者**

* **クライアント シークレット** - パスワードである必要があります。 現時点では、証明書の認証用に設定されたサービス プリンシパルを使用することはできません。

> [!NOTE]
> すべてのサービス プリンシパルは、Azure Active Directory アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure Active Directory アプリケーション名に関連付けることができます。
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Kubernetes クラスターのサービス プリンシパルのオプション

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>オプション 1: サービス プリンシパルのクライアント ID とクライアント シークレットを渡す

Kubernetes クラスターを作成するときに、既存のサービス プリンシパルの**クライアント ID** (アプリケーション ID を意味する `appId` とよく呼ばれます) と**クライアント シークレット** (`password`) をパラメーターとして指定します。 既存のサービス プリンシパルを使用する場合は、前のセクションの要件を満たしていることを確認してください。 サービス プリンシパルを作成する必要がある場合は、この記事の後半の「[サービス プリンシパルの作成](#create-a-service-principal-in-azure-active-directory)」を参照してください。

これらのパラメーターは、ポータル、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell を使用して [Kubernetes クラスターをデプロイする](./container-service-deployment.md)ときに指定できます。

>[!TIP] 
>**クライアント ID** を指定するときは、サービス プリンシパルの `ObjectId` ではなく `appId` を使用してください。
>

次の例では、[Resource Manager モード](../xplat-cli-connect.md)の [Azure CLI](../xplat-cli-install.md) でパラメーターを渡す 1 つの方法を示しています。 この例では、[Kubernetes クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)を使用します。

1. GitHub からテンプレート パラメーター ファイルの azuredeploy.parameters.json を[ダウンロード](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json)します。

2. サービス プリンシパルを指定するには、ファイルの `servicePrincipalClientId` と `servicePrincipalClientSecret` に値を入力します  (また、`dnsNamePrefix` と `sshRSAPublicKey` に独自の値を指定する必要もあります。 後者は、クラスターにアクセスするための SSH 公開キーです)。ファイルを保存します。

    ![サービス プリンシパルのパラメーターを渡す](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. 次のコマンドを実行します。その際、`-e` パラメーターを使用して、azuredeploy.parameters.json ファイルへのパスを設定します。 このコマンドにより、`myResourceGroup` と呼ばれる既存のリソース グループにクラスターがデプロイされます。

    ```CLI
    azure group deployment create -n myClusterName -g myResourceGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" -e azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>オプション 2: Azure CLI 2.0 プレビューでクラスターを作成するときにサービス プリンシパルを生成する

[Azure CLI 2.0 (プレビュー)](https://docs.microsoft.com/cli/azure/install-az-cli2) をインストールしてセットアップした場合は、[`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) コマンドを実行して、[クラスターを作成](./container-service-create-acs-cluster-cli.md)できます。

他の Kubernetes クラスター作成オプションと同様に、`az acs create` を実行するときに、既存のサービス プリンシパルのパラメーターを指定できます。 ただし、これらのパラメーターを省略すると、Azure Container Service によってサービス プリンシパルが自動的に作成されます。 これは、デプロイ中に透過的に行われます。 

次のコマンドは、Kubernetes クラスターを作成し、SSH キーとサービス プリンシパル資格情報の両方を生成します。

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory にサービス プリンシパルを作成する

Kubernetes クラスターで使用するために Azure Active Directory にサービス プリンシパルを作成する場合、Azure にはいくつかの方法が用意されています。 

以下のコマンド例では、[Azure CLI 2.0 (プレビュー)](https://docs.microsoft.com/cli/azure/install-az-cli2) でこの操作を行う方法を示しています。 代わりに、[Azure コマンド ライン インターフェイス](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)、[Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)、または[クラシック ポータル](../azure-resource-manager/resource-group-create-service-principal-portal.md)を使用してサービス プリンシパルを作成することもできます。

> [!IMPORTANT]
> この記事で前に説明したサービス プリンシパルの要件を必ず確認してください。
>

```console
az login

az account set --subscription="mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

次のような出力が返されます (ここで示されている出力は編集されています)。

![サービス プリンシパルの作成](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

強調表示されているのは、クラスターのデプロイでサービス プリンシパルのパラメーターとして使用する**クライアント ID** (`appId`) と**クライアント シークレット** (`password`) です。


サービス プリンシパルを確認するには、新しいシェルを開いて次のコマンドを実行します。その際、`appId`、`password`、`tenant` は置き換えます。

```console 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>追加の考慮事項


* サービス プリンシパルの**クライアント ID** を指定する場合、この記事で示したように `appId` の値を使用するか、対応するサービス プリンシパル `name` (例: `https://www.contoso.org/example`) を使用することができます。

* `az acs create` コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の ~/.azure/acsServicePrincipal.json ファイルに書き込まれます。

* Kubernetes クラスター内のマスター VM とノード VM では、サービス プリンシパルの資格情報が /etc/kubernetes/azure.json ファイルに格納されます。

## <a name="next-steps"></a>次のステップ

* コンテナー サービス クラスターで [Kubernetes を使ってみます](container-service-kubernetes-walkthrough.md)。



<!--HONumber=Dec16_HO3-->


