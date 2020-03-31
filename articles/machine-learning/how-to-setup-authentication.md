---
title: 認証の設定
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、さまざまなリソースとワークフローの認証を設定して構成する方法について説明します。 サービス内で認証を構成して使用するには、開発またはテストの目的で単純な UI ベースの認証を使用する方法から、完全な Azure Active Directory サービス プリンシパル認証を使用する方法まで、幅広い複数の方法があります。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79237027"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning のリソースとワークフローの認証を設定する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning でさまざまなリソースとワークフローの認証を設定して構成する方法について説明します。 サービスに対して認証するには、開発またはテストの目的で単純な UI ベースの認証を使用する方法から、完全な Azure Active Directory サービス プリンシパル認証を使用する方法まで、幅広い複数の方法があります。 この記事では、Web サービス認証の動作の違いについて説明し、Azure Machine Learning REST API に対して認証する方法についても説明します。

次のタスクの実行方法について説明します。

* テスト/開発用の対話型 UI 認証の使用
* サービス プリンシパル認証の設定
* ワークスペースに対する認証
* Azure Machine Learning REST API の OAuth 2.0 ベアラー型トークンの取得
* Web サービス認証の理解

Azure Machine Learning 内のセキュリティと認証の一般的な概要については、[概念に関する記事](concept-enterprise-security.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)を作成します。
* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) を使用します。

## <a name="interactive-authentication"></a>対話型認証

このサービスのドキュメントに記載されているほとんどの例では、Jupyter Notebook の対話型認証をテストおよびデモンストレーションのための簡単な方法として使用しています。 これは、構築中の内容をテストするための簡易な方法です。 UI ベースの認証フローが自動的に要求される関数呼び出しが 2 つあります。

`from_config()` 関数を呼び出すと、プロンプトが表示されます。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 関数は、ワークスペースの接続情報を含む JSON ファイルを検索します。 また、対話型認証が要求される `Workspace` コンストラクターを使用して、接続の詳細を明示的に指定することもできます。 どちらの呼び出しも同等です。

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

複数のテナントにアクセスできる場合は、クラスをインポートし、ターゲットとするテナントを明示的に定義する必要があります。 `InteractiveLoginAuthentication` のコンストラクターを呼び出すと、上記の呼び出しと同様にログインするよう求められます。

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

対話型認証は、テストと学習に便利ですが、自動化またはヘッドレス ワークフローの構築には役立ちません。 SDK を使用する自動化されたプロセスには、サービス プリンシパル認証の設定が最適なアプローチです。

## <a name="set-up-service-principal-authentication"></a>サービス プリンシパル認証の設定

このプロセスは、特定のユーザー ログインから分離された認証を有効にするために必要です。これにより、自動化されたワークフローで Azure Machine Learning Python SDK に対して認証を行うことができます。 サービス プリンシパル認証では、[REST API に対して認証を行う](#azure-machine-learning-rest-api-auth)こともできます。

サービス プリンシパル認証を設定するには、まず Azure Active Directory でアプリの登録を作成してから、アプリのロールベースのアクセス権を ML ワークスペースに付与します。 この設定を完了する最も簡単な方法は、Azure portal で [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を使用することです。 ポータルにログインしたら、ページの右上にある名前の近くの [`>_`] アイコンをクリックして、シェルを開きます。

Azure アカウントで以前に Cloud Shell を使用していない場合は、書き込まれたファイルを格納するためのストレージ アカウント リソースを作成する必要があります。 一般に、このストレージ アカウントはごくわずかな月額料金のみで利用できます。 また、以前に Machine Learning 拡張機能を使用していない場合は、次のコマンドでそれをインストールします。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 次の手順を実行するには、サブスクリプションの管理者である必要があります。

次に、次のコマンドを実行して、サービス プリンシパルを作成します。 名前を付けてください (この例では **ml-auth**)。

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

出力は次のような JSON になります。 この記事の他の手順で必要になるため、`clientId`、`clientSecret`、`tenantId` の各フィールドをメモしておきます。

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

次に、次のコマンドを実行して、先ほど作成したサービス プリンシパルの詳細を取得します。このとき、上記の `clientId` 値を `--id` パラメーターへの入力として使用します。

```azurecli-interactive
az ad sp show --id your-client-id
```

次に、コマンドからの JSON 出力の簡略化された例を示します。 次の手順で値が必要になるため、`objectId` フィールドをメモしておきます。

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

次に、次のコマンドを使用して、サービス プリンシパルのアクセス権を Machine Learning ワークスペースに割り当てます。 `-w` と `-g` のパラメーターに、それぞれワークスペース名とリソース グループ名が必要になります。 `--user` パラメーターには、前の手順の `objectId` 値を使用します。 `--role` パラメーターでは、サービス プリンシパルのアクセス ロールを設定でき、通常は **owner** または **contributor** を使用します。 どちらにもコンピューティング クラスターやデータストアなどの既存のリソースへの書き込みアクセス権がありますが、これらのリソースをプロビジョニングできるのは **owner** だけです。 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

この呼び出しでは出力は生成されませんが、これでワークスペースに対してサービス プリンシパル認証が設定されました。

## <a name="authenticate-to-your-workspace"></a>ワークスペースに対する認証

サービス プリンシパル認証が有効になったため、ユーザーとして物理的にログインしなくても、SDK でワークスペースに対して認証を行うことができます。 `ServicePrincipalAuthentication` クラス コンストラクターを使用し、前の手順で取得した値をパラメーターとして使用します。 `tenant_id` パラメーターは上記の `tenantId` にマップされ、`service_principal_id` は `clientId` にマップされ、`service_principal_password` は `clientSecret` にマップされます。

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp` 変数は、SDK で直接使用する認証オブジェクトを保持するようになりました。 一般に、次のコードに示すように、上記で使用した ID/シークレットを環境変数に格納することをお勧めします。

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python で実行され、SDK を主に使用する自動化されたワークフローの場合は、ほとんどの場合、このオブジェクトをそのまま使用して認証を行うことができます。 次のコードでは、作成した auth オブジェクトを使用してワークスペースに対して認証を行います。

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API 認証

上記の手順で作成したサービス プリンシパルを使用して、Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/) に対する認証を行うこともできます。 Azure Active Directory の[クライアント資格情報付与フロー](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)を使用します。これにより、自動化されたワークフローでヘッドレス認証に対するサービス間の呼び出しが許可されます。 これらの例は、Python と Node.js の両方で [ADAL ライブラリ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)と共に実装されていますが、OpenID Connect 1.0 をサポートする任意のオープンソース ライブラリを使用することもできます。 

> [!NOTE]
> MSAL js は ADAL より新しいライブラリですが、MSAL.js によってクライアント資格情報を使用したサービス間認証を行うことはできません。これは、主に、特定のユーザーに関連付けられた対話型/UI 認証を目的とするクライアント側ライブラリであるためです。 次に示すように、ADAL を使用して、REST API で自動化されたワークフローを構築することをお勧めします。

### <a name="nodejs"></a>Node.js

Node.js を使用して認証トークンを生成するには、次の手順に従います。 現在の環境で、`npm install adal-node` を実行します。 次に、前の手順で作成したサービス プリンシパルの `tenantId`、`clientId`、および `clientSecret` を、次のスクリプトで一致する変数の値として使用します。

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

変数 `tokenResponse` は、トークンと関連メタデータ (有効期限など) を含むオブジェクトです。 トークンは 1 時間有効ですが、同じ呼び出しを再度実行して新しいトークンを取得することによって更新できます。 次に、応答のサンプルを示します。

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

`accessToken` プロパティを使用して、認証トークンをフェッチします。 トークンを使用して API を呼び出す方法の例については、[REST API のドキュメント](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)を参照してください。

### <a name="python"></a>Python 

Python を使用して認証トークンを生成するには、次の手順に従います。 現在の環境で、`pip install adal` を実行します。 次に、前の手順で作成したサービス プリンシパルの `tenantId`、`clientId`、および `clientSecret` を、次のスクリプトで適切な変数の値として使用します。

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

変数 `token_response` は、トークンと関連メタデータ (有効期限など) を含むディクショナリです。 トークンは 1 時間有効ですが、同じ呼び出しを再度実行して新しいトークンを取得することによって更新できます。 次に、応答のサンプルを示します。

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]` を使用して、認証トークンをフェッチします。 トークンを使用して API を呼び出す方法の例については、[REST API のドキュメント](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)を参照してください。

## <a name="web-service-authentication"></a>Web サービス認証

Azure Machine Learning の Web サービスは、上記の方法とは異なる認証パターンを使用します。 デプロイされた Web サービスを認証する最も簡単な方法は、**キーベースの認証**を使用することです。これにより、更新する必要のない静的なベアラー型の認証キーが生成されます。 デプロイされた Web サービスに対して認証のみを行う必要がある場合は、上記のようにサービス プリンシパル認証を設定する必要はありません。

Azure Kubernetes Service にデプロイされた Web サービスでは、キーベースの認証が既定で*有効*になっています。 Azure Container Instances のデプロイ サービスでは、キーベースの認証が既定で*無効*になっていますが、ACI Web サービスの作成時に `auth_enabled=True` を設定して有効にすることができます。 次に、キーベースの認証が有効になっている ACI デプロイ構成を作成する例を示します。

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

次に、`Model` クラスを使用して、デプロイでカスタム ACI 構成を使用できます。

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

認証キーをフェッチするには、`aci_service.get_keys()` を使用します。 キーを再生成するには、`regen_key()` 関数を使用して、**Primary** または **Secondary** のいずれかを渡します。

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Web サービスは、トークンベースの認証もサポートしますが、Azure Kubernetes Service のデプロイのみが対象です。 認証の詳細については、Web サービスの使用に関する[ハウツー](how-to-consume-web-service.md)記事を参照してください。

### <a name="token-based-web-service-authentication"></a>トークンベースの Web サービス認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JSON Web トークンを提示する必要があります。 トークンは、指定された期間後に期限切れとなり、呼び出しを続行するには更新する必要があります。

* Azure Kubernetes Service にデプロイする場合、トークン認証は**既定で無効**になります。
* Azure Container Instances にデプロイする場合、トークン認証は**サポートされません**。

トークン認証を制御するには、デプロイの作成時や更新時に `token_auth_enabled` パラメーターを使用します。

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JSON Web トークン (JWT) とそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。 Python SDK の外部でトークンを更新する必要がある場合は、前に説明したように、サービス プリンシパル認証で REST API を使用して定期的に `service.get_token()` 呼び出しを行う方法があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 
>
> トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その結果、ワークスペースのリージョンが再び使用可能になるまで Azure AD Authentication は使用できなくなります。 
>
> また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="next-steps"></a>次のステップ

* [画像分類モデルをトレーニングし、デプロイする](tutorial-train-models-with-aml.md)。
* [Web サービスとしてデプロイされた Azure Machine Learning モデルを使用する](how-to-consume-web-service.md)。
