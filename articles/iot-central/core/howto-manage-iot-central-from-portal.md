---
title: Azure portal で IoT Central を管理する | Microsoft Docs
description: この記事では、Azure portal から IoT Central アプリケーションを作成して管理する方法について説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: 3e5e126815d0171a6c1627a08419b05b9a3c0c23
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719207"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Azure portal で IoT Central を管理する

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure portal](https://portal.azure.com) を使用して、IoT Central アプリケーションを作成および管理できます。

## <a name="create-iot-central-applications"></a>IoT Central アプリケーションを作成する

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

アプリケーションを作成するには、Azure portal の [[IoT Central アプリケーション]](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) ページに移動します。

![IoT Central フォームを作成する](media/howto-manage-iot-central-from-portal/image6a.png)

* **[リソース名]** は、Azure リソース グループ内の IoT Central アプリケーション用に選択できる一意の名前です。

* **[アプリケーション URL]** は、アプリケーションへのアクセスに使用できる URL です。

* "**場所**" は、アプリケーションを作成する [地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 Azure IoT Central は現在、次の場所でご利用いただけます。

  * アジア太平洋
  * オーストラリア
  * ヨーロッパ
  * 日本
  * イギリス
  * United States

  いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

すべてのフィールドに値を入力したら、 **[作成]** を選択します。 詳細については、クイックスタートの [IoT Central アプリケーションの作成](quick-deploy-iot-central.md)に関する記事を参照してください。

## <a name="manage-existing-iot-central-applications"></a>既存の IoT Central アプリケーションを管理する

Azure IoT Central アプリケーションが既にある場合は、それを削除したり、Azure portal で別のサブスクリプションまたはリソース グループに移動したりできます。

> [!NOTE]
> "*無料*" プランを使用して作成されたアプリケーションは、Azure サブスクリプションを必要としないため、Azure portal の Azure サブスクリプション一覧にも表示されません。 無料のアプリは、IoT Central ポータルからのみ表示して管理することができます。

まず、Azure portal の上部にある検索バーでアプリケーションを検索します。 "_IoT Central アプリケーション_" で検索してサービスを選択することで、すべてのアプリケーションを表示することもできます。

!["IoT Central アプリケーション" の検索結果が表示され、最初のサービスが選択されているスクリーンショット。](media/howto-manage-iot-central-from-portal/search-iot-central.png)

検索結果でアプリケーションを選択すると、Azure portal にその概要が表示されます。 アプリケーションに移動するには、 **[IoT Central アプリケーションの URL]** を選択します。

![[IoT Central アプリケーション URL] が強調表示されている [概要] ページを示すスクリーンショット。](media/howto-manage-iot-central-from-portal/image3.png)

別のリソース グループにアプリケーションを移動するには、リソース グループの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるリソース グループを選択します。

![[リソースグループ (変更)] が強調表示されている [概要] ページを示すスクリーンショット。](media/howto-manage-iot-central-from-portal/image4a.png)

アプリケーションを別のサブスクリプションに移動するには、サブスクリプションの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるサブスクリプションを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>次のステップ

ここでは、Azure portal で Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)