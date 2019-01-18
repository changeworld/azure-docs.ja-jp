---
title: Azure Stack への Azure Cognitive Services のデプロイ | Microsoft Docs
description: Azure Cognitive Services を Azure Stack にデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.openlocfilehash: 5af508714b5eae5cdd23c940af0ae21300c0c5b8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195171"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Azure Cognitive Services を Azure Stack にデプロイする

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

> [!Note]  
> Azure Stack の Aure Cognitive Services は、プレビュー段階です。

Azure Cognitive Services は、Azure Stack 上のコンテナー サポートとともに使用できます。 Azure Cognitive Services でのコンテナーのサポートにより、Azure で使用できる同じ豊富な API を使用できます。 コンテナーを使用することで、[Docker コンテナー](https://www.docker.com/what-container)で提供されるサービスのデプロイ先やホスト先に柔軟に対応できるようになります。 現在、コンテナー サポートは、[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)、[Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)、および [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS) など、Azure Cognitive Services のサブセットに対するプレビューで使用できます。

コンテナー化とは、アプリケーションまたはサービスとその依存関係や構成をコンテナー イメージとしてパッケージ化する、ソフトウェア配布のアプローチです。 ほとんどまたはまったく変更せずに、コンテナー ホストにイメージをデプロイできます。 各コンテナーは、他のコンテナーや、基になるオペレーティング システムから分離されます。 システム自体には、イメージを実行するために必要なコンポーネントしかありません。 コンテナー ホストは、仮想マシンよりも小さいフット プリントです。 さらに、短期的なタスクでは、イメージからコンテナーを作成し、不要になったときには削除できます。

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Azure Stack の Cognitive Services でコンテナーを使用する

- **データの制御**  
  Cognitive Services を使用しているときに、データを制御することをアプリ ユーザーに許可します。 Cognitive Services は、グローバル Azure またはパブリック クラウドにデータを送信できないアプリ ユーザーに配信できます。

- **モデルの更新プログラムの制御**  
  アプリ ユーザーに、ソリューションにデプロイされたモデルのバージョンと更新プログラムを提供します。

- **ポータブル アーキテクチャ**  
  ポータブル アプリ アーキテクチャを作成できるようにして、ソリューションをパブリック クラウドや、オンプレミスまたはエッジのプライベート クラウドにデプロイできるようにします。 コンテナーは Azure Kubernetes Service、Azure Container Instances、または Azure Stack 内の Kubernetes cluster にデプロイできます。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。

- **高スループットで待機時間の短いクエリ**  
   高いスループットと低待機時間のために、アプリ ユーザーにトラフィックの急増に合わせて拡大縮小する機能を提供します。 Cognitive Services を、アプリケーション ロジックとデータに物理的に近い Azure Kubernetes サービスで実行できるようにします。

Azure Stack では、高可用性およびエラスティック スケーリングのために、Cognitive Services コンテナーをアプリケーション コンテナーとともに Kubernetes クラスターにデプロイします。 Cognitive Services を、App Services、関数、Blob ストレージ、または SQL あるいは mySQL データベースで作成されたコンポーネントと組み合わせることで、アプリケーションを開発できます。 

Cognitive Services のコンテナーの詳細については、「[Azure Cognitive Services でのコンテナーのサポート](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)」を参照してください。

## <a name="deploy-the-azure-face-api"></a>Azure Face API をデプロイする

この記事では、Azure Stack の Kubernetes クラスターに Azure Face API をデプロイする方法について説明します。 他の Cognitive Services を Azure Stack Kubernetes クラスターにデプロイするには、同じアプローチを使用できます。

## <a name="prerequisites"></a>前提条件

手順を開始する前に、次が必要となります。

1.  Azure Cognitive Services コンテナー レジストリから Face コンテナー イメージをプルするには、コンテナー レジストリへのアクセスを要求します。 詳細については、「[プライベート コンテナー レジストリへのアクセスの要求](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry)」のセクションを参照してください。

2.  Azure Stack で Kubernetes クラスターを準備します。 「[Kubernetes を Azure Stack にデプロイする](azure-stack-solution-template-kubernetes-deploy.md)」の記事に記載されている手順に従うことができます。

## <a name="create-azure-resources"></a>Azure リソースを作成する

それぞれ、Face、LUIS、またはテキスト認識コンテナーをプレビューする Cognitive Service リソースを Azure で作成します。 Cognitive Services コンテナーをインスタンス化するために、リソースからのサブスクリプション キーとエンドポイント URL を使用する必要があります。

1.  Azure portal で Azure リソースを作成します。 Face コンテナーをプレビューする場合、まず Azure portal で対応する Face リソースを作成する必要があります。 詳細については、「[クイック スタート: Azure portal で Cognitive Services アカウントを作成する](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)」を参照してください。

    >  [!Note]  
    >  Face または Computer Vision リソースでは、F0 価格レベルを使用する必要があります。

2.  Azure リソースのエンドポイント URL とサブスクリプション キーを取得します。 Azure リソースを作成したら、そのリソースからのサブスクリプション キーとエンドポイント URL を使用して、対応する Face、LUIS、またはテキスト認識コンテナーをプレビュー用にインスタンス化する必要があります。

## <a name="create-a-kubernetes-secret"></a>Kubernetes シークレットを作成する 

プライベート コンテナー レジストリにアクセスするには、Kubectl はシークレット作成コマンドを使用します。 **&lt;username&gt;** と **&lt;password&gt;** を、Azure Cognitive Services チームから受け取った資格情報内で提供されているユーザー名とパスワードにそれぞれ置き換えます。

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>YAML 構成ファイルの準備

YAML 構成ファイルを使用して Kubernetes クラスターの Cognitive Service のデプロイを簡略化します。

これは Face サービスを Azure Stack にデプロイする サンプル YAML構成ファイルです。

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

この YAML 構成ファイルで、Azure Container Registry から Cognitive Service コンテナー イメージを取得するために使うシークレットを使用します。 シークレット ファイルを使用して、コンテナーの特定のレプリカをデプロイできます。 ユーザーが外部からこのサービスにアクセスできるかどうかを確認するために、ロード バランサーも作成します。

キー フィールドに関する詳細:

| フィールド | メモ |
| --- | --- |
| replicaNumber | 作成するインスタンスの初期レプリカを定義します。 デプロイ後に確実に拡張できます。 |
| ImageLocation | ACR での特定の Cognitive Service コンテナー イメージの場所を示します。 たとえば、顔サービスの場合は次のようになります: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |[Azure リソースの作成](#create-azure-resources) のステップで書き留めたエンドポイント URL |
| ApiKey | [Azure リソースの作成](#create-azure-resources) の手順で書き留めたサブスクリプション キー |
| SecretName | [プライベート コンテナー レジストリにアクセスするシークレットの作成](#create-secrete-to-access-the-private-container-registry) の手順で書き留めたシークレット名 |

## <a name="deploy-the-cognitive-service"></a>Cognitive Services のデプロイ

Cognitive Services コンテナーをデプロイする次のコマンドの使用

```bash  
    Kubectl apply -f <yamlFineName>
```
デプロイ方法を監視するには、次のコマンドを使用します。 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Cognitive Service をテストする

インスタンス化されたコンテナーの **/swagger** 相対 URI から、そのコンテナーによってサポートされる操作について説明する [OpenAPI 仕様](https://swagger.io/docs/specification/about/) (以前の Swagger 仕様) にアクセスします。 たとえば、次の URI から、前の例でインスタンス化された感情分析コンテナーの OpenAPI 仕様にアクセスできます。

```HTTP  
http:<External IP>:5000/swagger
```

外部 IP アドレスは、次のコマンドで取得できます。 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Python を使用してサービスを試す

いくつかの単純な Python スクリプトを実行して Azure Stack で Cognitive Services を検証することができます。 参照用に [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)、[Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)、および [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS) の Python 公式クイックスタート サンプルがあります。

コンテナーで実行されているサービスに対して実行する Python アプリを作成するには、次の 2 つのことを念頭に置いておく必要があります。 
1. コンテナー内の Cognitive Services の認証にはサブ キーは必要ありませんが、SDK を満たすために、プレースホルダーとして任意の文字列を入力する必要があります。 
2. base_URL を実際のサービス エンドポイントの IP アドレスに置き換える 

ここでは、イメージの中で顔を検出してフレームするために、サンプルの Python スクリプトが Face サービスの Python SDK を使用しています。 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>次の手順

[Computer Vision API コンテナーをインストールして実行する方法](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Face API コンテナーをインストールして実行する方法](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[Text Analytics API コンテナーをインストールして実行する方法](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Language Understanding (LIUS) コンテナーをインストールして実行する方法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)