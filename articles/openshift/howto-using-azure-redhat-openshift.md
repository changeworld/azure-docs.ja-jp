---
title: Azure Red Hat OpenShift 4.3 クラスターを作成する | Microsoft Docs
description: Azure Red Hat OpenShift 4.3 でクラスターを作成する
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro、openshift、az aro、red hat、cli
ms.openlocfilehash: 423f09c135da51b8401c1933a4a271d0becd2c8f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349428"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Azure Red Hat OpenShift 4.3 クラスターの作成、アクセス、管理

> [!IMPORTANT]
> 現時点では、Azure Red Hat OpenShift 4.3 は、米国東部のプライベート プレビューでのみご利用いただけることにご注意ください。 プライベート プレビューの受け入れは招待によってのみ可能です。 この機能を有効にする前に、必ずサブスクリプションを登録してください。[Azure Red Hat OpenShift プライベート プレビューの登録](https://aka.ms/aro-preview-register)

> [!NOTE]
> プレビュー機能は、セルフサービスかつ現状のまま、現状利用可能な状態で提供され、サービス レベル アグリーメント (SLA) および限定保証からは除外されるものとします。 したがって、これらのフィーチャーはプロダクション環境での使用を目的としたものではありません。

## <a name="prerequisites"></a>前提条件

Azure Red Hat OpenShift 4.3 クラスターを作成するには、次のものが必要です。

- Azure CLI バージョン 2.0.72 以降
  
- 'az aro' 拡張機能

- 2 つの空のサブネットを含み、それぞれにネットワーク セキュリティ グループがアタッチされていない仮想ネットワーク。  クラスターは、これらのサブネットにデプロイされます。

- クラスター AAD アプリケーション (クライアント ID とシークレット) とサービス プリンシパル、または AAD アプリケーションとサービス プリンシパルを自動的に作成するための `az aro create` に対する十分な AAD アクセス許可。

- RP サービス プリンシパルとクラスター サービス プリンシパルそれぞれに、クラスター仮想ネットワークに対する共同作成者ロールが必要です。  仮想ネットワークに "ユーザー アクセス管理者" ロールがある場合、`az aro create` によって自動的にロールの割り当てが設定されます。

### <a name="install-the-az-aro-extension"></a>'az aro' 拡張機能のインストール
`az aro` 拡張機能を使用すると、Azure CLI を使用してコマンド ラインから直接 Azure Red Hat OpenShift クラスターを作成、アクセス、削除できます。

> [!Note] 
> `az aro` 拡張機能は現在プレビュー段階にあります。 今後のリリースで変更または削除される可能性があります。
> `az aro` 拡張機能のプレビューをオプトインするには、`Microsoft.RedHatOpenShift` リソース プロバイダーを登録する必要があります。
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Azure にログインします。

   ```console
   az login
   ```

2. 次のコマンドを実行して、`az aro` 拡張機能をインストールします。

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. ARO 拡張が登録されていることを確認します。

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>2 つの空のサブネットを含む仮想ネットワークを作成する

2 つの空のサブネットを含む仮想ネットワークを作成するには、次の手順に従います。

1. 次の変数を設定します。

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > オプションのプル シークレットを使用すると、クラスターは追加のコンテンツと共に Red Hat コンテナー レジストリにアクセスできます。
   >
   > https://cloud.redhat.com/openshift/install/azure/installer-provisioned に移動し、 *[プル シークレットのコピー]* をクリックして、プルシークレットにアクセスします。
   >
   > Red Hat アカウントにログインするか、お使いのビジネス メールアドレスを使用して新しい Red Hat アカウントを作成し、使用条件に同意する必要があります。
 

2. 自分のクラスターのリソース グループを作成します。

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. 仮想ネットワークを作成します。

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. 2 つの空のサブネットを自分の仮想ネットワークに追加します。

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. 仮想ネットワークとサブネット上の Private Link サービスのネットワーク ポリシーを無効にします。 これは、ARO サービスがクラスターにアクセスして管理するための要件です。

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>クラスターの作成

次のコマンドを実行して、クラスターを作成します。

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> 通常、クラスターの作成には約 35 分かかります。

## <a name="access-the-cluster-console"></a>クラスター コンソールにアクセスする

クラスター コンソールの URL (`https://console-openshift-console.apps.<random>.<location>.aroapp.io/` の形式) は、Azure Red Hat OpenShift 4.3 クラスター リソースの下にあります。 リソースを表示するには、次のコマンドを実行します。

```console
az aro list -o table
```

`kubeadmin` ユーザーを使用してクラスターにログインできます。  次のコマンドを実行して、`kubeadmin` ユーザーのパスワードを検索します。

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>クラスターの削除

クラスターを削除するには、次のコマンドを実行します。

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
