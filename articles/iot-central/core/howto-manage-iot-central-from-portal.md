---
title: Azure portal で IoT Central を管理する | Microsoft Docs
description: この記事では、Azure portal から IoT Central アプリケーションを作成して管理する方法について説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 27517c375265b552d2e1dec4d8c167d1bc86549d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137685"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure portal で IoT Central を管理する

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で IoT Central アプリケーションを作成および管理するのではなく、[Azure portal](https://portal.azure.com) を使用してアプリケーションを管理できます。

## <a name="create-iot-central-applications"></a>IoT Central アプリケーションを作成する

アプリケーションを作成するには、[Azure portal](https://ms.portal.azure.com) に移動し、 **[リソースの作成]** を選択します。

**[Marketplace を検索]** バーで、「*IoT Central*」と入力します。

![管理ポータル: 検索](media/howto-manage-iot-central-from-portal/image0a1.png)

検索結果の **[IoT Central アプリケーション]** タイルを選択します。

![管理ポータル: 検索結果](media/howto-manage-iot-central-from-portal/image0b1.png)

ここで、 **[作成]** を選択します。

![管理ポータル: IoT Central リソース](media/howto-manage-iot-central-from-portal/image0c1.png)

フォームのすべてのフィールドに入力します。 このフォームは、[Azure IoT Central のアプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上でアプリケーションを作成するために入力するフォームに似ています。 詳細については、クイックスタートの「[Azure IoT Central アプリケーションの作成](quick-deploy-iot-central.md)」をご覧ください。

![IoT Central フォームを作成する](media/howto-manage-iot-central-from-portal/image6a.png)

"**場所**" は、アプリケーションを作成する[地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 現在、Azure IoT Central は、**米国**、**オーストラリア**、**アジア太平洋**、または**ヨーロッパ**で使用できます。  いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。


すべてのフィールドに値を入力したら、 **[作成]** を選択します。

## <a name="manage-existing-iot-central-applications"></a>既存の IoT Central アプリケーションを管理する

Azure IoT Central アプリケーションが既にある場合は、Azure portal でそれを削除したり、別のサブスクリプションまたはリソース グループに移動したりできます。

> [!NOTE]
> 無料価格プランで作成されたアプリケーションは、サブスクリプションに関連付けられていないため、Azure portal には表示されません。

開始するには、ポータルの **[すべてのリソース]** を選択します。 **[非表示の型の表示]** を選択し、 **[名前でフィルター処理]** にアプリケーションの名前の入力を開始して名前を見つけます。 次に、管理する IoT Central アプリケーションを選択します。

アプリケーションに移動するには、 **[IoT Central アプリケーションの URL]** を選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image3.png)

別のリソース グループにアプリケーションを移動するには、リソース グループの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるリソース グループを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image4a.png)

アプリケーションを別のサブスクリプションに移動するには、サブスクリプションの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるサブスクリプションを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>次のステップ

ここでは、Azure portal で Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)