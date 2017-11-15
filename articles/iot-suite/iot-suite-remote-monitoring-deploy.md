---
title: "リモート監視ソリューションのデプロイ - Azure | Microsoft Docs"
description: "このチュートリアルでは、リモート監視の事前構成済みソリューションを azureiotsuite.com からプロビジョニングする方法について説明します。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 159397ba4fc93628acbf2bb53edf5eb88fd0cac7
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>リモート監視の事前構成済みソリューションをデプロイする

このチュートリアルでは、リモート監視の事前構成済みソリューションをプロビジョニングする方法について説明します。 azureiotsuite.com からソリューションをデプロイするとします。CLI を使用してソリューションをデプロイすることもできます。このオプションの詳細については、[コマンドラインからの構成済みソリューションのデプロイ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line)に関するページをご覧ください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 構成済みソリューションの構成
> * 構成済みソリューションのデプロイ
> * 構成済みソリューションへのサインイン

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="deploy-the-preconfigured-solution"></a>構成済みソリューションのデプロイ

お使いの Azure サブスクリプションに構成済みのソリューションをデプロイする前に、一部の構成オプションを選択する必要があります。

1. Azure アカウントの資格情報を使用して [azureiotsuite.com](https://www.azureiotsuite.com) にログオンし、**[+]** をクリックして新しいソリューションを作成します。

    ![新しいソリューションの作成](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. **[Remote monitoring preview](リモート監視プレビュー)** タイルで **[選択]** をクリックします。

    ![リモート監視の選択](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. **[Create Remote Monitoring soultion]\(リモート監視ソリューションの作成\)** ページでリモート監視の事前構成済みソリューションの **[ソリューション名]** を入力します。

1. **[Basic]** または **[Standard]** デプロイを選択します。 しくみについて学ぶ、またはデモを実行するためにソリューションをデプロイする場合、**[Basic]** を選択すると費用を最小限に抑えられます。

1. 言語として、**[Java]** または **[.NET]** のいずれかを選択します。 すべてのマイクロサービスは Java と .NET のいずれの実装としても利用できます。

1. 選択した構成の詳細については、**[ソリューションの詳細]** パネルを確認します。

1. ソリューションのプロビジョニングに使用する**サブスクリプション**と**リージョン**を選択します。

1. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

    ![リモート監視ソリューションの詳細](media/iot-suite-remote-monitoring-deploy/createform.png)

トラブルシューティングの情報については、GitHub リポジトリの「[What to do when a deployment fails (デプロイが失敗した場合の対処方法)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)」をご覧ください。

## <a name="sign-in-to-the-preconfigured-solution"></a>構成済みソリューションへのサインイン

プロビジョニング プロセスが完了すると、リモート監視の構成済みソリューションにサインインできます。

1. **[プロビジョニングされたソリューション]** ページで、新しいリモート監視ソリューションを選択します。

    ![新しいソリューションの選択](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. 表示されるパネルで、リモート監視ソリューションに関する情報を確認できます。 **[ソリューション ダッシュボード]** を選択してリモート監視ソリューションに接続します。

    > [!NOTE]
    > 使用が済んだら、このパネルからリモート監視ソリューションを削除できます。

    ![ソリューション パネル](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. お使いのブラウザーで、リモート監視ソリューションのダッシュボードが表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 構成済みソリューションの構成
> * 構成済みソリューションのデプロイ
> * 構成済みソリューションへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](./iot-suite-remote-monitoring-explore.md)することです。

<!-- Next tutorials in the sequence -->