---
title: Azure portal で IoT Central を管理する | Microsoft Docs
description: Azure portal で IoT Central を管理します。
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963829"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure portal で IoT Central を管理する 
IoT Central Web サイトでの IoT Central アプリケーションの作成と管理に加え、Azure portal でも IoT Central を管理できます。 この記事では、実行できる操作とその実行方法について説明します。

## <a name="create-iot-central-applications"></a>IoT Central アプリケーションを作成する
新しいアプリケーションを作成するには、[Azure portal](https://ms.portal.azure.com) に移動し、左側のメイン ナビゲーション メニューの [リソースの作成] をクリックします。 

![管理ポータル: ナビゲーション メニュー](media/howto-manage-iot-central-from-portal/image0.png)

検索バーに、「IoT Central」と入力します。

![管理ポータル: 検索](media/howto-manage-iot-central-from-portal/image0a.png)

検索結果の [IoT Central アプリケーション] 行項目をクリックします。

![管理ポータル: 検索結果](media/howto-manage-iot-central-from-portal/image0b.png)

次に、[作成] ボタンをクリックして、入力する必要があるフォームを表示します。

![管理ポータル: IoT Central リソース](media/howto-manage-iot-central-from-portal/image0c.png)

フォームのすべてのフィールドに入力します。 このフォームは、IoT Central Web サイトからアプリケーションを作成するために入力する必要があるフォームに似ています。 各フィールドの入力方法の詳細については、「[Azure IoT Central アプリケーションの作成](quick-deploy-iot-central.md)」のクイック スタートを参照してください。 

![管理ポータル: IoT Central リソースの作成](media/howto-manage-iot-central-from-portal/image1.png)  

すべてのフィールドに値を入力したら、[作成] ボタンをクリックします。

## <a name="manage-existing-iot-central-applications"></a>既存の IoT Central アプリケーションを管理する
Azure IoT Central アプリケーションが既にある場合は、Azure portal でそれを削除したり、別のサブスクリプションまたはリソース グループに移動したりできます。 試用版アプリケーションをサポートするサブスクリプションがないため、Azure portal にそれらの試用版を表示することはできません。

開始するには、左側のメイン ナビゲーション メニューの [すべてのリソース] をクリックします。 検索ボックスにアプリケーションの名前を入力し、リソースの一覧でそれを見つけます。 次に、管理する IoT Central アプリケーションをクリックします。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image2.png)

アプリケーションに移動するには、IoT Central アプリケーションの URL をクリックします。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image3.png)

別のリソース グループにアプリケーションを移動するには、リソース グループの横にある **[変更]** リンクをクリックします。 表示されたダイアログ ボックスで、このアプリケーションの移動先となるリソース グループを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image4.png)

アプリケーションを別のサブスクリプションに移動するには、サブスクリプションの横にある **[変更]** リンクをクリックします。 表示されたダイアログ ボックスで、このアプリケーションの移動先となるサブスクリプションを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>次の手順

ここでは、Azure portal で Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)