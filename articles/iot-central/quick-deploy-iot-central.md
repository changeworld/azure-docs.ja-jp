---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 冷蔵自動販売機を管理するための新しい Azure IoT Central アプリケーションを作成します。 シミュレートされたデバイスから生成された利用統計情報を表示します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0199efbc1456206c67efb846b4381cae333e3749
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309044"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

"_ビルダー_" は、Azure IoT Central の UI を使用して、Microsoft Azure IoT Central アプリケーションを定義します。 このクイック スタートでは、サンプル "_デバイス テンプレート_" およびシミュレートされた "_デバイス_" を含んだ Azure IoT Central アプリケーションを作成する方法について説明します。

## <a name="create-the-application"></a>アプリケーションを作成する

このクイック スタートの作業を行うためには、**Sample Contoso** アプリケーション テンプレートから Azure IoT Central アプリケーションを作成する必要があります。

Azure IoT Central の [[Application Manager]\(アプリケーション マネージャー\)](https://aka.ms/iotcentral) ページに移動します。 次に、Azure サブスクリプションへのアクセスに使用するメール アドレスとパスワードを入力します。

![所属組織のアカウントを入力](media/quick-deploy-iot-central/sign-in.png)

新しい Azure IoT Central アプリケーションの作成を開始するには、**[New Application]\(新しいアプリケーション\)** を選択します。

![Azure IoT Central の [Application Manager]\(アプリケーション マネージャー\) ページ](media/quick-deploy-iot-central/iotcentralhome.png)

新しい Azure IoT Central アプリケーションを作成するには:

1. **[Free Trial Application]\(無料試用版アプリケーション\)** 支払プランを選択します。
1. わかりやすいアプリケーション名を選びます (**Contoso IoT** など)。 Azure IoT Central によって、一意の URL プレフィックスが自動的に生成されます。 この URL プレフィックスは、もっと覚えやすいものに変更することができます。
1. **[Sample Contoso]\(サンプル Contoso\)** アプリケーション テンプレートを選択します。
1. **[作成]** を選択します。

![Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、**Refrigerated Vending Machine** デバイス テンプレートとシミュレートされたデバイスを含んだ事前設定済みの Azure IoT Central アプリケーションを作成しました。 ビルダーとして独自のデバイス テンプレートを定義する方法について詳しくは、[アプリケーションに新しいデバイス テンプレートを定義する方法](tutorial-define-device-type.md)に関するページをご覧ください。
