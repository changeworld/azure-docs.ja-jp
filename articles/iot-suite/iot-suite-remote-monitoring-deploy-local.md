---
title: リモート監視ソリューションをローカルにデプロイする - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視構成済みソリューションをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>リモート監視構成済みソリューションをローカルにデプロイする

この記事では、リモート監視構成済みソリューションをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 このアプローチでは、マイクロサービスをローカル Docker コンテナーにデプロイし、クラウド内で IoT Hub、Cosmos DB、および Azure ストレージ サービスを使用します。 構成済みソリューション (PCS) CLI を使用して Azure クラウド サービスをデプロイします。

## <a name="prerequisites"></a>前提条件

リモート監視構成済みソリューションによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) - このソフトウェアは PCS CLI の前提条件です。
* PCS CLI

> [!NOTE]
> これらのツールは、Windows、Linux、iOS を含む多数のプラットフォームで使用できます。

### <a name="install-the-pcs-cli"></a>PCS CLI をインストールする

CLI をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

```cmd/sh
npm install iot-solutions -g
```

CLI の詳細については、[CLI の使用方法](https://github.com/Azure/pcs-cli/blob/master/README.md)に関する記事をご覧ください。

## <a name="deploy-the-azure-services"></a>Azure サービスをデプロイする

この記事ではマイクロサービスをローカルに実行する方法を示しますが、これらのマイクロサービスは、クラウド内で実行されている 3 つの Azure サービスに依存します。 これらの Azure サービスは Azure Portal から手動で、または PCS CLI を使用してデプロイできます。 この記事では、`pcs` ツールを使用する方法を示します。

### <a name="sign-in-to-the-cli"></a>CLI へのサインイン

構成済みのソリューションをデプロイする前に、次のように CLI を使用して Azure サブスクリプションにサインインする必要があります。

```cmd/sh
pcs login
```

画面の指示に従って、サインインを完了します。

### <a name="run-a-local-deployment"></a>ローカル デプロイを実行する

ローカル デプロイを開始するには、次のコマンドを使用します。

```cmd/pcs
pcs -s local
```

このスクリプトでは、次の情報を入力するよう求められます。

* ソリューション名。
* 使用する Azure サブスクリプション。
* 使用する Azure データセンターの場所。

このスクリプトは、Azure サブスクリプションのリソース グループ内に IoT Hub インスタンス、Cosmos DB インスタンス、および Azure ストレージ アカウントを作成します。 このリソース グループの名前は、`pcs` ツールを実行するときに選択したソリューションの名前です。

このスクリプトは、実行するのに数分かかります。 それが完了すると、メッセージ `Copy the following environment variables to /scripts/local/.env file:` が表示されます。 メッセージの後に表示される環境変数の定義をコピーします。これは、後の手順で使用します。

## <a name="download-the-source-code"></a>ソース コードをダウンロードする

リモート監視ソース コード リポジトリには、マイクロサービスを含む Docker イメージをダウンロード、構成、および実行するために必要な Docker 構成ファイルが含まれています。 このリポジトリを複製するには、ローカル コンピューター上の適切なフォルダーに移動し、次のコマンドのいずれかを実行します。

マイクロサービスの Java 実装をインストールするには、次を実行します。

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

マイクロサービスの .Net 実装をインストールするには、次を実行します。

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

これらのコマンドは、すべてのマイクロサービスのソース コードをダウンロードします。 Docker 内のマイクロサービスを実行するためのソース コードは必要ありませんが、後で構成済みソリューションを変更し、その変更をローカルにテストすることを予定している場合はこのソース コードが役立ちます。

## <a name="run-the-microservices-in-docker"></a>Docker 内のマイクロサービスを実行する

Docker 内のマイクロサービスを実行するには、まずリポジトリのローカル コピー内の **scripts\\local\\.env** ファイルを編集します。 このファイルの内容全体を、前に `pcs` コマンドを実行したときにメモした環境変数の定義に置き換えます。 これらの環境変数は、Docker コンテナー内のマイクロサービスが `pcs` ツールによって作成された Azure サービスに接続できるようにします。

構成済みソリューションを実行するには、コマンド ライン環境で **scripts\local** フォルダーに移動し、次のコマンドを実行します。

```cmd\sh
docker-compose up
```

このコマンドを初めて実行すると、Docker は Docker ハブからマイクロサービス イメージをダウンロードして、コンテナーをローカルに構築します。 以降の実行で、Docker は直ちにコンテナーを実行します。

個別のシェルを使用して、コンテナーからのログを表示できます。 最初に、`docker ps -a` コマンドを使用してコンテナー ID を見つけます。 次に、`docker logs {container-id} --tail 1000` を使用して、指定されたコンテナーの最後の 1000 個のログ エントリを表示します。

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで [http://localhost:8080](http://localhost:8080) に移動します。

## <a name="tidy-up"></a>片付ける

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除するための最も簡単な方法として、Azure Portal を使用して、`pcs` ツールによって作成されたリソース グループを削除します。

Docker イメージを削除し、ローカル コンピューター上の領域を解放するには、`docker-compose down --rmi all` コマンドを使用します。 また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 構成済みソリューションの構成
> * 構成済みソリューションのデプロイ
> * 構成済みソリューションへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](./iot-suite-remote-monitoring-deploy.md)することです。

<!-- Next tutorials in the sequence -->