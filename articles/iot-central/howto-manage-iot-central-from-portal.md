---
title: Azure portal で IoT Central を管理する | Microsoft Docs
description: Azure portal で IoT Central を管理します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 89109dec83342a8f4b5962778b1803eb36656e42
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352216"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure portal で IoT Central を管理する

IoT Central の [[Application Manager]\(アプリケーション マネージャー\)](https://aka.ms/iotcentral) ページから IoT Central アプリケーションを作成して管理する代わりに、[Azure portal](https://portal.azure.com) を使用してアプリケーションを管理できます。

## <a name="create-iot-central-applications"></a>IoT Central アプリケーションを作成する

アプリケーションを作成するには、[Azure portal](https://ms.portal.azure.com) に移動し、左側のメイン ナビゲーション メニューの **[リソースの作成]** をクリックします。

![管理ポータル: ナビゲーション メニュー](media/howto-manage-iot-central-from-portal/image0.png)

検索バーに、「**IoT Central**」と入力します。

![管理ポータル: 検索](media/howto-manage-iot-central-from-portal/image0a.png)

検索結果の **[IoT Central アプリケーション]** 行項目をクリックします。

![管理ポータル: 検索結果](media/howto-manage-iot-central-from-portal/image0b.png)

**[作成]** ボタンをクリックします。

![管理ポータル: IoT Central リソース](media/howto-manage-iot-central-from-portal/image0c.png)

フォームのすべてのフィールドに入力します。 このフォームは、IoT Central の [[Application Manager]\(アプリケーション マネージャー\)](https://aka.ms/iotcentral) ページでアプリケーションを作成するために入力するフォームに似ています。 詳細については、クイックスタートの「[Azure IoT Central アプリケーションの作成](quick-deploy-iot-central.md)」をご覧ください。

![管理ポータル: IoT Central リソースの作成](media/howto-manage-iot-central-from-portal/image1.png)  

すべてのフィールドに値を入力したら、**[作成]** ボタンをクリックします。

## <a name="manage-existing-iot-central-applications"></a>既存の IoT Central アプリケーションを管理する

Azure IoT Central アプリケーションが既にある場合は、Azure portal でそれを削除したり、別のサブスクリプションまたはリソース グループに移動したりできます。

> [!NOTE]
> 試用版アプリケーションは、サブスクリプションに関連付けられていないため、Azure portal には表示されません。

開始するには、左側のメイン ナビゲーション メニューの **[すべてのリソース]** をクリックします。 検索ボックスにアプリケーションの名前を入力し、リソースの一覧でそれを見つけます。 次に、管理する IoT Central アプリケーションをクリックします。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image2.png)

アプリケーションに移動するには、IoT Central アプリケーションの URL をクリックします。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image3.png)

別のリソース グループにアプリケーションを移動するには、リソース グループの横にある **[変更]** をクリックします。 **[リソースの移動]** ページで、このアプリケーションの移動先となるリソース グループを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image4.png)

アプリケーションを別のサブスクリプションに移動するには、サブスクリプションの横にある **[変更]** リンクをクリックします。 表示されたダイアログ ボックスで、このアプリケーションの移動先となるサブスクリプションを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>次の手順

ここでは、Azure portal で Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)