---
title: Form Recognizer v2.1 用の Docker コンテナーのインストールと実行
titleSuffix: Azure Applied AI Services
description: オンプレミスの Form Recognizer 用の Docker コンテナーを使用して、フォームとドキュメントからキーと値のペア、選択マーク、表、構造を識別し、抽出します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: オンプレミス, Docker, コンテナー, 識別
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdd04a42ba97721d5d5dafd23f9e8e7b5b661f4b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027001"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>Form Recognizer v2.1-preview コンテナーのインストールと実行

> [!IMPORTANT]
>
> * Form Recognizer コンテナーは、限定的なプレビュー段階にあります。 これらを使用するには、[オンライン リクエスト](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)を送信し、承認を受ける必要があります。 詳細については、下記の「[**コンテナーを実行するための承認を要求する**](#request-approval-to-run-the-container)」を参照してください。
>
> * オンライン リクエスト フォームでは、Azure サブスクリプション ID を所有する組織に属している有効なメール アドレスを指定する必要があり、ご自身がそのサブスクリプションを所有しているか、そのアクセス権を付与されている必要があります。

Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築することができる Azure Applied AI Services です。 Form Recognizer を使用すると、テキスト、キーと値のペア、選択マーク、表データなどを特定してフォーム ドキュメントから抽出し、元のファイル内のリレーションシップを含む構造化データを出力することができます。

この記事では、Form Recognizer コンテナーをダウンロード、インストール、実行する方法について説明します。 コンテナーを使用すると、独自の環境で Form Recognizer サービスを実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 Form Recognizer 機能は、**レイアウト**、**名刺**、**身分証明書**、**レシート**、**請求書**、**カスタム** の 6 つの Form Recognizer 機能コンテナーによってサポートされています (レシート、名刺、身分証明書のコンテナーには、OCR の **読み取り** コンテナーも必要になります)。

## <a name="prerequisites"></a>前提条件

開始するには、アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)が必要です。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。

Form Recognizer コンテナーを使用するには、以下も必要です。

| 必須 | 目的 |
|----------|---------|
| **Docker に関する知識** | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な`docker`[用語とコマンドの知識](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)が必要です。 |
| **Docker Engine がインストールされている** | <ul><li>[ホスト コンピューター](#host-computer-requirements)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。</li><li> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 </li><li> **Windows** では、**Linux** コンテナーをサポートするように Docker を構成することも必要です。</li></ul>  |
|**Form Recognizer リソース** | Azure portal 内の [**単一サービスの Azure Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) または [**マルチサービスの Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) リソース。 コンテナーを使用するには、関連付けられている API キーとエンドポイント URI が必要です。 どちらの値も、Azure portal の Form Recognizer の **[キーとエンドポイント]** ページで入手できます。 <ul><li>**{FORM_RECOGNIZER_API_KEY}** : 利用可能な 2 つのリソース キーのどちらか。<li>**{FORM_RECOGNIZER_ENDPOINT_URI}** : 課金情報を追跡するために使用されるリソースのエンドポイント。</li></li></ul>|
| **Computer Vision API リソース** | **名刺、身分証明書、またはレシートを処理するには、Computer Vision リソースが必要です。** <ul><li>Azure リソース (REST API または SDK) または **cognitive-services-recognize-text** [コンテナー](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)のいずれかとして、テキスト認識機能にアクセスできます。 通常の[課金](#billing)の料金が適用されます。</li> <li>**cognitive-services-recognize-text** コンテナーを使用する場合は、Form Recognizer コンテナーの Computer Vision キーが、**cognitive-services-recognize-text** コンテナーに対して Computer Vision の `docker run` または `docker compose` コマンドで指定されたキーであることと、課金エンドポイントがコンテナーのエンドポイント (例: `http://localhost:5000`) であることを確認してください。 同じホスト上で Computer Vision コンテナーと Form Recognizer コンテナーの両方を一緒に使用する場合、既定のポート *5000* を使用してその両方を起動することはできません。 </li></ul></br>Computer Vision Azure クラウドまたは Cognitive Services コンテナーの API キーとエンドポイントの両方を渡します。<ul><li>**{COMPUTER_VISION_API_KEY}** : 利用可能な 2 つのリソース キーのどちらか。</li><li> **{COMPUTER_VISION_ENDPOINT_URI}** : 課金情報を追跡するために使用されるリソースのエンドポイント。</li></ul> |

|オプション|目的|
|---------|----------|
|**Azure CLI (コマンド ライン インターフェイス)** | [Azure CLI](/cli/azure/install-azure-cli) を使用すると、一連のオンライン コマンドを使用して Azure リソースを作成および管理できます。 Windows、macOS、Linux 環境にインストールでき、Docker コンテナーと Azure Cloud Shell で実行できます。 |
|||

## <a name="request-approval-to-run-the-container"></a>コンテナーを実行するための承認を要求する

コンテナーを実行するための承認を依頼するには、[限定サービスの申請フォーム](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)に記入して送信します。

このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、そのフォームは Azure Cognitive Services チームによって確認されます。その後、チームから決定事項がメールで届きます。

このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。 コンテナーの実行に使用する Azure リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。 Microsoft からのアプリケーションの状態に関する更新については、電子メール (受信トレイと迷惑フォルダーの両方) を確認してください。 承認されると、Microsoft Container Registry (MCR) からコンテナーをダウンロードした後、そのコンテナーを実行できるようになります。これについては、記事の後半で説明します。

## <a name="host-computer-requirements"></a>ホスト コンピューターの要件

ホストとは、Docker コンテナーを実行する x64 ベースのコンピューターのことです。 お客様のオンプレミス上のコンピューターを使用できるほか、次のような Azure 内の Docker ホスティング サービスを使用することもできます。

* [Azure Kubernetes Service](../../../aks/index.yml)。
* [Azure Container Instances](../../../container-instances/index.yml)。
* [Azure Stack](/azure-stack/operator) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスター。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

#### <a name="required-containers"></a>必要なコンテナー

次の表に、ダウンロードする Form Recognizer コンテナーごとの追加のサポート コンテナーを示します。 詳細については、「[課金](#billing)」セクションを参照してください。

| 機能コンテナー | サポート コンテナー |
|---------|-----------|
| **レイアウト** | なし |
| **名刺** | **Computer Vision 読み取り**|
| **身分証明書** | **Computer Vision 読み取り** |
| **請求書**   | **レイアウト** |
| **Receipt** |**Computer Vision 読み取り** |
| **Custom** | **カスタム API**、**カスタムの教師あり**、**レイアウト**|

#### <a name="recommended-cpu-cores-and-memory"></a>推奨される CPU コア数とメモリ

> [!Note]
> 最小値と推奨値は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものでは *ありません*。

##### <a name="read-layout-and-prebuilt-containers"></a>読み取り、レイアウト、および事前構築済みのコンテナー

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| Read 3.2 | 8 コア、16 GB のメモリ | 8 コア、24 GB のメモリ|
| Layout 2.1-preview | 8 コア、16 GB のメモリ | 8 コア、24 GB のメモリ |
| Business Card 2.1-preview | 2 コア、4 GB のメモリ | 4 コア、4 GB のメモリ |
| ID Document 2.1-preview | 1 コア、2 GB メモリ |2 コア、2 GB のメモリ |
| Invoice 2.1-preview | 4 コア、8 GB のメモリ | 8 コア、8 GB のメモリ |
| Receipt 2.1-preview |  4 コア、8 GB のメモリ | 8 コア、8 GB のメモリ  |

##### <a name="custom-containers"></a>カスタム コンテナー

要求の **トレーニングと分析** を行う場合は、次のホスト マシン要件が適用されます。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| カスタム API| 0.5 コア、0.5 GB のメモリ| 1 コア、1 GB のメモリ |
|カスタムの教師あり | 4 コア、2 GB のメモリ | 8 コア、4 GB のメモリ|

分析の呼び出しのみを行う場合、ホスト マシンの要件は次のとおりです。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
|カスタムの教師あり (分析) | 1 コア、0.5 GB | 2 コア、1 GB のメモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* コアとメモリは、`docker compose` または `docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用して、ダウンロードしたコンテナー イメージを一覧表示できます。 たとえば、次のコマンドは、ダウンロードした各コンテナー イメージの ID、リポジトリ、およびタグが表として書式設定されて表示されます。
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>**docker-compose up** コマンドを使用してコンテナーを実行する

* {ENDPOINT_URI} と {API_KEY} の値を、Azure リソース ページのリソース エンドポイント URI と API キーに置き換えます。

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="スクリーンショット: Azure portal の [キーとエンドポイント] ページ。":::

* EULA 値が "accept" に設定されていることを確認します。

* `EULA`、`Billing`、`ApiKey` の各値が指定されている必要があります。指定されてない場合、コンテナーは起動しません。

> [!IMPORTANT]
> サブスクリプション キーは、Form Recognizer リソースにアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

### <a name="layout"></a>[レイアウト](#tab/layout)

Form Recognizer レイアウト コンテナーを実行する自己完結型 `docker compose` の例を下に示します。  `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。 レイアウト コンテナー インスタンスの {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

これで、[**docker compose**](https://docs.docker.com/compose/) コマンドを使用してサービスを開始できます。

```bash
docker-compose up
```

### <a name="business-card"></a>[名刺](#tab/business-card)

Form Recognizer 名刺コンテナーおよび読み取りコンテナーをまとめて実行する自己完結型 `docker compose` の例を下に示します。 `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。 名刺コンテナー インスタンスの {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。 Computer Vision 読み取りコンテナーの {COMPUTER_VISION_ENDPOINT_URI} と{COMPUTER_VISION_API_KEY} を入力します。

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

これで、[**docker compose**](https://docs.docker.com/compose/) コマンドを使用してサービスを開始できます。

```bash
docker-compose up
```

### <a name="id-document"></a>[身分証明書](#tab/id-document)

Form Recognizer 身分証明書コンテナーおよび読み取りコンテナーをまとめて実行する自己完結型 `docker compose` の例を下に示します。 `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。 身分証明書コンテナーの {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。 Computer Vision 読み取りコンテナーの {COMPUTER_VISION_ENDPOINT_URI} と{COMPUTER_VISION_API_KEY} の値を入力します。

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

これで、[**docker compose**](https://docs.docker.com/compose/) コマンドを使用してサービスを開始できます。

```bash
docker-compose up
```

### <a name="invoice"></a>[請求書](#tab/invoice)

Form Recognizer 請求書コンテナーおよびレイアウト コンテナーをまとめて実行する自己完結型 `docker compose` の例を下に示します。 `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。  請求書コンテナーおよびレイアウト コンテナーの {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

これで、[**docker compose**](https://docs.docker.com/compose/) コマンドを使用してサービスを開始できます。

```bash
docker-compose up
```

### <a name="receipt"></a>[Receipt](#tab/receipt)

Form Recognizer レシート コンテナーおよび読み取りコンテナーをまとめて実行する自己完結型 `docker compose` の例を下に示します。 `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。 レシート コンテナーの {FORM_RECOGNIZER_ENDPOINT_URI} と {FORM_RECOGNIZER_API_KEY} の値を入力します。 Computer Vision 読み取りコンテナーの {COMPUTER_VISION_ENDPOINT_URI} と{COMPUTER_VISION_API_KEY} の値を入力します。

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

これで、[**docker compose**](https://docs.docker.com/compose/) コマンドを使用してサービスを開始できます。

```bash
docker-compose up
```

### <a name="custom"></a>[Custom](#tab/custom)

カスタム ドキュメントを処理するには、上記の[前提条件](#prerequisites)に加えて、以下を実行する必要があります。

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; 次のファイルを格納するフォルダーを作成する。

  1. [ **.env**](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; 入力データを格納するフォルダーを作成する

  1. このフォルダーに「**shared**」という名前を付けます。
  1. このフォルダーのファイル パスは、 **{SHARED_MOUNT_PATH}** として参照する予定です。
  1. ファイル パスを、"*Microsoft のメモ帳*" などの使いやすい場所にコピーします。 これは、下の **.env** ファイルに追加する必要があります。

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; ローカル コンピューターに Form Recognizer サービスによって書き込まれたログを格納するフォルダーを作成する

  1. このフォルダーに「**output**」という名前を付けます。
  1. このフォルダーのファイル パスは、 **{OUTPUT_MOUNT_PATH}** として参照する予定です。
  1. ファイル パスを、"*Microsoft のメモ帳*" などの使いやすい場所にコピーします。 これは、下の **.env** ファイルに追加する必要があります。

#### <a name="bullet-create-an-environment-file"></a>&bullet; 環境ファイルを作成する

  1. このファイルに「 **.env**」という名前を付けます。

  1. 以下の環境変数を宣言します。

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; **nginx** ファイルを作成する

  1. このファイルに「**nginx.conf**」という名前を付けます。

  1. 次の構成を入力します。

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/;

        }

        location /status {
            proxy_pass http://docker-api/status;

        }

        location /ready {
            proxy_pass http://docker-api/ready;

        }

        location /swagger {
            proxy_pass http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_set_header Host $host:$server_port;
            proxy_set_header Referer $scheme://$host:$server_port;
            proxy_pass http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* 同じ種類の少なくとも 6 つのフォームのセットをまとめます。 このデータを使用して、モデルのトレーニングとフォームのテストを行います。 [サンプル データセット](https://go.microsoft.com/fwlink/?linkid=2090451)を使用できます (*sample_data.zip* をダウンロードして展開します)。 上記で作成した **shared** フォルダーにトレーニング ファイルをダウンロードします。

* データにラベルを付ける場合は、[Windows 用の Form Recognizer サンプル ラベル付けツール](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga)をダウンロードします。 ダウンロードにより、ラベル付けツールの .exe ファイルがインポートされます。これを使用して、ローカル ファイル システムに存在するデータにラベルを付けます。 ダウンロード プロセス中に発生した警告は無視してかまいません。

#### <a name="create-a-new-sample-labeling-tool-project"></a>新しいサンプル ラベル付けツール プロジェクトを作成する

* サンプル ラベル付けツールの .exe ファイルをダブルクリックして、ラベル付けツールを開きます。
* ツールの左側のペインで、[connections]\(接続\) タブを選択します。
* [create a new project]\(新しいプロジェクトの作成\) を選択し、名前と説明を入力します。
* プロバイダーには、[local file system]\(ローカル ファイル システム\) オプションを選択します。 ローカル フォルダーには、サンプル データ ファイルを格納したフォルダーへのパスを必ず入力してください。
* [home]\(ホーム\) タブに戻り、[Use custom to train a model with labels and key value pairs]\(カスタムを使用してラベルとキーと値のペアを使用してモデルをトレーニングする\) オプションを選択します。
* 左側のペインで [train]\(トレーニング\) ボタンを選択して、ラベル付けされたモデルをトレーニングします。
* この接続を保存し、それを使用して要求にラベルを付けます。
* トレーニング済みのモデルに対して、任意のファイルの分析を選択できます。

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; **docker compose** ファイルを作成する

1. このファイルに「**docker-compose.yml**」という名前を付けます。

2. Form Recognizer レイアウト、ラベル ツール、カスタム API、カスタムの教師ありの各コンテナーをまとめて実行する自己完結型 `docker compose` の例を下に示します。 `docker compose` では、YAML ファイルを使用してアプリケーションのサービスを構成します。 次に、`docker-compose up` コマンドを使用して、構成からすべてのサービスを作成し、開始します。

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>サービスが実行されていることを確認する

サービスが稼働していることを確認するには、 Ubuntu シェルでこれらのコマンドを実行します。

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>新しい接続を作成する

* ツールの左側のペインで、 **[connections]\(接続\)** タブを選択します。
* **[create a new project]\(新しいプロジェクトの作成\)** を選択し、名前と説明を入力します。
* プロバイダーには、 **[local file system]\(ローカル ファイル システム\)** オプションを選択します。 ローカル フォルダーには、**サンプル データ** ファイルを格納したフォルダーへのパスを必ず入力してください。
* [home]\(ホーム\) タブに戻り、 **[Use custom to train a model with labels and key value pairs]\(カスタムを使用してラベルとキー値のペアを使用してモデルをトレーニングする\)** を選択します。
* 左側のペインで **[train]\(トレーニング\) ボタン** を選択して、ラベル付けされたモデルをトレーニングします。
* この接続を **保存** し、それを使用して要求にラベルを付けます。
* トレーニング済みのモデルに対して、任意のファイルの分析を選択できます。

---

## <a name="validate-that-the-service-is-running"></a>サービスが実行されていることを検証する

コンテナーが実行されていることを検証する方法は複数あります。

* コンテナーでは、コンテナーが実行されていることを視覚的に検証するために、`\` にホームページを用意しています。

* 使い慣れた Web ブラウザーを開いて、問題のコンテナーの外部 IP アドレスと公開ポートに移動できます。 以下に示した各種の要求 URL を使用して、コンテナーが実行中であることを確認します。 以下に示した例の要求 URL は `http://localhost:5000` ですが、実際のコンテナーは異なる可能性があります。 実際のコンテナーの **外部 IP アドレス** と公開ポートに移動していることに注意してください。

  要求 URL    | 目的
  ----------- | --------
  |**http://<span></span>localhost:5000/** | コンテナーには、ホーム ページが用意されています。
  |**http://<span></span>localhost:5000/ready**     | GET で要求することで、コンテナーがモデルに対するクエリを受け取る準備ができていることを確認できます。 この要求は Kubernetes の liveness probe と readiness probe に対して使用できます。
  |**http://<span></span>localhost:5000/status** | GET で要求することで、コンテナーを起動するために使用された API キーが有効であるかどうかを、エンドポイント クエリを発生させずに確認できます。 この要求は Kubernetes の liveness probe と readiness probe に対して使用できます。
  |**http://<span></span>localhost:5000/swagger** | コンテナーには、エンドポイントの完全なドキュメント一式と、 [Try it out]\(試してみる\) の機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="スクリーンショット: Azure コンテナーのウェルカム ページ。":::

## <a name="stop-the-containers"></a>コンテナーを停止する

コンテナーを停止するには、次のコマンドを使用します。

```console
docker-compose down
```

## <a name="billing"></a>課金

Form Recognizer コンテナーでは、Azure アカウントの Form Recognizer リソースを使用して、Azure に課金情報を送信します。

コンテナーへのクエリは、`ApiKey` に使用される Azure リソースの価格レベルで課金されます。 ドキュメントと画像の処理に使用されるコンテナー インスタンスごとに課金されます。 そのため、名刺機能を使用する場合は、Form Recognizer `BusinessCard` および `Compuer Vision Read` コンテナー インスタンスについて課金されます。 請求書機能の場合、Form Recognizer `Invoice` および `Layout` コンテナー インスタンスについて課金されます。 [Form Recognizer](https://azure.microsoft.com/pricing/details/form-recognizer/) と Computer Vision [読み取り機能](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)コンテナーの価格に関する記事を "*参照してください*"。

Azure Cognitive Services コンテナーは、計測または課金エンドポイントに接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に課金エンドポイントに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーによって、お客様のデータ (解析対象の画像やテキストなど) が Microsoft に送信されることはありません。

### <a name="connect-to-azure"></a>Azure に接続する

コンテナーには、実行する課金引数の値が必要です。 これらの値により、コンテナーは課金エンドポイントに接続することができます。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。 許可された時間枠内でコンテナーが Azure に接続しなかった場合、コンテナーは引き続き実行されますが、課金エンドポイントが復元されるまでクエリには対応しません。 接続は、10 ～15 分の同じ時間間隔で、10 回試行されます。 10 回以内に課金エンドポイントに接続できなかった場合、コンテナーによる要求の処理は停止されます。 課金のために Microsoft に送信される情報の例については、[Cognitive Services コンテナーについてよく寄せられる質問](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work)を参照してください。

### <a name="billing-arguments"></a>課金引数

[**docker-compose up**](https://docs.docker.com/engine/reference/commandline/compose_up/) コマンドは、次の 3 つのオプションのすべてに有効な値が指定された場合にコンテナーを起動します。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Cognitive Services リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みのリソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Cognitive Services リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | お客様がコンテナーのライセンスに同意したことを示します。<br/>このオプションの値は **accept** に設定する必要があります。 |

これらのオプションの詳細については、「[コンテナーの構成](form-recognizer-container-configuration.md)」を参照してください。

## <a name="summary"></a>まとめ

これで完了です。 この記事では、Form Recognizer コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて学習しました。 要約すると:

* Form Recognizer は、Docker 用の Linux コンテナーを 7 つ提供します。
* コンテナー イメージは mcr からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* コンテナーをインスタンス化するときには、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* [Form Recognizer コンテナーの構成設定](form-recognizer-container-configuration.md) 
