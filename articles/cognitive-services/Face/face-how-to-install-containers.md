---
title: コンテナーのインストールと実行
titlesuffix: Face - Azure Cognitive Services
description: このチュートリアルでの Face のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: ac273ac7d4c6c371670e6b8a8170274602a5318b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53536544"
---
# <a name="install-and-run-containers"></a>コンテナーのインストールと実行

コンテナー化とは、アプリケーションまたはサービスをコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 アプリケーションまたはサービスの構成と依存関係がコンテナー イメージに含まれます。 コンテナー イメージは、ほとんどまたはまったく変更せずにコンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

Face には、画像中の人の顔を検出し、顔のパーツ (鼻や目など)、性別、年齢のほか、マシンが予測するその他の顔の特徴などの属性を識別する Face という Docker 用標準 Linux コンテナーが用意されています。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認したり、データベースと顏を比較して、似ている顏や同一の顔が既に存在するかどうかを調べたりできます。 また、同じ視覚的特徴を使用して、似た顔をグループに分けて整理することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="preparation"></a>準備

Face コンテナーを使用する前に、次の前提条件を満たす必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](../../aks/index.yml)、[Azure Container Instances](../../container-instances/index.yml)、または [Azure Stack](../../azure-stack/index.yml) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイすることもできます。 Kubernetes を Azure Stack にデプロイする方法の詳細については、「[Kubernetes を Azure Stack にデプロイする](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)」を参照してください。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。  

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

Face コンテナーには最低 1 つの CPU コア、2.6 ギガヘルツ (GHz) 以上、および 4 ギガバイト (GB) の割り当て済みメモリが必要ですが、2 つ以上の CPU コアと 6 GB 以上の割り当て済みメモリをお勧めします。

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

まず、[Cognitive Services Vision Containers Request フォーム](https://aka.ms/VisionContainersPreview)に入力して、送信し、Face コンテナーへのアクセスを要求する必要があります。 このフォームでは、ユーザー、会社、およびコンテナーを使用するユーザー シナリオに関する情報が要求されます。 送信すると、Azure Cognitive Services チームによってフォームがレビューされ、プライベート コンテナー レジストリにアクセスするための条件を満たしていることが確認されます。

> [!IMPORTANT]
> フォームでは、Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) アカウントに関連付けられた電子メール アドレスを使用する必要があります。

要求が承認されると、資格情報を取得して、プライベート コンテナー レジストリにアクセスする方法を説明する手順を記載した電子メールを受け取ります。

## <a name="create-a-face-resource-on-azure"></a>Azure での Face リソースの作成

Face コンテナーを使用する場合、Azure で Face リソースを作成する必要があります。 リソースの作成後、リソースのサブスクリプション キーとエンドポイント URL を使用して、コンテナーをインスタンス化します。 コンテナーのインスタンス化の詳細については、「[ダウンロードしたコンテナー イメージからのコンテナーのインスタンス化](#instantiate-a-container-from-a-downloaded-container-image)」を参照してください。

次の手順に従って、Face リソースを作成し、Face リソースから情報を取得します。

1. Azure portal で、Face リソースを作成します。  
   Face コンテナーを使用する場合、まず Azure portal で対応する Face リソースを作成する必要があります。 詳細については、「[クイック スタート: Azure portal で Cognitive Services アカウントを作成する](../cognitive-services-apis-create-account.md)」を参照してください。

1. Azure リソースのエンドポイント URL とサブスクリプション キーを取得します。  
   Azure リソースを作成したら、そのリソースのエンドポイント URL とサブスクリプション キーを使用して、対応する Face コンテナーをインスタンス化する必要があります。 Azure portal で Face リソースのクイック スタートとキーのそれぞれのページから、エンドポイント URL とサブスクリプション キーをコピーできます。

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

Face コンテナーのコンテナー イメージは、Azure Container Registry 内の `containerpreview.azurecr.io` というプライベート Docker コンテナー レジストリから入手できます。 Face コンテナーのコンテナー イメージをリポジトリからダウンロードして、コンテナーをローカルで実行する必要があります。

リポジトリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。 たとえば、リポジトリから最新の Face コンテナー イメージをダウンロードするには、次のコマンドを使用します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

Face コンテナーで使用可能なタグの詳細については、Docker Hub の[テキスト認識](https://go.microsoft.com/fwlink/?linkid=2018655)に関するページを参照してください。

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用して、ダウンロードしたコンテナー イメージを一覧表示できます。 たとえば、次のコマンドは、ダウンロードした各コンテナー イメージの ID、リポジトリ、およびタグが表として書式設定されて表示されます。
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>ダウンロードしたコンテナー イメージからのコンテナーのインスタンス化

ダウンロードしたコンテナー イメージからコンテナーをインスタンス化するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 たとえば、次のコマンドは:

* Face コンテナー イメージからコンテナーをインスタンス化します
* 2 つの CPU コアと 6 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-tty を割り当てます
* コンテナーの終了後にそれを自動的に削除します

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> コンテナーをインスタンス化するには、`Eula`、`Billing`、および `ApiKey` オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

インスタンス化したら、コンテナーのホスト URI を使用して、コンテナーから操作を呼び出すことができます。 たとえば、次のホスト URI は、前の例でインスタンス化された Face コンテナーを表しています。

```http
http://localhost:5000/
```

> [!TIP]
> インスタンス化されたコンテナーの `/swagger` 相対 URI から、そのコンテナーによってサポートされる操作について説明する [OpenAPI 仕様](https://swagger.io/docs/specification/about/) (以前の Swagger 仕様) にアクセスできます。 たとえば、次の URI から、前の例でインスタンス化された Face コンテナーの OpenAPI 仕様にアクセスできます。
>
>  ```http
>  http://localhost:5000/swagger
>  ```

コンテナーから使用可能な [REST API 操作を呼び出す](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage)か、または [Azure Cognitive Services Face クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/)のクライアント ライブラリを使用して、それらの操作を呼び出すことができます。  
> [!IMPORTANT]
> コンテナーでクライアント ライブラリを使用する場合は、Azure Cognitive Services Face クライアント ライブラリ バージョン 2.0 以降が必要です。

コンテナーから特定の操作を呼び出すことと、Azure の対応するサービスから同じ操作を呼び出すことの違いは、操作を呼び出すために、Azure リージョンのホスト URI ではなく、コンテナーのホスト URI を使用することだけです。 たとえば、米国西部 Azure リージョンで実行されている Face インスタンスを使用して、顔を検出する場合は、次の REST API 操作を呼び出します。

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

ローカル コンピューターで、実行されている Face コンテナーをその既定の構成で使用して、顏を検出する場合は、次の REST API 操作を呼び出します。

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>課金

Face コンテナーは、Azure アカウントの対応する Face リソースを使用して、Azure に課金情報を送信します。 Face コンテナーによって、課金目的で次のコマンド ライン オプションが使用されます。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Face リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みの Face Azure リソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Face リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Face Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |

> [!IMPORTANT]
> 3 つすべてのオプションに有効な値が指定されている必要があります。そうでないと、コンテナーが起動しません。

これらのオプションの詳細については、「[コンテナーの構成](face-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Face コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Face は、人のデータベースを使用して、顔を検出するか、顔を識別する Face という Docker 用の 1 つの Linux コンテナーを提供します。
* Azure のプライベート コンテナー レジストリからコンテナー イメージがダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Face コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](face-resource-container-config.md)を確認する
* [Face の概要](Overview.md)を確認して、顔の検出と識別の詳細を参照する  
* コンテナーでサポートされるメソッドの詳細について、[Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) を参照する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Face 機能に関連する問題を解決する。
