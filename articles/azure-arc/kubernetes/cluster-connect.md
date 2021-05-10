---
title: クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに接続する
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに安全に接続する
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450821"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに接続する

クラスター接続を使用すると、ファイアウォールでインバウンド ポートを有効にしなくても、Azure Arc 対応 Kubernetes クラスターに安全に接続できます。 Arc 対応 Kubernetes クラスターの `apiserver` にアクセスすると、次のシナリオが可能になります。
* 対話型デバッグとトラブルシューティングを有効にする。
* [カスタムの場所](custom-locations.md)やその上に作成されたその他のリソースの Azure サービスへのクラスター アクセスを提供する。

この機能の概要については、「[クラスター接続 - Azure Arc 対応 Kubernetes](conceptual-cluster-connect.md)」の記事を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件   

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](https://docs.microsoft.com/cli/azure/install-azure-cli)

- `connectedk8s` Azure CLI 拡張機能バージョン 1.1.0 以降をインストールします。

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    `connectedk8s` 拡張機能が既にインストールされている場合は、その拡張機能を最新バージョンに更新します。
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Azure Arc 対応 Kubernetes に接続された既存のクラスター。
    - クラスターをまだ接続していない場合は、[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に[お使いのエージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

- `kubeconfig` ファイルが問題のクラスターを指している状態で、マシン上で次のコマンドを実行して、任意の Azure Arc 対応 Kubernetes クラスターに対してクラスター接続を有効にします。

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- 送信アクセスのために、「[Kubernetes クラスターを Azure Arc に接続する](quickstart-connect-cluster.md#meet-network-requirements)」で示されているエンドポイントに加えて、下記に示すものも有効にします。

    | エンドポイント | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>使用

クラスター接続機能では、2 つの認証オプションがサポートされています。 
* Azure Active Directory (Azure AD) 
* サービス アカウント トークン

### <a name="option-1-azure-active-directory"></a>オプション 1: Azure Active Directory

1. `kubeconfig` ファイルがご自分の Kubernetes クラスターの `apiserver` を指している状態で、アクセスを必要とする Azure AD エンティティ (サービス プリンシパルまたはユーザー) への ClusterRoleBinding または RoleBinding を作成します。

    **ユーザーの場合:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Azure AD アプリケーションの場合:**

    1. お使いの Azure AD アプリケーションに関連付けられている `objectId` を取得します。

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. このクラスターにアクセスする必要がある Azure AD エンティティ (サービス プリンシパルまたはユーザー) への ClusterRoleBinding または RoleBinding を作成します。
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. 目的の Azure AD エンティティを使用して Azure CLI にログインした後、任意の場所から (クラスターを囲むファイアウォールの外側からでも) クラスターと通信するために必要なクラスター接続の `kubeconfig` を取得します。

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. `kubectl` を使用して、クラスターに要求を送信します。

    ```console
    kubectl get pods
    ```
    
    これで、`default` 名前空間の下にあるすべてのポッドの一覧が含まれている、クラスターからの応答が表示されます。

### <a name="option-2-service-account-bearer-token"></a>オプション 2: サービス アカウント ベアラー トークン

1. `kubeconfig` ファイルがご自分の Kubernetes クラスターの `apiserver` を指している状態で、任意の名前空間にサービス アカウントを作成します (次のコマンドを実行すると、既定の名前空間に作成されます)。

    ```console
    kubectl create serviceaccount admin-user
    ```

1. ClusterRoleBinding または RoleBinding を作成して、この[サービス アカウントにクラスターに対する適切なアクセス許可](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding)を付与します。

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. 次のコマンドを使用して、サービス アカウントのトークンを取得します

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. 任意の場所から (クラスターを囲むファイアウォールの外側からでも) クラスターと通信するために必要なクラスター接続の `kubeconfig` を取得します。

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. `kubectl` を使用して、クラスターに要求を送信します。

    ```console
    kubectl get pods
    ```

    これで、`default` 名前空間の下にあるすべてのポッドの一覧が含まれている、クラスターからの応答が表示されます。

## <a name="known-limitations"></a>既知の制限事項

Kubernetes クラスターへの要求を行うときに、使用する Azure AD エンティティが 200 を超えるグループに属している場合、これは既知の制限であるため、次のエラーが発生します。

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

このエラーを回避するには:
1. [サービス プリンシパル](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)を作成します。これは、200 を超えるグループのメンバーになる可能性が高くありません。
1. `az connectedk8s proxy` コマンドを実行する前に、サービス プリンシパルを使用して Azure CLI に[サインイン](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> ご自分のクラスターで [Azure AD RBAC](azure-rbac.md) を設定する