---
title: "Java リモート監視ソリューションのデプロイ - Azure | Microsoft Docs"
description: "このチュートリアルでは、リモート監視の事前構成済みソリューション Java マイクロサービスを CLI を使用してプロビジョニングする方法について説明します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: ea3764299d07f548abbc2857a3adbfb4dc50dec8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>CLI を使用してリモート監視の構成済みソリューションをデプロイする

このチュートリアルでは、リモート監視の事前構成済みソリューションをプロビジョニングする方法について説明します。 CLI を使用してソリューションをデプロイします。 azureiotsuite.com で Web ベースの UI を使用してソリューションをデプロイすることもできます。このオプションの詳細については、「[リモート監視の事前構成済みソリューションをデプロイする](iot-suite-remote-monitoring-deploy.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

リモート監視構成済みソリューションをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

CLI を実行するには、[Node.js](https://nodejs.org/) がローカル コンピューターにインストールされている必要があります。

## <a name="install-the-cli"></a>CLI をインストールする

CLI をインストールするには、コマンド ライン環境で、次のコマンドを実行します。

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>CLI へのサインイン

構成済みのソリューションをデプロイする前に、次のように CLI を使用して Azure サブスクリプションにサインインする必要があります。

```cmd/sh
pcs login
```

画面の指示に従って、サインインを完了します。

## <a name="deployment-options"></a>デプロイ オプション

構成済みのソリューションをデプロイするときに、デプロイ プロセスを構成するいくつかのオプションがあります。

| オプション | 値 | 説明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard` | _基本_デプロイは、テストおよびデモを目的としており、すべてのマイクロサービスを単一の仮想マシンにデプロイします。 _標準_デプロイは、実稼働を目的としており、マイクロサービスを複数の仮想マシンにデプロイします。 |
| ランタイム | `dotnet`、`java` | マイクロサービスの言語の実装を選択します。 |

## <a name="deploy-the-preconfigured-solution"></a>構成済みソリューションのデプロイ

### <a name="example-deploy-net-version"></a>例: .NET バージョンのデプロイ

次の例では、リモート監視の構成済みソリューションの基本 .NET バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>例: Java バージョンのデプロイ

次の例では、リモート監視の構成済みソリューションの標準 Java バージョンをデプロイする方法を示します。

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs コマンド オプション

ソリューションをデプロイするため `pcs` コマンドを実行すると、以下が求められます。

- ソリューションの名前。 この名前は一意である必要があります。
- 使用する Azure サブスクリプション。
- 場所。
- マイクロサービスをホストする仮想マシンの資格情報です。 これらの資格情報を使用して、トラブルシューティングのために仮想マシンにアクセスすることができます。

`pcs` コマンドが終了したら、新しい構成済みのソリューションのデプロイの URL が表示されます。 `pcs` コマンドでは、プロビジョニングされた IoT Hub の名前などの追加情報を含むファイル `{deployment-name}-output.json` も作成されます。

コマンド ライン パラメーターの詳細については、次を実行します。

```cmd/sh
pcs -h
```

CLI の詳細については、[CLI の使用方法](https://github.com/Azure/pcs-cli/blob/master/README.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 構成済みソリューションの構成
> * 構成済みソリューションのデプロイ
> * 構成済みソリューションへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](./iot-suite-remote-monitoring-deploy.md)することです。

<!-- Next tutorials in the sequence -->