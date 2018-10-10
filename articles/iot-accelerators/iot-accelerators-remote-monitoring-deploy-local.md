---
title: リモート監視ソリューションをローカルにデプロイする - Azure | Microsoft Docs
description: この攻略ガイドでは、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターに展開する方法を示します。
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407435"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>リモート監視ソリューション アクセラレータをローカルでデプロイする

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 この記事で説明する方法では、マイクロサービスをローカル Docker コンテナーに展開し、クラウド内で IoT Hub、Cosmos DB、および Azure Time Series Insights サービスを使用します。 ローカル コンピューター上の IDE でリモート監視ソリューション アクセラレータを実行する方法については、GitHub の「[Starting Microservices on local environment](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md)」(ローカル環境でのマイクロサービスの開始) をご覧ください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) - このソフトウェアは、スクリプトが Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> これらのツールは、Windows、Linux、iOS を含む多数のプラットフォームで使用できます。

### <a name="download-the-source-code"></a>ソース コードをダウンロードする

リモート監視ソース コードの GitHub リポジトリには、マイクロサービスを含む Docker イメージをダウンロード、構成、および実行するために必要な Docker 構成ファイルが含まれています。 複製してローカル バージョンのリポジトリを作成するには、コマンド ライン環境を使用してローカル コンピューター上の適切なフォルダーに移動してから、次のいずれかのコマンドを実行します。

Java マイクロサービスの実装の最新バージョンをダウンロードするには、次のコマンドを実行します。

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

.NET マイクロサービスの実装の最新バージョンをダウンロードするには、次のコマンドを実行します。

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> これらのコマンドでは、マイクロサービスのローカル実行に使用するスクリプトだけでなく、すべてのマイクロサービスのソース コードがダウンロードされます。 Docker 内のマイクロサービスを実行するためのソース コードは必要ありませんが、後でソリューション アクセラレータを変更し、その変更をローカルにテストすることを予定している場合は、ソース コードが役に立ちます。

## <a name="deploy-the-azure-services"></a>Azure サービスをデプロイする

この記事ではマイクロサービスをローカルに実行する方法を示しますが、これらはクラウド内で実行されている Azure サービスに依存します。 これらの Azure サービスは、[Azure portal から手動で](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup)、または提供されているスクリプトを使用して、デプロイできます。 次のスクリプトの例では、Windows コンピューターで .NET リポジトリを使用していることを前提としています。 別の環境で作業している場合は、パス、ファイル拡張子、およびパスの区切り記号を適切に調整してください。 提供されているスクリプトを使用するには、次のようにします。

### <a name="create-new-azure-resources"></a>新しい Azure リソースを作成する

必要な Azure リソースをまだ作成していない場合は、次の手順のようにします。

1. コマンド ライン環境で、リポジトリの複製したコピーの **remote-monitoring-services-dotnet\scripts\local\launch** フォルダーに移動します。

2. **start.cmd** スクリプトを実行し、指示に従います。 スクリプトで、Azure アカウントにサインインしてスクリプトを再起動するように求められます。 その後、スクリプトで次の情報の入力を求められます。
    * ソリューション名。
    * 使用する Azure サブスクリプション。
    * 使用する Azure データセンターの場所。

    スクリプトで、指定したソリューション名のリソース グループが Azure に作成されます。 このリソース グループには、ソリューション アクセラレータが使用する Azure リソースが含まれます。

3. スクリプトが完了すると、環境変数の一覧が表示されます。 コマンドからの出力の指示に従って、これらの変数を **remote-monitoring-services-dotnet\\scripts\\local\\.env** ファイルに保存します。

### <a name="use-existing-azure-resources"></a>既存の Azure リソースを使用する

必要な Azure リソースを既に作成してある場合は、**remote-monitoring-services-dotnet\\scripts\\local\\.env** ファイルの環境変数定義を必要な値で編集します。 **.env** ファイルには、必要な値を検索する場所についての詳細な情報が含まれています。

## <a name="run-the-microservices-in-docker"></a>Docker 内のマイクロサービスを実行する

ローカルの Docker コンテナーで実行されているマイクロサービスは、Azure で実行されているサービスにアクセスする必要があります。 次のコマンドを使用して、お使いの Docker 環境のインターネット接続をテストできます。このコマンドは、小さいコンテナーを起動し、インターネット アドレスに ping を試みます。

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

ソリューション アクセラレータを実行するには、コマンド ライン環境で **remote-monitoring-services-dotnet\\scripts\\local** フォルダーに移動し、次のコマンドを実行します。

```cmd\sh
docker-compose up
```

このコマンドを初めて実行すると、Docker は Docker ハブからマイクロサービス イメージをダウンロードして、コンテナーをローカルに構築します。 以降の実行で、Docker は直ちにコンテナーを実行します。

個別のシェルを使用して、コンテナーからのログを表示できます。 最初に、`docker ps -a` コマンドを使用してコンテナー ID を見つけます。 次に、`docker logs {container-id} --tail 1000` を使用して、指定されたコンテナーの最後の 1000 個のログ エントリを表示します。

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで [http://localhost:8080](http://localhost:8080) に移動します。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除する最も簡単な方法は、[Azure portal](https://ms.portal.azure.com) に移動し、**start.cmd** スクリプトを実行したときに作成されたリソース グループを削除することです。

Docker イメージを削除し、ローカル コンピューター上の領域を解放するには、`docker-compose down --rmi all` コマンドを使用します。 また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * ローカルの開発環境の設定
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は [ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md) することです。

<!-- Next tutorials in the sequence -->
