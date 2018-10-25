---
title: SSL によって Azure Machine Learning Web サービスをセキュリティで保護する
description: Azure Machine Learning サービスでデプロイされた Web サービスをセキュリティで保護する方法について説明します。 Secure Sockets Layer (SSL) とキー ベースの認証を使用して、Web サービスへのアクセスを制限し、クライアントによって送信されたデータをセキュリティで保護することができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 885d867d0733ef923d327d8d6a36fc1588fd4961
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801014"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>SSL によって Azure Machine Learning Web サービスをセキュリティで保護する

この記事では、Azure Machine Learning サービスでデプロイされた Web サービスをセキュリティで保護する方法について説明します。 Secure Sockets Layer (SSL) とキー ベースの認証を使用して、Web サービスへのアクセスを制限し、クライアントによって送信されたデータをセキュリティで保護することができます。

> [!Warning]
> SSL を有効にしない場合、インターネット上のすべてのユーザーが Web サービスへの呼び出しを実行できます。

SSL は、クライアントと Web サービス間で送信されるデータを暗号化します。 これは、クライアントがサーバーの ID を検証するためにも使用されます。 認証は、SSL 証明書とキーが提供されるサービスに対して有効にできます。  SSL を有効にすると、Web サービスにアクセスするときに認証キーが要求されます。

SSL が有効な Web サービスをデプロイする場合も、デプロイ済みの既存の Web サービスに対して SSL を有効にする場合も、手順は同じです。

1. ドメイン名を取得します。

2. SSL 証明書を取得します。

3. SSL 設定を有効にして Web サービスをデプロイまたは更新します。

4. その Web サービスを指すように DNS を更新します。

複数の[デプロイ ターゲット](how-to-deploy-and-where.md)にわたる Web サービスをセキュリティで保護する場合は、わずかな違いがあります。 

## <a name="get-a-domain-name"></a>ドメイン名を取得する

ドメイン名をまだ所有していない場合は、__ドメイン名レジストラー__から購入できます。 プロセスと費用は、レジストラーによって異なります。 レジストラーも、ドメイン名を管理するためのツールを提供しています。 これらのツールを使用して、完全修飾ドメイン名 (www.contoso.com など) を、Web サービスをホストする IP アドレスにマップします。

## <a name="get-an-ssl-certificate"></a>SSL 証明書を取得する

SSL 証明書を取得する方法はたくさんあります。 最も一般的な方法は、__証明機関__(CA) から購入することです。 証明書の取得場所に関係なく、次のファイルが必要です。

* __証明書__。 証明書は、完全な証明書チェーンを含み、PEM でエンコードされている必要があります。
* __キー__。 キーは、PEM でエンコードされている必要があります。

証明書を要求するときは、Web サービスで使用する予定のアドレスの完全修飾ドメイン名 (FQDN) を指定する必要があります。 例: www.contoso.com。 Web サービスの ID を検証するときに、証明書に記載されているアドレスとクライアントによって使用されるアドレスが比較されます。 アドレスが一致しない場合、クライアントはエラーを受信します。 

> [!TIP]
> 証明機関が、証明書とキーを PEM でエンコードされたファイルとして提供できない場合は、 [OpenSSL](https://www.openssl.org/) などのユーティリティを使用して、フォーマットを変更できます。

> [!IMPORTANT]
> 自己署名証明書は、開発時のみ使用してください。 運用環境では使用しないでください。 自己署名証明書を使用する場合の具体的な指示については、[自己署名証明書付きの Web サービスの使用](#self-signed)に関するセクションを参照してください。


## <a name="enable-ssl-and-deploy"></a>SSL を有効にしてデプロイする

SSL が有効なサービスをデプロイ (または再デプロイ) するには、`ssl_enabled` パラメーターを `True` に設定します (該当する場合は必ず)。 `ssl_certificate` パラメーターを__証明書__ファイルの値に設定し、`ssl_key` を __キー__ ファイルの値に設定します。 

+ **Azure Kubernetes Service (AKS) にデプロイする**
  
  AKS クラスターをプロビジョニングするときには、コード スニペットに示したように、SSL 関連のパラメーターの値を指定します。

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Azure Container Instances (ACI) にデプロイする**
 
  ACI にデプロイするときには、コード スニペットに示したように、SSL 関連のパラメーターの値を指定します。

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **フィールド プログラマブル ゲート アレイ (FPGA) に展開する**

  `create_service` 操作の応答に、サービスの IP アドレスが含まれています。 この IP アドレスは、DNS 名をサービスの IP アドレスに マップするときに使用されます。 応答には、サービスを利用するときに使用される__主キー__と__セカンダリ キー__も含まれています。 コード スニペットに示したように、SSL 関連のパラメーターの値を指定します。

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

## <a name="update-your-dns"></a>DNS を更新する

次に、Web サービスを指すように DNS を更新する必要があります。

+ **ACI と FPGA の場合**:  

  ドメイン名レジストラーが提供しているツールを使用して、ドメイン名の DNS レコードを更新します。 レコードは、サービスの IP アドレスを指している必要があります。  

  レジストラと、ドメイン名に対して構成された Time to Live (TTL) に応じて、クライアントがドメイン名を解決できるまで、数分から数時間かかる可能性があります。

+ **AKS の場合**: 

  イメージに示したように、AKS クラスターの [パブリック IP アドレス] の [構成] タブで DNS を更新します。 パブリック IP アドレスは、AKS エージェント ノードとその他のネットワーク リソースを含むリソース グループの下に作成される、リソースの種類の 1 つとして見つかります。

  ![Azure Machine Learning service: SSL による Web サービスのセキュリティ保護](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="consume-authenticated-services"></a>認証済みのサービスを使用する

### <a name="how-to-consume"></a>使用方法 
+ **ACI と AKS の場合**: 

  ACI と AKS の Web サービスの場合は、以下の記事で Web サービスを使用する方法を説明しています。
  + [ACI にデプロイする方法](how-to-deploy-to-aci.md)

  + [AKS にデプロイする方法](how-to-deploy-to-aks.md)

+ **FPGA の場合**:  

  次の例は、Python と C# で認証済みの FPGA サービスを使用する方法を示しています。
  `authkey` を、サービスの展開時に返された主キーまたはセカンダリ キーに置き換えてください。

  Python の例:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  C# の例:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>承認ヘッダーを設定する
他の gRPC クライアントは、承認ヘッダーを設定することで、要求を認証できます。 一般的な方法は、`SslCredentials` と `CallCredentials` を組み合わせる `ChannelCredentials` オブジェクトを作成することです。 これは、要求の Authorization ヘッダーに追加されます。 特定のヘッダーに対するサポートの実装の詳細については、[https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html) を参照してください。

次の例は、C# と Go でヘッダーを設定する方法を示しています。

+ C# を使用してヘッダーを設定します。
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Go を使用してヘッダーを設定します。
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>自己署名付き証明書サービスを使用する

クライアントが自己署名証明書によってセキュリティ保護されているサーバーで認証されるようにするには、2 つの方法があります。

* クライアント システムで、クライアント システム上の `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` 環境変数を、証明書ファイルを指すように設定します。

* `SslCredentials` オブジェクトを構築するときに、証明書ファイルの内容をコンストラクターに渡します。

どちらの方法を使用しても、gRPC は、証明書をルート証明書として使用するようになります。

> [!IMPORTANT]
> gRPC では、信頼されていない証明書は受け入れられません。 信頼されていない証明書を使用すると、状態コード `Unavailable` で失敗します。 エラーの詳細の中に、`Connection Failed` が含まれます。
