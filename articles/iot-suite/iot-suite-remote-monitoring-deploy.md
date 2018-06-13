---
title: リモート監視ソリューションのデプロイ - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータを azureiotsuite.com からプロビジョニングする方法について説明します。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
ms.locfileid: "33773634"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータをデプロイする

このチュートリアルでは、リモート監視ソリューション アクセラレータをプロビジョニングする方法について説明します。 azureiotsuite.com からソリューションをデプロイするとします。CLI を使用してソリューションをデプロイすることもできます。このオプションの詳細については、[コマンドラインからのソリューション アクセラレータのデプロイ](iot-suite-remote-monitoring-deploy-cli.md)に関するページをご覧ください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="deploy-the-solution-accelerator"></a>ソリューション アクセラレータのデプロイ

Azure サブスクリプションにソリューション アクセラレータをデプロイする前に、構成オプションをいくつか選択する必要があります。

1. Azure アカウントの資格情報を使用して、[azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) にログオンします。

1. **[リモート監視]** タイルで **[Try Now]** (今すぐ試す) を選択します。

    ![リモート監視の選択](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. **[Create Remote Monitoring solution] (リモート監視ソリューションの作成)** ページでリモート監視のソリューション アクセラレータの **[ソリューション名]** を入力します。

1. **[Basic]** または **[Standard]** デプロイを選択します。 しくみについて学ぶ、またはデモを実行するためにソリューションをデプロイする場合、**[Basic]** を選択すると費用を最小限に抑えられます。

1. 言語として、**[Java]** または **[.NET]** のいずれかを選択します。 すべてのマイクロサービスは Java と .NET のいずれの実装としても利用できます。

1. 選択した構成の詳細については、**[ソリューションの詳細]** パネルを確認します。

1. ソリューションのプロビジョニングに使用する**サブスクリプション**と**リージョン**を選択します。

1. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

    ![リモート監視ソリューションの詳細](media/iot-suite-remote-monitoring-deploy/createform.png)

トラブルシューティングの情報については、GitHub リポジトリの「[What to do when a deployment fails (デプロイが失敗した場合の対処方法)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)」をご覧ください。

## <a name="sign-in-to-the-solution-accelerator"></a>ソリューション アクセラレータへのサインイン

プロビジョニング プロセスが完了すると、リモート監視ソリューション アクセラレータにサインインできます。

1. **[プロビジョニングされたソリューション]** ページで、新しいリモート監視ソリューションを選択します。

    ![新しいソリューションの選択](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. 表示されるパネルで、リモート監視ソリューションに関する情報を確認できます。 **[ソリューション ダッシュボード]** を選択してリモート監視ソリューションに接続します。

    > [!NOTE]
    > 使用が済んだら、このパネルからリモート監視ソリューションを削除できます。

    ![ソリューション パネル](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. お使いのブラウザーで、リモート監視ソリューションのダッシュボードが表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ソリューション アクセラレータの構成
> * ソリューション アクセラレータのデプロイ
> * ソリューション アクセラレータへのサインイン

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](./iot-suite-remote-monitoring-explore.md)することです。

<!-- Next tutorials in the sequence -->