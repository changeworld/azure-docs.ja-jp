---
title: Azure Arc 対応 Kubernetes クラスター用の Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 対応 Kubernetes クラスターでの承認チェックに Azure RBAC を使用します
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304207"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Azure Active Directory と Azure Arc 対応 Kubernetes クラスターの統合

Kubernetes の [ClusterRoleBinding および RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) オブジェクト型は、Kubernetes でネイティブに承認を定義するのに役立ちます。 この機能を使用すると、Azure Active Directory と Azure のロールの割り当てを使用して、クラスターでの承認チェックを制御できます。 これは、Azure のロールの割り当てを使用すると、デプロイ、ポッド、サービスなどの Kubernetes オブジェクトの読み取り、書き込み、削除を行うことができるユーザーを、細かく制御できることを意味します

この機能の概念的な概要については、[Azure RBAC と Azure Arc 対応 Kubernetes](conceptual-azure-rbac.md) に関する記事を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、それにアップグレードします](https://docs.microsoft.com/cli/azure/install-azure-cli)

- `connectedk8s` Azure CLI 拡張機能バージョン 1.1.0 以降をインストールします

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    `connectedk8s` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新できます。 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Azure Arc 対応 Kubernetes に接続された既存のクラスター。
    - クラスターをまだ接続していない場合は[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に [エージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

> [!NOTE]
> この機能は、ユーザーがクラスターの `apiserver` にアクセスできない、Elastic Kubernetes Service や Google Kubernetes Engine などのクラウド プロバイダーのマネージド Kubernetes オファリングには設定できません。 Azure Kubernetes Service (AKS) クラスターの場合、この[機能はネイティブで利用可能](../../aks/manage-azure-rbac.md)であり、AKS クラスターを Azure Arc に接続する必要はありません。

## <a name="set-up-azure-ad-applications"></a>Azure AD アプリケーションを設定する

### <a name="create-server-application"></a>サーバー アプリケーションを作成する

1. 新しい Azure AD アプリケーションを作成し、その `appId` の値を取得します。これは、後の手順で `serverApplicationId` として使用します。

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. アプリケーション グループ メンバーシップ要求を更新します。

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. サービス プリンシパルを作成し、その `password` フィールドの値を取得します。この値は後ほどクラスターでこの機能を有効にするときに `serverApplicationSecret` として必要になります。

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. アプリケーション API のアクセス許可を付与します。

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * このステップは、Azure テナント管理者が行う必要があります。
    > * 運用環境でこの機能を使用する場合は、クラスターごとに異なるサーバー アプリケーションを作成することをお勧めします。

### <a name="create-client-application"></a>クライアント アプリケーションを作成する

1. 新しい Azure AD アプリケーションを作成し、その "appId" の値を取得します。これは、後の手順で `clientApplicationId` として使用します。

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. このクライアント アプリケーション用のサービス プリンシパルを作成します。

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. サーバー アプリケーション用の `oAuthPermissionId` を取得します。

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. クライアント アプリケーションに必要なアクセス許可を付与します。

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>サーバー アプリケーション用のロールの割り当てを作成する

要求を行っているユーザーが、要求の一部である Kubernetes オブジェクトで承認されているかどうかを確認するため、サーバー アプリケーションには `Microsoft.Authorization/*/read` アクセス許可が必要です。

1. 次のような内容で accessCheck.json という名前のファイルを作成します。

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    `<subscription-id>` は実際のサブスクリプション ID に置き換えます。

2. 次のコマンドを実行して、新しいカスタム ロールを作成します。

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. 上記のコマンドの出力から、`id` フィールドの値を保存します。これは、後の手順で `roleId` として使用します。

4. 上で作成したロールを使用して、担当者としてサーバー アプリケーションにロールの割り当てを作成します。

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>クラスターで Azure RBAC を有効にする

1. 次のコマンドを実行して、Arc 対応 Kubernetes クラスターで Azure RBAC を有効にします。

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. 上のコマンドを実行する前に、マシンにある `kubeconfig` ファイルで、Azure RBAC 機能を有効にするクラスターが確実に指定されているようにします。
    > 2. Azure RBAC ではなく Kubernetes ネイティブの ClusterRoleBinding オブジェクトと RoleBinding オブジェクトを使用して承認チェックを行う場合は、上のコマンドでユーザー名、メール アドレス、OID のコンマ区切りリストのための `--skip-azure-rbac-list` を使用します。

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>apiserver 仕様で競合回避モジュールが実行されていない汎用クラスターの場合:

1. クラスターのすべてのマスター ノードに SSH で接続して、次の手順を行います。

    1. 編集モードで `apiserver` マニフェストを開きます。
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. `volumes` に次の指定を追加します。
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. `volumeMounts` に次の指定を追加します。

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. 次の `apiserver` 引数を追加します。

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Kubernetes クラスターのバージョンが 1.19.0 以降の場合は、次の `apiserver argument` も設定する必要があります。

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. 保存してエディターを終了し、`apiserver` ポッドを更新します。


### <a name="for-a-cluster-created-using-cluster-api"></a>Cluster API を使用して作成されたクラスターの場合

1. 認証と承認の Webhook 構成ファイル `from the workload cluster` が含まれるガード シークレットをお使いのコンピューターにコピーします。

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. `azure-arc-guard-manifests.yaml` ファイルの `namespace` フィールドを、ワークロード クラスターを作成するためのカスタム リソースを適用している管理クラスター内の名前空間に変更します。

1. このマニフェストを適用します。

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. `kubectl edit kcp <clustername>-control-plane` を実行して `KubeadmControlPlane` オブジェクトを編集します。
    
    1. 次のスニペットを `files:` に追加します。
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. 次のスニペットを `apiServer:` -> `extraVolumes:` に追加します。
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. 次のスニペットを `apiServer:` -> `extraArgs:` に追加します。
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. 保存して終了し、`KubeadmControlPlane` オブジェクトを更新します。 これらの変更がワークロード クラスターで実現されるまで待ちます。


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>ユーザーがクラスターにアクセスするためのロールの割り当てを作成する

Azure Arc 対応 Kubernetes リソースの所有者は、組み込みロールまたはカスタム ロールを使用して、他のユーザーに Kubernetes クラスターへのアクセスを許可できます。

### <a name="built-in-roles"></a>組み込みのロール

| ロール | 説明 |
|---|---|
| [Azure Arc Kubernetes ビューアー](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 このロールでは、シークレットの表示は許可されません。 これは、シークレットに対する `read` アクセス許可によって名前空間の `ServiceAccount` 資格情報にアクセスできるようになり、さらにその `ServiceAccount` (特権エスカレーションの形式で) を使用して API アクセスが許可されるようになるためです。 |
| [Azure Arc Kubernetes ライター](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 このロールでは、ロールまたはロールのバインドを表示または変更することはできません。 ただし、このロールを使用すると、シークレットにアクセスし、名前空間内の任意の `ServiceAccount` としてポッドを実行できるので、名前空間内の任意の `ServiceAccount` の API アクセス レベルを取得するために使用できます。 |
| [Azure Arc Kubernetes 管理者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | 管理者アクセスを許可します。 RoleBinding を使用して名前空間内で付与されることが意図されています。 RoleBinding で使用された場合、名前空間内でロールおよびロール バインドを作成する能力など、名前空間内のほとんどのリソースへの読み取りおよび書き込みアクセスが許可されます。 このロールでは、リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| [Azure Arc Kubernetes クラスター管理者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | 任意のリソースに対して任意のアクションを実行できるスーパー ユーザー アクセスが許可されます。 ClusterRoleBinding で使用すると、クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 RoleBinding で使用すると、ロール バインドの名前空間内のすべてのリソース (名前空間自体を含む) を完全に制御できます。|

Azure portal のクラスター リソースの `Access Control (IAM)` ブレードで、Arc 対応 Kubernetes クラスターを対象とするロールの割り当てを作成できます。 次に示すように、Azure CLI のコマンドを使用することもできます。

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

ここで、`AZURE-AD-ENTITY-ID` には、ユーザー名 (testuser@mytenant.onmicrosoft.com など) や、サービス プリンシパルの `appId` を指定できます。

クラスター内の特定の名前空間を対象としたロールの割り当てを作成する別の例を次に示します。

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> クラスターを対象とするロールの割り当ては、Azure portal または CLI を使用して作成できますが、名前空間を対象とするロールの割り当ては CLI を使用してのみ作成できます。

### <a name="custom-roles"></a>カスタム ロール

ロールの割り当てで使用する独自のロールの定義を作成することもできます。

ユーザーにデプロイの読み取りだけを許可するロールの定義の例を次に示します。 詳細については、[ロールの定義の作成に使用できるデータ操作の完全な一覧](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)を参照してください。

次の JSON オブジェクトを custom-role.json という名前のファイルにコピーします。 `<subscription-id>` プレースホルダーは、実際のサブスクリプション ID に置き換えます。 次のカスタム ロールを使用すると、いずれかのデータ操作を使用して、ロールの割り当てが作成されるスコープ (クラスター、名前空間) 内のすべてのデプロイを表示できます。

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. `custom-role.json` を保存したフォルダーから次のコマンドを実行することで、ロールの定義を作成します。

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. このカスタム ロールの定義を使用してロールの割り当てを作成します。

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>ユーザー資格情報を使用して kubectl を構成する

クラスターにアクセスするために必要な `kubeconfig` ファイルを取得するには、次の 2 つの方法があります。
1. Azure Arc 対応 Kubernetes クラスターの[クラスター接続](cluster-connect.md) 機能 (`az connectedk8s proxy`) を使用します。
1. クラスター管理者が、他のすべてのユーザーと `kubeconfig` ファイルを共有します。

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>クラスター接続機能を使用してクラスターにアクセスする場合

次のコマンドを実行して、プロキシ プロセスを開始します。

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

プロキシ プロセスの実行後、コンソールで別のタブを開いて、[クラスターへの要求の送信を開始](#sending-requests-to-cluster)できます。

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>クラスター管理者と `kubeconfig` ファイルを共有している場合 

1. 次のコマンドを実行して、ユーザーの資格情報を設定します。

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 前に作成した `kubeconfig` ファイルを開きます。 `contexts` で、クラスターに関連付けられているコンテキストが、前のステップで作成したユーザー資格情報を指していることを確認します。

1. ユーザー構成に **config-mode** の設定を追加します。
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>要求をクラスターに送信する

1. 任意の `kubectl` コマンドを実行します。 次に例を示します。
  * `kubectl get nodes` 
  * `kubectl get pods`

1. ブラウザー ベースの認証を求めるメッセージが表示されたら、デバイス ログイン URL `https://microsoft.com/devicelogin` をコピーし、Web ブラウザーで開きます。

1. コンソールに出力されたコードを入力し、ターミナルのコードをコピーして、デバイス認証の入力プロンプトに貼り付けます。

1. ユーザー名 (testuser@mytenant.onmicrosoft.com) とそれに関連付けられているパスワードを入力します。

1. 次のようなエラー メッセージが表示される場合は、要求したリソースへのアクセスを承認されていないことを意味します。

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    管理者は、このユーザーにリソースへのアクセスを許可する新しいロールの割り当てを作成する必要があります。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クラスター接続](cluster-connect.md)を使用してクラスターに安全に接続する