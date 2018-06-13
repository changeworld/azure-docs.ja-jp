---
title: Azure デプロイ後タスクの OpenShift | Microsoft Docs
description: OpenShift クラスターがデプロイされた後の追加タスクについて説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: bdfd075b9438ee12e940f3ec4fddebf467c93ca8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796161"
---
# <a name="post-deployment-tasks"></a>デプロイ後タスク

OpenShift クラスターをデプロイした後に、追加の項目を構成することができます。 この記事では、次のトピックについて説明します。

- Azure Active Directory (Azure AD) を使用してシングル サインオンを構成する方法
- OpenShift を監視する Log Analytics を構成する方法
- メトリックとログを構成する方法

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Azure Active Directory を使用してシングル サインオンを構成する

認証に Azure Active Directory を使用するには、まず Azure AD アプリの登録を作成する必要があります。 このプロセスには、アプリの登録の作成とアクセス許可の構成という 2 つの手順があります。

### <a name="create-an-app-registration"></a>アプリの登録を作成する

以下の手順では、Azure CLI を使用してアプリの登録を作成し、GUI (Portal) を使用してアクセス許可を設定します。 アプリの登録を作成するには、次の 5 つの情報が必要です。

- 表示名: アプリの登録名 (例: OCPAzureAD)
- ホーム ページ: OpenShift コンソール URL (例: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 識別子 URI: OpenShift コンソール URL (例: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 応答 URL: マスター パブリック URL とアプリの登録名 (例: https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- パスワード: セキュリティで保護されたパスワード (強力なパスワードを使用する)

次の例では、上記の情報を使用してアプリの登録を作成します。

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

コマンドが成功すると、次のような JSON が出力されます。

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

後の手順のために、コマンドから返された appId プロパティを書き留めます。

Azure Portal で次の操作を行います。

1.  **[Azure Active Directory]** > **[アプリの登録]** の順に選択します。
2.  アプリの登録を検索します (例: OCPAzureAD)。
3.  結果のアプリの登録をクリックします。
4.  **[設定]** の **[必要なアクセス許可]** を選択します
5.  **[必要なアクセス許可]** の **[追加]** を選択します。

  ![アプリケーションの登録](media/openshift-post-deployment/app-registration.png)

6.  [手順 1: API の選択]、**[Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)]** の順にクリックします。 下部にある **[選択]** をクリックします。

  ![[アプリの登録] の [API の選択]](media/openshift-post-deployment/app-registration-select-api.png)

7.  [手順 2: アクセス許可の選択] で、**[委任されたアクセス許可]** の **[サインインとユーザー プロファイルの読み取り]** を選択し、**[選択]** をクリックします。

  ![[アプリの登録] のアクセス](media/openshift-post-deployment/app-registration-access.png)

8.  **[完了]** を選択します。

### <a name="configure-openshift-for-azure-ad-authentication"></a>Azure AD 認証用に OpenShift を構成する

認証プロバイダーとして Azure AD を使用するように OpenShift を構成するには、すべてのマスター ノードで /etc/origin/master/master-config.yaml ファイルを編集する必要があります。

テナント ID を確認するには、次の CLI コマンドを使用します。

```azurecli
az account show
```

yaml ファイルで、次の行を探します。

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

これらの行の真上に次の行を挿入します。

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

テナント ID を確認するには、次の CLI コマンドを使用します。```az account show```

すべてのマネージャーで OpenShift マスター サービスを再起動します。

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**複数のマスターがある OpenShift Container Platform (OCP)**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**マスターが 1 つの OpenShift Container Platform**

```bash
sudo systemctl restart atomic-openshift-master
```

OpenShift Console に、認証のオプションが 2 つ表示されるようになります (htpasswd_auth と [アプリの登録])。

## <a name="monitor-openshift-with-log-analytics"></a>Log Analytics を使用して OpenShift を監視する

Log Analytics を使用して OpenShift を監視するには、OMS Agent を VM ホストにインストールする方法か、OMS Container を使用する方法のいずれかを使用できます。 この記事では、OMS Container のデプロイ手順について説明します。

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>Log Analytics 用の OpenShift プロジェクトを作成してユーザー アクセスを設定する

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>デーモン セットの yaml ファイルを作成する

ocp-omsagent.yml というファイルを作成します。

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>シークレット yaml ファイルを作成する

シークレット yaml ファイルを作成するには、Log Analytics ワークスペース ID と Log Analytics ワークスペース共有キーという 2 つの情報が必要です。 

ocp-secret.yml ファイルの例を次に示します。 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

wsid_data を、Base64 でエンコードされた Log Analytics ワークスペース ID で置き換えます。 key_data を、Base64 でエンコードされた Log Analytics ワークスペース共有キーで置き換えます。

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>シークレットとデーモン セットを作成する

シークレット ファイルをデプロイします。

```bash
oc create -f ocp-secret.yml
```

OMS エージェントのデーモン セットをデプロイします。

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>メトリックとログを構成する

OpenShift Container Platform 用の Azure Resource Manager テンプレートには、メトリックとログを有効にするための入力パラメーターが用意されています。 OpenShift Container Platform Marketplace Offer および OpenShift Origin Resource Manager テンプレートには用意されていません。

OCP Resource Manager テンプレートを使用し、インストール時にメトリックとログを有効にしなかった場合、または OCP Marketplace プランを使用した場合、後で簡単に有効にすることができます。 OpenShift Origin Resource Manager テンプレートを使用している場合、事前の作業がいくつか必要です。

### <a name="openshift-origin-template-pre-work"></a>OpenShift Origin テンプレートの事前の作業

1. ポート 2200 を使用した最初のマスター ノードへの SSH

   例:

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. /etc/ansible/hosts file を編集し、ID プロバイダー セクション (# Enable HTPasswdPasswordIdentityProvider) の後に次の行を追加します。

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

3. $ROUTING を、同じ /etc/ansible/hosts ファイルの openshift_master_default_subdomain オプションに使用されている文字列で置き換えます。

### <a name="azure-cloud-provider-in-use"></a>使用中の Azure クラウド プロバイダー

最初のマスター ノード (Origin) または要塞ノード (OCP) で、デプロイ中に指定された資格情報を使用して SSH を実行します。 次のコマンドを発行します。

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>使用中ではない Azure クラウド プロバイダー

最初のマスター ノード (Origin) または要塞ノード (OCP) で、デプロイ中に指定された資格情報を使用して SSH を実行します。 次のコマンドを発行します。

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>次の手順

- [OpenShift Container Platform の概要](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [OpenShift Origin の概要](https://docs.openshift.org/latest/getting_started/index.html)
