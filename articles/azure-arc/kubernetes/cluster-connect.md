---
title: クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに接続する
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに安全に接続する
ms.openlocfilehash: e585b67495aef1fb094d157c584c6744d993b135
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997915"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>クラスター接続を使用して Azure Arc 対応 Kubernetes クラスターに接続する

クラスター接続を使用すると、ファイアウォールでどの受信ポートも有効にすることなく、Azure Arc 対応 Kubernetes クラスターに安全に接続できます。 Azure Arc 対応 Kubernetes クラスターの `apiserver` にアクセスすると、次のシナリオが可能になります。
* 対話型デバッグとトラブルシューティングを有効にする。
* [カスタムの場所](custom-locations.md)やその上に作成されたその他のリソースの Azure サービスへのクラスター アクセスを提供する。

この機能の概要については、[クラスター接続と Azure Arc 対応 Kubernetes](conceptual-cluster-connect.md) に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件   

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](/cli/azure/install-azure-cli)

- `connectedk8s` Azure CLI 拡張機能バージョン 1.1.0 以降をインストールします。

    ```console
    az extension add --name connectedk8s
    ```
  
    `connectedk8s` 拡張機能が既にインストールされている場合は、その拡張機能を最新バージョンに更新します。
    
    ```console
    az extension update --name connectedk8s
    ```

- Azure Arc 対応 Kubernetes に接続された既存のクラスター。
    - クラスターをまだ接続していない場合は[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に[エージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

- 送信アクセスのために、「[Kubernetes クラスターを Azure Arc に接続する](quickstart-connect-cluster.md#meet-network-requirements)」で示されているエンドポイントに加えて、下記に示すものも有効にします。

    | エンドポイント | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com` | 443 |

- プレースホルダーを置き換えて下記のコマンドを実行し、このドキュメントで使用する環境変数を設定します。

    ```console
    CLUSTER_NAME=<cluster-name>
    RESOURCE_GROUP=<resource-group-name>
    ARM_ID_CLUSTER=$(az connectedk8s show -n $CLUSTER_NAME -g $RESOURCE_GROUP --query id -o tsv)
    ```


## <a name="enable-cluster-connect-feature"></a>クラスター接続機能を有効にする

`kubeconfig` ファイルが問題のクラスターを指し示しているマシンで次のコマンドを実行することで、任意の Azure Arc 対応 Kubernetes クラスターでクラスター接続を有効にできます。

```console
az connectedk8s enable-features --features cluster-connect -n $CLUSTER_NAME -g $RESOURCE_GROUP
```

## <a name="azure-active-directory-authentication-option"></a>Azure Active Directory 認証オプション

1. お使いの Azure AD エンティティに関連付けられている `objectId` を取得します。

    - Azure AD ユーザー アカウントの場合:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad signed-in-user show --query objectId -o tsv)
        ```

    - Azure AD アプリケーションの場合:

        ```console
        AAD_ENTITY_OBJECT_ID=$(az ad sp show --id <id> --query objectId -o tsv)
        ```

1. 適切なアクセス許可を持っている AAD エンティティを承認します。

    - クラスターでの認可チェックのために Kubernetes ネイティブの ClusterRoleBinding または RoleBinding を使用している場合は、直接アクセスのためにクラスターの `apiserver` を指し示す `kubeconfig` ファイルを使用して、このクラスターにアクセスする必要がある Azure AD エンティティ (サービス プリンシパルまたはユーザー) にマップされるバインドを作成できます。 例:
    
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=$AAD_ENTITY_OBJECT_ID
        ```

    - クラスターでの認可チェックのために Azure RBAC を使用している場合は、Azure AD エンティティにマップされる Azure ロールの割り当てを作成できます。 例:

        ```console
        az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee $AAD_ENTITY_OBJECT_ID --scope $ARM_ID_CLUSTER
        ```

## <a name="service-account-token-authentication-option"></a>サービス アカウント トークン認証オプション

1. `kubeconfig` ファイルがご自分の Kubernetes クラスターの `apiserver` を指している状態で、任意の名前空間にサービス アカウントを作成します (次のコマンドを実行すると、既定の名前空間に作成されます)。

    ```console
    kubectl create serviceaccount admin-user
    ```

1. ClusterRoleBinding または RoleBinding を作成して、この[サービス アカウントにクラスターでの適切なアクセス許可](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding)を付与します。 例:

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

## <a name="access-your-cluster"></a>クラスターにアクセスする

1. 使用される認証オプションに基づいてクラスターにアクセスするために必要な、クラスター接続ベースの kubeconfig を設定します。

    - Azure Active Directory 認証オプションを使用する場合は、目的の Azure AD エンティティを使用して Azure CLI にログインした後で、任意の場所から (クラスターを囲むファイアウォールの外側からでも) クラスターと通信するために必要なクラスター接続 `kubeconfig` を取得します。

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP
        ```

    - サービス アカウント認証オプションを使用する場合は、任意の場所からクラスターと通信するために必要なクラスター接続 `kubeconfig` を取得します。

        ```console
        az connectedk8s proxy -n $CLUSTER_NAME -g $RESOURCE_GROUP --token $TOKEN
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
1. [サービス プリンシパル](/cli/azure/create-an-azure-service-principal-azure-cli)を作成します。これは、200 を超えるグループのメンバーになる可能性が高くありません。
1. `az connectedk8s proxy` コマンドを実行する前に、サービス プリンシパルを使用して Azure CLI に[サインイン](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> ご自分のクラスターで [Azure AD RBAC](azure-rbac.md) を設定する
