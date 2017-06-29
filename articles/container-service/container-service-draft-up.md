---
title: "Azure Container Service と Azure Container Registry で Draft を使用する | Microsoft Docs"
description: "ACS Kubernetes クラスターと Azure Container Registry を作成し、Draft を使用して Azure に最初のアプリケーションを作成します。"
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, Draft, Azure"
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 20619fd21f376afee95facb688e35534f5979b90
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017



---

# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Azure Container Service と Azure Container Registry で Draft を使用して、アプリケーションを構築し Kubernetes にデプロイする

[Draft](https://aka.ms/draft) は、新しいオープンソース ツールです。Docker や Kubernetes について詳しくなくても、またそれらをインストールしなくても、容易にコンテナーベースのアプリケーションの開発と、Kubernetes クラスターへのデプロイを行えるようになります。 Draft のようなツールを使用すると、チームはインフラストラクチャをあまり気にせずに、アプリケーションの構築に集中できます。

Draft は、すべての Docker イメージ レジストリと、ローカルを含むすべての Kubernetes クラスターで使用できます。 このチュートリアルでは、Kubernetes、ACR、Azure DNS で ACS を使用し、Draft を使用してライブ CI/CD 開発者パイプラインを作成する方法を説明します。


## <a name="create-an-azure-container-registry"></a>Azure Container Registry を作成する
[新しい Azure Container Registry の作成](../container-registry/container-registry-get-started-azure-cli.md)は容易です。手順は次のとおりです。

1. Azure リソース グループを作成して、ACR レジストリと Kubernetes クラスターを ACS で管理します。
      ```azurecli
      az group create --name draft --location eastus
      ```

2. [az acr create](/cli/azure/acr#create) を使用して ACR イメージ レジストリを作成します。
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Kubernetes を使用して Azure Container Service をデプロイする

これで、[az acs create](/cli/azure/acs#create) を使用し、Kubernetes を `--orchestrator-type` の値として、ACS クラスターを作成する準備が整いました。
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Kubernetes は既定の種類の Orchestrator ではないため、必ず `--orchestrator-type kubernetes` スイッチを使用してください。

正常に終了すると、出力は次のようになります。

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

クラスターが作成されたところで、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) コマンドを使用して資格情報をインポートできます。 現在、クラスターのローカル構成ファイルがあり、Helm と Draft が処理を行うためにこれが必要です。

## <a name="install-and-configure-draft"></a>Draft をインストールして構成する
Draft のインストール手順は、[Draft のリポジトリ](https://github.com/Azure/draft/blob/master/docs/install.md)にあります。 比較的単純な手順ですが、Helm チャートを作成して Kubernetes クラスターにデプロイするために [Helm](https://aka.ms/helm) を利用するための構成が必要です。

1. [Helm をダウンロードしてインストールします](https://aka.ms/helm#install)。
2. Helm を使用して、`stable/traefik` と受信コントローラーを検索してインストールし、ビルドのインバウンド要求を有効にします。
    ```bash
    $ helm search traefik
    NAME              VERSION    DESCRIPTION
    stable/traefik    1.2.1-a    A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    ここで、`ingress` コントローラーにウォッチを設定して、デプロイ時に外部 IP 値をキャプチャするようにします。 この IP アドレスは、次のセクションで[デプロイ ドメインにマップされる](#wire-up-deployment-domain)ものです。

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    このケースでは、デプロイ ドメインの外部 IP は `13.64.108.240` です。 ここで、その IP にドメインをマップできます。

## <a name="wire-up-deployment-domain"></a>デプロイ ドメインに接続する

Draft によって、作成される各 Helm チャート (作業対象の各アプリケーション) のリリースが作成されます。 それぞれに対して、Draft で使用される名前が生成されます。これは、ユーザーが制御するルート "_デプロイ ドメイン_" 上の "_サブドメイン_" です (この例では、デプロイ ドメインとして `squillace.io` を使用します)。このサブドメインの動作を有効にするには、デプロイ ドメインの DNS エントリで `'*'` に対して A レコードを作成する必要があります。こうすることで、生成される各サブドメインが Kubernetes クラスターの受信コントローラーにルーティングされます。

ドメイン プロバイダーには DNS サーバーを割り当てる独自の方法があります。[ドメイン ネームサーバーを Azure DNS に委任する](../dns/dns-delegate-domain-azure-dns.md)には、次の手順を実行します。

1. ゾーンのリソース グループを作成します。
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. ドメインの DNS ゾーンを作成します。
[az network dns zone create](/cli/azure/network/dns/zone#create) コマンドを使用して、ドメインに関して DNS の制御を Azure DNS に委任するネームサーバーを取得します。
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. 与えられた DNS サーバーを、デプロイ ドメインのドメイン プロバイダーに追加します。これにより、Azure DNS を使用し、必要に応じて自分のドメインを再指定できるようになります。
4. デプロイ ドメインの A レコードセット エントリを作成し、前のセクションの手順 2 の `ingress` IP にマッピングします。
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
次のような画面が出力されます。
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. レジストリを使用して、作成される各 Helm チャートのサブドメインを作成するように、Draft を構成します。 Draft を構成するには次が必要です。
  - Azure Container Registry 名 (この例では `draftacs`)
  - レジストリ キーまたはパスワード (`az acr credential show -n $acrname --output tsv --query "passwords[0].value"` を使用)
  - Kubernetes 受信外部 IP アドレスにマップするように構成したルート デプロイ ドメイン (この例では `13.64.108.240`)

  これらの値を使用し、構成 JSON 文字列 (`{"username":"<user>","password":"<secret>","email":"email@example.com"}`) の base-64 でエンコードされた値を作成します。 この値をエンコードする方法の 1 つを次に示します (例の値を自分の値で置き換えてください)。
      ```bash
      acrname="draftacs"
      password=$(az acr credential show -n $acrname --output tsv --query "passwords[0].value")
      authtoken=$(echo \{\"username\":\"$acrname\",\"password\":\"$password\",\"email\":\"rasquill@microsoft.com\"\} | base64)
      ```

  JSON 文字列が正しいことを確認するには、`echo $authtoken | base64 -D` を入力して、エンコードされていない結果を表示します。
  ここで、次のコマンドと `-set` オプションの構成引数を使用して、Draft を初期化します。
      ```bash
      draft init --set registry.url=$acrname.azurecr.io,registry.org=$acrname,registry.authtoken=$authtoken,basedomain=squillace.io
      ```
      > [!NOTE]
      > `basedomain` の値は、自分で制御しているベース デプロイ ドメインであり、受信外部 IP を指すように構成したことを忘れがちです。

これで、アプリケーションをデプロイする準備が整いました。


## <a name="build-and-deploy-an-application"></a>アプリケーションを構築してデプロイする

Draft リポジトリには [6 個の単純なサンプル アプリケーション](https://github.com/Azure/draft/tree/master/examples)があります。 このリポジトリを複製して、[Python のサンプル](https://github.com/Azure/draft/tree/master/examples/python)を使用しましょう。 examples/Python ディレクトリに移動し、`draft create` と入力してアプリケーションを構築します。 次の例のようになります。
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

出力には Dockerfile と Helm チャートが含まれます。 構築してデプロイするには、`draft up` と入力するだけです。 大量に出力されますが、最初の部分は次のようになります。
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

正常に終了すると、最後の部分は次のようになります。
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

チャートの名前に関係なく、`curl http://gangly-bronco.squillace.io` を実行すると、`Hello World!` が返信されます。

## <a name="next-steps"></a>次のステップ

これで、ACS Kubernetes クラスターが用意されました。[Azure Container Registry](../container-registry/container-registry-intro.md) を使用して調査し、このシナリオのさまざまなデプロイを作成できます。 たとえば、特定の ACS デプロイの深い階層のサブドメインの処理を制御する、draft._basedomain.toplevel_ ドメイン DNS レコードセットを作成できます。







