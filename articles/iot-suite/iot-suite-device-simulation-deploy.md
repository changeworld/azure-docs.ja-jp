---
title: デバイス シミュレーション ソリューションのデプロイ - Azure | Microsoft Docs
description: このチュートリアルでは、azureiotsuite.com からデバイス シミュレーション ソリューションをプロビジョニングする方法について説明します。
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 52890c51b06777a2d74fcf143bee4e5e8919ba75
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Azure IoT デバイス シミュレーション ソリューションをデプロイする

このチュートリアルでは、デバイス シミュレーション ソリューションを プロビジョニングする方法について説明します。 azureiotsuite.com からソリューションをデプロイするとします。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * デバイス シミュレーション ソリューションの構成
> * デバイス シミュレーション ソリューションのデプロイ
> * デバイス シミュレーション ソリューションへのサインイン

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

お使いの Azure サブスクリプションにソリューションをデプロイする前に、構成オプションをいくつか選択する必要があります。

1. Azure アカウントの資格情報を使用して [azureiotsuite.com](https://www.azureiotsuite.com) にサインインし、**+** をクリックして新しいソリューションを作成します。

    ![新しいソリューションの作成](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. **[デバイスのシミュレーション]** タイルで **[選択]** をクリックします。

    ![デバイス シミュレーションの選択](media/iot-suite-device-simulation-deploy/select.png)

1. **[Create Device simulation solution (デバイス シミュレーション ソリューションの作成)]** ページで、デバイス シミュレーション ソリューションの**ソリューション名**を入力します。

1. ソリューションのプロビジョニングに使用する**サブスクリプション**と**リージョン**を選択します。

1. デバイス シミュレーション ソリューションで新しい IoT Hub をデプロイするかどうかを指定します。 このボックスをオンにすると、サブスクリプションに新しい IoT Hub がデプロイされます。 選択内容に関わらず、シミュレーションは常にどの IoT Hub にもポイントできます。

1. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

    ![デバイス シミュレーション ソリューションの詳細](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>ソリューションにサインインします。

プロビジョニング プロセスが完了すると、デバイス シミュレーション ソリューションにサインインできます。

1. **[プロビジョニングされたソリューション]** ページで、新しいデバイス シミュレーション ソリューションの **[起動]** をクリックします。

    ![新しいソリューションの選択](media/iot-suite-device-simulation-deploy/newsolution.png)

1. 表示されるパネルで、デバイス シミュレーション ソリューションに関する情報を確認できます。 **[ソリューション ダッシュボード]** を選択して、デバイス シミュレーション ソリューションに接続します。

    > [!NOTE]
    > 使用が済んだら、このパネルからデバイス シミュレーション ソリューションを削除できます。

    ![ソリューション パネル](media/iot-suite-device-simulation-deploy/properties.png)

1. お使いのブラウザーに、デバイス シミュレーション ソリューションのダッシュボードが表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * ソリューションの構成
> * ソリューションのデプロイ方法
> * ソリューションにサインインします。

これで、デバイス シミュレーション ソリューションのデプロイが完了しました。次の手順では、[デバイス シミュレーション ソリューション の機能を確認](./iot-suite-device-simulation-explore.md)します。

<!-- Next tutorials in the sequence -->
