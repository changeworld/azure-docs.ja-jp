---
title: Azure Machine Learning でモデルを Web サービスとして FPGA 上に配置する方法
description: FPGA 上で実行されるモデルを含む Web サービスを Azure Machine Learning で配置する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Azure Machine Learning でモデルを Web サービスとして FPGA 上に配置する

このドキュメントでは、ワークステーション環境をセットアップし、モデルを Web サービスとして [Field Programmable Gate Array (FPGA)](concept-accelerate-with-fpgas.md) に配置する方法について説明します。 この Web サービスは、Project Brainwave を使用して、FPGA 上でモデルを実行します。

FPGA を使用すると、単一のバッチ サイズでも、待機時間が極端に短い推論を実行できます。

## <a name="create-an-azure-machine-learning-model-management-account"></a>Azure Machine Learning モデル管理アカウントを作成する

1. [Azure Portal](https://aka.ms/aml-create-mma)で、[モデル管理アカウントの作成] ページに移動します。

2. ポータルで、**[米国東部 2]** リージョンにモデル管理アカウントを作成します。

   ![[モデル管理アカウントの作成] 画面](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. モデル管理アカウントの名前を指定し、サブスクリプションを選択し、リソース グループを選択します。

   >[!IMPORTANT]
   >[場所] では、リージョンとして **[米国東部 2]** を選択する必要があります。  現時点では、それ以外のリージョンは利用できません。

4. 価格レベルを選択します (S1 で十分ですが、S2 と S3 でも動作します)。  DevTest レベルはサポートされていません。  

5. **[選択]** をクリックして価格レベルを確定します。

6. 左側の [ML モデル管理] の **[作成]** をクリックします。

## <a name="get-model-management-account-information"></a>モデル管理アカウント情報を取得する

モデルの管理アカウント (MMA) に関する情報を取得するには、Azure Portal で __[モデル管理アカウント]__ をクリックします。

次の項目の値をコピーします。

+ モデル管理アカウントの名前 (左上隅にあります)
+ リソース グループ名
+ サブスクリプション ID
+ 場所 ([米国東部 2] を使用してください)

![モデル管理アカウント情報](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>コンピューターをセットアップする

FPGA への配置用にワークステーションをセットアップするには、次の手順を使用します。

1. 最新バージョンの [Git](https://git-scm.com/downloads) をダウンロードしてインストールします。

2. [Anaconda (Python 3.6)](https://conda.io/miniconda.html) をインストールします。

3. Anaconda 環境をダウンロードするには、Git プロンプトで次のコマンドを使用します。

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. 環境を作成するには、(Azure Machine Learning Workbench プロンプトではなく) **Anaconda Prompt** を開いて、次のコマンドを実行します。

    > [!IMPORTANT]
    > `environment.yml` ファイルは、前の手順で複製した git リポジトリ内にあります。 必要に応じて、ワークステーション上のファイルを指すようにパスを変更します。

    ```
    conda env create -f environment.yml
    ```

5. 環境をアクティブ化にするには、次のコマンドを使用します。

    ```
    conda activate amlrealtimeai
    ```

6. Jupyter Notebook サーバーを起動するには、次のコマンドを使用します。

    ```
    jupyter notebook
    ```

    このコマンドの出力は次のテキストのようになります。

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > コマンドを実行するたびに、異なるトークンが取得されます。

    ブラウザーで Jupyter Notebook が自動的に開かない場合は、前のコマンドで返された HTTP URL を使用してページを開きます。

    ![Jupyter Notebook Web ページ](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>モデルを展開する

Jupyter Notebook から、`notebooks/resnet50` ディレクトリの `00_QuickStart.ipynb` を開きます。 以下を行うには、Notebook の指示に従います。

* サービスを定義する
* モデルを配置する
* 配置したモデルを使用する
* 展開したサービスを削除する

> [!IMPORTANT]
> 待機時間とスループットを最適化するために、ワークステーションは、エンドポイントと同じ Azure リージョンに存在する必要があります。  現時点では、この API は、Azure の米国東部リージョンに作成されています。

## <a name="ssltls-and-authentication"></a>SSL/TLS と認証

Azure Machine Learning は、SSL のサポートとキー ベースの認証を提供します。 これにより、サービスへのアクセスを制限し、クライアントによって送信されるデータをセキュリティで保護できます。

> [!NOTE]
> このセクションの手順は、Azure Machine Learning ハードウェア アクセラレーション モデルにのみ適用されます。 標準的な Azure Machine Learning のサービスについては、[Azure Machine Learning コンピューティングで SSL を設定する方法](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc)に関する記事を参照してください。

> [!IMPORTANT]
> 認証は、SSL 証明書とキーが提供されるサービスに対して有効にできます。 
>
> SSL を有効にしない場合、インターネット上のすべてのユーザーがサービスへの呼び出しを実行できます。
>
> SSL を有効にすると、サービスにアクセスするときに認証キーが要求されます。

SSL は、クライアントとサービス間で送信されるデータを暗号化します。 これは、クライアントがサーバーの ID を検証するためにも使用されます。

SSL が有効なサービスを展開するか、既に展開されているサービスを更新できます。 手順は同じです。

1. ドメイン名を取得します。

2. SSL 証明書を取得します。

3. SSL が有効なサービスを展開するか更新します。

4. サービスを指すように DNS を更新します。

### <a name="acquire-a-domain-name"></a>ドメイン名を取得する

ドメイン名をまだ所有していない場合は、__ドメイン名レジストラー__から購入できます。 プロセスと費用は、レジストラーによって異なります。 レジストラーも、ドメイン名を管理するためのツールを提供しています。 これらのツールを使用して、完全修飾ドメイン名 (www.contoso.com など) を、サービスがホストされる IP アドレスにマップします。

### <a name="acquire-an-ssl-certificate"></a>SSL 証明書を取得する

SSL 証明書を取得する方法はたくさんあります。 最も一般的な方法は、__証明機関__(CA) から購入することです。 証明書の取得場所に関係なく、次のファイルが必要です。

* __証明書__。 証明書は、完全な証明書チェーンを含み、PEM でエンコードされている必要があります。
* __キー__。 キーは、PEM でエンコードされている必要があります。

> [!TIP]
> 証明機関が、証明書とキーを PEM でエンコードされたファイルとして提供できない場合は、 [OpenSSL](https://www.openssl.org/) などのユーティリティを使用して、フォーマットを変更できます。

> [!IMPORTANT]
> 自己署名証明書は、開発時のみ使用してください。 運用環境では使用しないでください。
>
> 自己署名証明書を使用する場合は、「[自己署名証明書付きのサービスの使用](#self-signed)」セクションの具体的な指示を参照してください。

> [!WARNING]
> 証明書を要求するときは、サービスで使用するアドレスの完全修飾ドメイン名 (FQDN) を指定する必要があります。 例: www.contoso.com。サービスの ID を検証するときに、証明書に記載されているアドレスとクライアントによって使用されるアドレスが比較されます。
>
> アドレスが一致しない場合、クライアントはエラーを受信します。 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>SSL が有効なサービスを展開するか更新する

SSL が有効なサービスを展開するには、`ssl_enabled` パラメーターを `True` に設定します。 `ssl_certificate` パラメーターを__証明書__ファイルの値に設定し、`ssl_key` を __キー__ ファイルの値に設定します。 次の例は、SSL が有効なサービスの展開を示しています。

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

`create_service` 操作の応答に、サービスの IP アドレスが含まれています。 この IP アドレスは、DNS 名をサービスの IP アドレスに マップするときに使用されます。

応答には、サービスを利用するときに使用される__主キー__と__セカンダリ キー__も含まれています。

### <a name="update-your-dns-to-point-to-the-service"></a>サービスを指すように DNS を更新する

ドメイン名レジストラーが提供しているツールを使用して、ドメイン名の DNS レコードを更新します。 レコードは、サービスの IP アドレスを指している必要があります。

> [!NOTE]
> レジストラと、ドメイン名に対して構成された Time to Live (TTL) に応じて、クライアントがドメイン名を解決できるまで、数分から数時間かかる可能性があります。

### <a name="consuming-authenticated-services"></a>認証済みのサービスの使用

次の例は、Python と C# を使用して認証済みのサービスを使用する方法を示しています。

> [!NOTE]
> `authkey` を、サービスの作成時に返された主キーまたはセカンダリ キー置き換えてください。

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

他の gRPC クライアントは、承認ヘッダーを設定することで、要求を認証できます。 一般的な方法は、`SslCredentials` と `CallCredentials` を組み合わせる `ChannelCredentials` オブジェクトを作成することです。 これは、要求の Authorization ヘッダーに追加されます。 特定のヘッダーに対するサポートの実装の詳細については、[https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html) を参照してください。

次の例は、C# と Go でヘッダーを設定する方法を示しています。

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>自己署名付き証明書サービスの使用

クライアントが自己署名証明書によってセキュリティ保護されているサーバーで認証されるようにするには、2 つの方法があります。

* クライアント システムで、クライアント システム上の `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 環境変数を、証明書ファイルを指すように設定します。

* `SslCredentials` オブジェクトを構築するときに、証明書ファイルの内容をコンストラクターに渡します。

どちらの方法を使用しても、gRPC は、証明書をルート証明書として使用するようになります。

> [!IMPORTANT]
> gRPC は、信頼されていない証明書を受け入れません。 信頼されていない証明書を使用すると、状態コード `Unavailable` で失敗します。 エラーの詳細の中に、`Connection Failed` が含まれます。
