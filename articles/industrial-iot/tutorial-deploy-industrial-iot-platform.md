---
title: Azure Industrial IoT プラットフォームをデプロイする
description: このチュートリアルでは、IIoT プラットフォームをデプロイする方法について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787408"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>チュートリアル: Azure Industrial IoT プラットフォームをデプロイする

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * IIoT プラットフォームの主要コンポーネントについて
> * さまざまなインストールの種類について
> * Industrial IoT プラットフォームをデプロイする方法

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションを作成する必要があります
- [Git](https://git-scm.com/downloads) をダウンロードします
- 認証に使用される Azure Active Directory (AAD) アプリの登録で、テナント全体の管理者の同意を得るためには、全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者の権限が必要です (その他のオプションについては、以下を参照してください)
- デプロイでサポートされているオペレーティング システムは、Windows、Linux、および Mac です
- IoT Edge は、Windows 10 IoT Enterprise LTSC および Ubuntu Linux 16.08/18.04 LTS Linux をサポートしています

## <a name="main-components"></a>主要コンポーネント

- 最小依存関係: IoT Hub、Cosmos DB、Service Bus、イベント ハブ、Key Vault、ストレージ
- 標準依存関係: 最小に加え、SignalR Service、AAD アプリの登録、Device Provisioning Service、Time Series Insights、ブック、Log Analytics、Application Insights
- マイクロサービス: App Service プラン、App Service
- UI (Web アプリ): App Service プラン (マイクロサービスと共有)、App Service
- シミュレーション: 仮想マシン、仮想ネットワーク、IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>インストールの種類

- 最小: 最小依存関係
- ローカル: 最小および標準依存関係
- サービス: ローカルおよびマイクロサービス
- シミュレーション: 最小依存関係およびシミュレーション コンポーネント
- アプリ: サービスおよび UI
- すべて (既定): アプリおよびシミュレーション

## <a name="deployment"></a>デプロイ

1. IIoT プラットフォームのデプロイを開始するには、コマンド プロンプトまたはターミナルからリポジトリを複製します。

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. ガイド付きデプロイを開始すると、スクリプトによって、Azure アカウント、サブスクリプション、対象のリソースおよびグループ、アプリケーション名などの必要な情報が収集されます。

Windows の場合:
    ```
    .\deploy
    ```

Linux または Mac の場合:
    ```
    ./deploy.sh
    ```

3. マイクロサービスと UI は、認証を必要とする Web アプリケーションです。そのためには、AAD に 3 つのアプリを登録する必要があります。 必要な権限がない場合は、次の 2 つの解決策があります。

- AAD 管理者に依頼して、アプリケーションに対してテナント全体の管理者の同意を付与します
- AAD 管理者は、AAD アプリケーションを作成できます。 deploy/scripts フォルダーには、デプロイとは別に AAD 登録を実行する aad- register.ps1 スクリプトが含まれています。 スクリプトの出力は、デプロイの一部として使用される関連情報を含むファイルであり、- aadConfig 引数を使用して、同じフォルダー内の deploy.ps1 スクリプトに渡す必要があります。
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

ステージング、ロールバック、スケーリング、回復性を必要とする運用環境のデプロイでは、プラットフォームを [Azure Kubernetes Service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md) にデプロイできます

参照:
- [Azure Industrial IoT Platform のデプロイ](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [オールインワンでデプロイする方法](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [AKS にプラットフォームをデプロイする方法](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>次のステップ
IIoT プラットフォームのデプロイが完了したので、コンポーネントの構成をカスタマイズする方法を学習できます。

> [!div class="nextstepaction"]
> [コンポーネントの構成をカスタマイズする](tutorial-configure-industrial-iot-components.md)
