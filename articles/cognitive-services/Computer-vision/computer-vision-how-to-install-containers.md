---
title: コンテナーのインストールと実行
titlesuffix: Computer Vision - Azure Cognitive Services
description: このチュートリアルでの Computer Vision のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 9964be07c578cd1cafd93328bffe972483123e03
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077037"
---
# <a name="install-and-run-containers"></a>コンテナーのインストールと実行

コンテナー化とは、アプリケーションまたはサービスをコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 アプリケーションまたはサービスの構成と依存関係がコンテナー イメージに含まれます。 コンテナー イメージは、ほとんどまたはまったく変更せずにコンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

Computer Vision のテキスト認識部分も Docker コンテナーとして利用できます。 レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトのイメージから、印刷されたテキストを検出して、抽出できます。  
> [!IMPORTANT]
> テキスト認識コンテナーは現在のところ、英語でのみ機能します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="preparation"></a>準備

テキスト認識コンテナーを使用する前に、次の前提条件を満たす必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](/azure/aks/)、[Azure Container Instances](/azure/container-instances/)、または [Azure Stack](/azure/azure-stack/) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイすることもできます。 Kubernetes を Azure Stack にデプロイする方法の詳細については、「[Kubernetes を Azure Stack にデプロイする](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」を参照してください。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。  

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

テキスト認識コンテナーには最低 1 つの CPU コア、2.6 ギガヘルツ (GHz) 以上、8 ギガバイト (GB) の割り当て済みメモリが必要ですが、2 つ以上の CPU コアと 8 GB 以上の割り当て済みメモリをお勧めします。

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

まず、[Cognitive Services Vision Containers Request フォーム](https://aka.ms/VisionContainersPreview)に入力して、送信し、テキスト認識コンテナーへのアクセスを要求する必要があります。 このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 送信すると、Azure Cognitive Services チームによってフォームがレビューされ、プライベート コンテナー レジストリにアクセスするための条件を満たしていることが確認されます。

> [!IMPORTANT]
> フォームでは、Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) アカウントに関連付けられた電子メール アドレスを使用する必要があります。

要求が承認されると、資格情報を取得して、プライベート コンテナー レジストリにアクセスする方法を説明する手順を記載した電子メールを受け取ります。

## <a name="create-a-computer-vision-resource-on-azure"></a>Azure で Computer Vision リソースを作成する

テキスト認識コンテナーを使用する場合、Azure で Computer Vision リソースを作成する必要があります。 リソースの作成後、リソースのサブスクリプション キーとエンドポイント URL を使用して、コンテナーをインスタンス化します。 コンテナーのインスタンス化の詳細については、「[ダウンロードしたコンテナー イメージからのコンテナーのインスタンス化](#instantiate-a-container-from-a-downloaded-container-image)」を参照してください。

次の手順に従って、Azure リソースを作成し、Azure リソースから情報を取得します。

1. Azure portal で Azure リソースを作成します。  
   テキスト認識コンテナーを使用する場合、まず Azure portal で対応する Computer Vision リソースを作成する必要があります。 詳細については、「[クイック スタート: Azure portal で Cognitive Services アカウントを作成する](../cognitive-services-apis-create-account.md)」を参照してください。

1. Azure リソースのエンドポイント URL とサブスクリプション キーを取得します。  
   Azure リソースを作成したら、そのリソースのエンドポイント URL とサブスクリプション キーを使用して、対応するテキスト認識コンテナーをインスタンス化する必要があります。 Azure portal で Computer Vision リソースのクイック スタートとキーのそれぞれのページから、エンドポイント URL とサブスクリプション キーをコピーできます。

## <a name="log-in-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのログイン

Cognitive Services コンテナーのプライベート コンテナー レジストリを認証する方法はいくつかありますが、コマンドラインからの推奨される方法は、[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用することです。

次の例のように [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用し、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルで資格情報をセキュリティ保護した場合は、次の例に示すように、`cat` コマンドを使用して、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 *\<passwordFile\>* を、パスワードを含むテキスト ファイルのパスと名前に置き換え、*\<username\>* を資格情報に指定されているユーザー名に置き換えます。

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>プライベート コンテナー レジストリからのコンテナー イメージのダウンロード

テキスト認識コンテナーのコンテナー イメージは、Azure Container Registry 内の `containerpreview.azurecr.io` というプライベート Docker コンテナー レジストリから入手できます。 テキスト認識コンテナーのコンテナー イメージをリポジトリからダウンロードして、コンテナーをローカルで実行する必要があります。

リポジトリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。 たとえば、リポジトリから最新のテキスト認識コンテナー イメージをダウンロードするには、次のコマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

テキスト認識コンテナーで使用可能なタグの詳細については、Docker Hub の[テキスト認識](https://go.microsoft.com/fwlink/?linkid=2018655)に関するページを参照してください。

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用して、ダウンロードしたコンテナー イメージを一覧表示できます。 たとえば、次のコマンドは、ダウンロードした各コンテナー イメージの ID、リポジトリ、およびタグが表として書式設定されて表示されます。
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>ダウンロードしたコンテナー イメージからのコンテナーのインスタンス化

ダウンロードしたコンテナー イメージからコンテナーをインスタンス化するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 たとえば、次のコマンドでは次の処理が行われます。

* テキスト認識コンテナー イメージからのコンテナーのインスタンス化
* 2 つの CPU コアと 8 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

インスタンス化したら、コンテナーのホスト URI を使用して、コンテナーから操作を呼び出すことができます。 たとえば、次のホスト URI は、前の例でインスタンス化されたテキスト認識コンテナーを表しています。

```http
http://localhost:5000/
```

> [!IMPORTANT]
> インスタンス化されたコンテナーの `/swagger` 相対 URI から、そのコンテナーによってサポートされる操作について説明する [OpenAPI 仕様](https://swagger.io/docs/specification/about/) (以前の Swagger 仕様) にアクセスできます。 たとえば、次の URI から、前の例でインスタンス化されたテキスト認識コンテナーの OpenAPI 仕様にアクセスできます。
>
>  ```http
>  http://localhost:5000/swagger
>  ```

コンテナーから使用可能な [REST API 操作を呼び出して](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi)同期または非同期でテキストを認識するか、[Azure Cognitive Services Computer Vision SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) クライアント ライブラリのクライアント ライブラリを使用してそれらの操作を呼び出すことができます。  
> [!IMPORTANT]
> コンテナーでクライアント ライブラリを使用する場合は、Cognitive Services Computer Vision SDK version 3.2.0 以降が必要です。

### <a name="asynchronous-text-recognition"></a>非同期のテキスト認識

Computer Vision サービスで該当する REST 操作を使用する方法と同じように、`POST /vision/v2.0/recognizeText` 操作と `GET /vision/v2.0/textOperations/*{id}*` 操作を同時に使用し、イメージ内の印刷テキストを非同期認識できます。 テキスト認識コンテナーでは現在のところ、印刷されたテキストのみ認識され、手書きのテキストは認識されません。そのため、Computer Vision サービス操作に通常指定される `mode` パラメーターはテキスト認識コンテナーで無視されます。

### <a name="synchronous-text-recognition"></a>同期のテキスト認識

`POST /vision/v2.0/recognizeTextDirect` 操作を使用し、イメージ内の印刷されたテキストが同期認識されます。 この操作は同期のため、この操作の要求本文は `POST /vision/v2.0/recognizeText` 操作のそれと同じになりますが、この操作の応答本文は `GET /vision/v2.0/textOperations/*{id}*` 操作によって返されるそれと同じになります。

### <a name="billing"></a>課金

テキスト認識コンテナーは、Azure アカウントの対応する Computer Vision リソースを使用して、Azure に課金情報を送信します。 テキスト認識コンテナーによって課金目的で次のオプションが使用されます。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Computer Vision リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みの Computer Vision Azure リソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Computer Vision リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Computer Vision Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |

> [!IMPORTANT]
> 3 つすべてのオプションに有効な値が指定されている必要があります。そうでないと、コンテナーが起動しません。

これらのオプションの詳細については、「[コンテナーの構成](computer-vision-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Computer Vision コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Computer Vision からは、印刷されたテキストを検出し、抽出するための Docker 用 Linux コンテナーが 1 つ提供されます。
* Azure のプライベート コンテナー レジストリからコンテナー イメージがダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Computer Vision コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](computer-vision-resource-container-config.md)を確認する
* [Computer Vision の概要](Home.md)ページを読み、印刷されたテキストと手書きのテキストの認識の詳細について確認する  
* コンテナーによりサポートされるメソッドの詳細を [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) ページで確認する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Computer Vision 機能に関連する問題を解決する。
