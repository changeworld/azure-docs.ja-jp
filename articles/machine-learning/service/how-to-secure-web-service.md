---
title: SSL を使用して Web サービスをセキュリティで保護する
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning サービスでデプロイされた Web サービスをセキュリティで保護する方法について説明します。 Secure Sockets Layer (SSL) とキー ベースの認証を使用して、Web サービスへのアクセスを制限し、クライアントによって送信されたデータをセキュリティで保護することができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.custom: seodec18
ms.openlocfilehash: 14350a04326ba22dcc5c8608b6ac6b9180666832
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53101203"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>SSL を使用して Azure Machine Learning service による Web サービスをセキュリティで保護する

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

ドメイン名をまだ所有していない場合は、__ドメイン名レジストラー__ から購入できます。 プロセスと費用は、レジストラーによって異なります。 レジストラーも、ドメイン名を管理するためのツールを提供しています。 これらのツールを使用して、完全修飾ドメイン名 (www.contoso.com など) を、Web サービスをホストする IP アドレスにマップします。

## <a name="get-an-ssl-certificate"></a>SSL 証明書を取得する

SSL 証明書を取得する方法はたくさんあります。 最も一般的な方法は、__証明機関__(CA) から購入することです。 証明書の取得場所に関係なく、次のファイルが必要です。

* __証明書__。 証明書は、完全な証明書チェーンを含み、PEM でエンコードされている必要があります。
* __キー__。 キーは、PEM でエンコードされている必要があります。

証明書を要求するときは、Web サービスで使用する予定のアドレスの完全修飾ドメイン名 (FQDN) を指定する必要があります。 例: www.contoso.com。 Web サービスの ID を検証するときに、証明書に記載されているアドレスとクライアントによって使用されるアドレスが比較されます。 アドレスが一致しない場合、クライアントはエラーを受信します。 

> [!TIP]
> 証明機関が、証明書とキーを PEM でエンコードされたファイルとして提供できない場合は、 [OpenSSL](https://www.openssl.org/) などのユーティリティを使用して、フォーマットを変更できます。

> [!WARNING]
> 自己署名証明書は、開発時のみ使用してください。 運用環境では使用しないでください。 自己署名証明書により、お使いのクライアント アプリケーションで問題が発生する可能性があります。 詳しくは、お使いのクライアント アプリケーションで使用されているネットワーク ライブラリに関するドキュメントをご覧ください。

## <a name="enable-ssl-and-deploy"></a>SSL を有効にしてデプロイする

SSL が有効なサービスをデプロイ (または再デプロイ) するには、`ssl_enabled` パラメーターを `True` に設定します (該当する場合は必ず)。 `ssl_certificate` パラメーターを __証明書__ ファイルの値に設定し、`ssl_key` を __キー__ ファイルの値に設定します。 

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

  `create_service` 操作の応答に、サービスの IP アドレスが含まれています。 この IP アドレスは、DNS 名をサービスの IP アドレスに マップするときに使用されます。 応答には、サービスを利用するときに使用される __主キー__ と __セカンダリ キー__ も含まれています。 コード スニペットに示したように、SSL 関連のパラメーターの値を指定します。

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

  ![Azure Machine Learning service:SSL を使用して Web サービスをセキュリティで保護する](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>次の手順

[Web サービスとしてデプロイされる ML モデルを使用する](how-to-consume-web-service.md)方法を学習します。