---
title: Java リモート監視ソリューションのデプロイ - Azure | Microsoft Docs
description: このチュートリアルでは、CLI を使用してリモート監視ソリューション アクセラレータをプロビジョニングする方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: dd696330c9ee78ef84ac9fcf85946c837ad5b824
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188018"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>CLI を使用してリモート監視ソリューション アクセラレータをデプロイする

このチュートリアルでは、リモート監視ソリューション アクセラレータをプロビジョニングする方法について説明します。 CLI を使用してソリューションをデプロイします。 azureiotsuite.com で Web ベースの UI を使用してソリューションをデプロイすることもできます。このオプションの詳細については、「[リモート監視ソリューション アクセラレータをデプロイする](quickstart-remote-monitoring-deploy.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

CLI を実行するには、[Node.js](https://nodejs.org/) がローカル コンピューターにインストールされている必要があります。

## <a name="install-the-cli"></a>CLI をインストールする

CLI をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI へのサインイン

ソリューション アクセラレータをデプロイする前に、次のように CLI を使用して Azure サブスクリプションにサインインする必要があります。

```cmd/sh
pcs login
```

画面の指示に従って、サインインを完了します。

## <a name="deployment-options"></a>デプロイ オプション

ソリューション アクセラレータをデプロイするときに、デプロイ プロセスを構成するいくつかのオプションがあります。

| オプション | 値 | 説明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard`、`local` | _基本_デプロイは、テストおよびデモを目的としており、すべてのマイクロサービスを単一の仮想マシンにデプロイします。 _標準_デプロイは、実稼働を目的としており、マイクロサービスを複数の仮想マシンにデプロイします。 _ローカル_ デプロイは、ローカル コンピューターでマイクロサービスを実行するように Docker コンテナーを構成し、クラウドで Azure サービス (ストレージや Cosmos DB など) を使用します。 |
| ランタイム | `dotnet`、`java` | マイクロサービスの言語の実装を選択します。 |

ローカル デプロイの使用方法の詳細については、「[Running the remote monitoring solution locally](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables)」(リモート監視ソリューションのローカルでの実行) をご覧ください。

## <a name="basic-vs-standard-deployments"></a>Basic デプロイとStandard デプロイ

### <a name="basic"></a>Basic
Basic デプロイは、ソリューションを紹介することを目的としています。 このデモのコストを減らすために、すべてのマイクロサービスが単一の仮想マシンでデプロイされますが、これは運用対応環境アーキテクチャとみなされません。

Standard デプロイ オプションは、スケーリングと拡張性のために構築された運用対応アーキテクチャをカスタマイズする準備ができたときに使用する必要があります。

Basic ソリューションを作成すると、有料で次の Azure サービスが Azure サブスクリプションにプロビジョニングされます。 

| Count | Resource                       | Type         | 用途 |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux Virtual Machine](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | マイクロサービスのホスト |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – Standard レベル | デバイス管理との通信 |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | 標準        | 構成データ、およびルール、アラーム、メッセージなどのデバイス テレメトリの格納 |  
| 1     | [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | 標準        | VM およびストリーミング チェックポイントのストレージ |
| 1     | [Web アプリケーション](https://azure.microsoft.com/services/app-service/web/)        |                 | フロント エンド Web アプリケーションのホスト |

### <a name="standard"></a>標準
標準デプロイは、開発者がニーズに合わせてカスタマイズし、拡張できる運用対応デプロイです。 信頼性と拡張性のため、アプリケーション マイクロサービスは Docker コンテナーとして構築され、オーケストレーター (既定では [Kubernetes](https://kubernetes.io/)) を使用してデプロイされます。 オーケストレーターは、デプロイ、拡張、およびアプリケーションの管理を担当します。

標準ソリューションを作成すると、有料で次の Azure サービスが Azure サブスクリプションにプロビジョニングされます。

| Count | Resource                                     | SKU/サイズ      | 用途 |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Linux virtual machines](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | 冗長性を備えたマイクロサービスをホストするための 1 つのマスターと 3 つのエージェント |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) オーケストレーター |
| 1     | [Azure IoT Hub][https://azure.microsoft.com/services/iot-hub/]                     | S2 – Standard レベル | デバイス管理、コマンドとコントロール |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | 標準        | 構成データ、およびルール、アラーム、メッセージなどのデバイス テレメトリの格納 |
| 5     | [Azure Storage アカウント](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | 標準        | VM ストレージ用に 4 つ、およびストリーミング チェックポイント用に 1 つ |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | SSL 経由のアプリケーション ゲートウェイ |

> これらのサービスの価格情報については、[こちら](https://azure.microsoft.com/pricing)をご覧ください。 サブスクリプションの使用量と請求の詳細については、[Azure Portal](https://portal.azure.com/) をご覧ください。

## <a name="deploy-the-solution-accelerator"></a>ソリューション アクセラレータのデプロイ

### <a name="example-deploy-net-version"></a>例: .NET バージョンのデプロイ

次の例では、リモート監視ソリューション アクセラレータの基本 .NET バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>例: Java バージョンのデプロイ

次の例では、リモート監視ソリューション アクセラレータの標準 Java バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs コマンド オプション

ソリューションをデプロイするため `pcs` コマンドを実行すると、以下が求められます。

- ソリューションの名前。 この名前は一意である必要があります。
- 使用する Azure サブスクリプション。
- 場所。
- マイクロサービスをホストする仮想マシンの資格情報です。 これらの資格情報を使用して、トラブルシューティングのために仮想マシンにアクセスすることができます。

`pcs` コマンドが終了したら、新しいソリューション アクセラレータのデプロイの URL が表示されます。 `pcs` コマンドでは、プロビジョニングされた IoT Hub の名前などの追加情報を含むファイル `{deployment-name}-output.json` も作成されます。

コマンド ライン パラメーターの詳細については、次を実行します。

```cmd/sh
pcs -h
```

CLI の詳細については、[CLI の使用方法](https://github.com/Azure/pcs-cli/blob/master/README.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は [ソリューション ダッシュボードの機能を確認](./quickstart-remote-monitoring-deploy.md) することです。

<!-- Next tutorials in the sequence -->
