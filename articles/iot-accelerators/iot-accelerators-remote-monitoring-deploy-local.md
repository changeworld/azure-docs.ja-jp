---
title: リモート監視ソリューションをローカルにデプロイする - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188662"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>リモート監視ソリューション アクセラレータをローカルでデプロイする

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 このアプローチでは、マイクロサービスをローカル Docker コンテナーにデプロイし、クラウド内で IoT Hub、Cosmos DB、および Azure ストレージ サービスを使用します。 ソリューション アクセラレータ (PCS) CLI を使用して Azure クラウド サービスをデプロイします。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) - このソフトウェアは PCS CLI の前提条件です。
* PCS CLI
* ソース コードのローカル リポジトリ

> [!NOTE]
> これらのツールは、Windows、Linux、iOS を含む多数のプラットフォームで使用できます。

### <a name="install-the-pcs-cli"></a>PCS CLI をインストールする

npm で PCS CLI をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

```cmd/sh
npm install iot-solutions -g
```

CLI の詳細については、[CLI の使用方法](https://github.com/Azure/pcs-cli/blob/master/README.md)に関する記事をご覧ください。

### <a name="download-the-source-code"></a>ソース コードをダウンロードする

 リモート監視ソース コード リポジトリには、マイクロサービスを含む Docker イメージをダウンロード、構成、および実行するために必要な Docker 構成ファイルが含まれています。 複製してローカル バージョンのリポジトリを作成するには、お気に入りのコマンド ラインまたはターミナルを使用してローカル コンピューター上に適切なフォルダーに移動し、次のいずれかのコマンドを実行します。

マイクロサービスの Java 実装をインストールするには、次を実行します。

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

マイクロサービスの .Net 実装をインストールするには、次を実行します。

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

リモート監視構成済みソリューションのリポジトリとサブモジュール [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> これらのコマンドは、すべてのマイクロサービスのソース コードをダウンロードします。 Docker 内のマイクロサービスを実行するためのソース コードは必要ありませんが、後で構成済みソリューションを変更し、その変更をローカルにテストすることを予定している場合はこのソース コードが役立ちます。

## <a name="deploy-the-azure-services"></a>Azure サービスをデプロイする

この記事ではマイクロサービスをローカルに実行する方法を示しますが、これらのマイクロサービスは、クラウド内で実行されている 3 つの Azure サービスに依存します。 これらの Azure サービスは [Azure Portal から手動で](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup)、または PCS CLI を使用してデプロイできます。 この記事では、`pcs` ツールを使用する方法を示します。

### <a name="sign-in-to-the-cli"></a>CLI へのサインイン

ソリューション アクセラレータをデプロイする前に、次のように CLI を使用して Azure サブスクリプションにサインインする必要があります。

```cmd/sh
pcs login
```

画面の指示に従って、サインインを完了します。 CLI 内部の場所はクリックしないでください。ログインが失敗する可能性があります。 ログインを完了すると、ログインに成功したというメッセージが CLI に表示されます。 

### <a name="run-a-local-deployment"></a>ローカル デプロイを実行する

ローカル デプロイを開始するには、次のコマンドを使用します。 必要な Azure リソースが作成され、コンソールに環境変数が出力されます。 

```cmd/pcs
pcs -s local
```

このスクリプトでは、次の情報を入力するよう求められます。

* ソリューション名。
* 使用する Azure サブスクリプション。
* 使用する Azure データセンターの場所。

> [!NOTE]
> このスクリプトは、Azure サブスクリプションのリソース グループ内に IoT Hub インスタンス、Cosmos DB インスタンス、および Azure ストレージ アカウントを作成します。 このリソース グループの名前は、上の `pcs` ツールを実行したときに選択したソリューションの名前です。 

> [!IMPORTANT]
> このスクリプトは、実行するのに数分かかります。 それが完了すると、メッセージ `Copy the following environment variables to /scripts/local/.env file:` が表示されます。 メッセージの後に表示される環境変数の定義をメモしておきます。これは、後の手順で使用します。

## <a name="run-the-microservices-in-docker"></a>Docker 内のマイクロサービスを実行する

Docker 内のマイクロサービスを実行するには、まず、上記の前の方の手順で複製したリポジトリのローカル コピー内の **scripts\\local\\.env** ファイルを編集します。 このファイルの内容全体を、直前の手順で `pcs` コマンドを実行したときにメモした環境変数の定義に置き換えます。 これらの環境変数は、Docker コンテナー内のマイクロサービスが `pcs` ツールによって作成された Azure サービスに接続できるようにします。

ソリューション アクセラレータを実行するには、コマンド ライン環境で **scripts\local** フォルダーに移動し、次のコマンドを実行します。

```cmd\sh
docker-compose up
```

このコマンドを初めて実行すると、Docker は Docker ハブからマイクロサービス イメージをダウンロードして、コンテナーをローカルに構築します。 以降の実行で、Docker は直ちにコンテナーを実行します。

個別のシェルを使用して、コンテナーからのログを表示できます。 最初に、`docker ps -a` コマンドを使用してコンテナー ID を見つけます。 次に、`docker logs {container-id} --tail 1000` を使用して、指定されたコンテナーの最後の 1000 個のログ エントリを表示します。

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで [http://localhost:8080](http://localhost:8080) に移動します。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除するための最も簡単な方法として、[Azure Portal](https://ms.portal.azure.com) に移動し、`pcs` ツールを使って作成したリソース グループを削除します。

Docker イメージを削除し、ローカル コンピューター上の領域を解放するには、`docker-compose down --rmi all` コマンドを使用します。 また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ローカルの開発環境の設定
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は [ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md) することです。

<!-- Next tutorials in the sequence -->