---
title: Azure portal で IoT Central を管理して監視する | Microsoft Docs
description: この記事では、Azure portal から IoT Central アプリケーションを作成、管理、監視する方法について説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: 57486312b380fc18cfdb399343535e5e12245bff
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091603"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>Azure portal で IoT Central を管理して監視する

[Azure portal](https://portal.azure.com) を使用して、IoT Central アプリケーションを作成、管理、監視できます。

## <a name="create-iot-central-applications"></a>IoT Central アプリケーションを作成する

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

アプリケーションを作成するには、Azure portal の [[IoT Central アプリケーション]](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) ページに移動します。

![IoT Central フォームを作成する](media/howto-manage-iot-central-from-portal/create-form.png)

* **[リソース名]** は、Azure リソース グループ内の IoT Central アプリケーション用に選択できる一意の名前です。

* **[アプリケーション URL]** は、アプリケーションへのアクセスに使用できる URL です。

* **[テンプレート]** は、作成する IoT Central アプリケーションの種類です。 アプリケーションを作成するには、業界に関連するテンプレートの一覧を参照して、すばやく作業を開始するか、 **[カスタム アプリケーション]** テンプレートを使用してゼロから始めることができます。

* "**場所**" は、アプリケーションを作成する [地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択する必要があります。 Azure IoT Central は現在、次の場所でご利用いただけます。
    
    * オーストラリア
    * 米国中東部
    * 米国東部
    * 米国東部 2
    * 東日本
    * 北ヨーロッパ
    * 東南アジア
    * 英国南部
    * 西ヨーロッパ
    * 米国西部

  いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

すべてのフィールドに値を入力したら、 **[作成]** を選択します。 詳細については、「[IoT Central アプリケーションを作成する](howto-create-iot-central-application.md)」を参照してください。

## <a name="manage-existing-iot-central-applications"></a>既存の IoT Central アプリケーションを管理する

Azure IoT Central アプリケーションが既にある場合は、それを削除したり、Azure portal で別のサブスクリプションまたはリソース グループに移動したりできます。

> [!NOTE]
> "*無料*" プランを使用して作成されたアプリケーションは、Azure サブスクリプションを必要としないため、Azure portal の Azure サブスクリプション一覧にも表示されません。 無料のアプリは、IoT Central ポータルからのみ表示して管理することができます。

まず、Azure portal の上部にある検索バーでアプリケーションを検索します。 "_IoT Central アプリケーション_" で検索してサービスを選択することで、すべてのアプリケーションを表示することもできます。

!["IoT Central アプリケーション" の検索結果が表示され、最初のサービスが選択されているスクリーンショット。](media/howto-manage-iot-central-from-portal/search-iot-central.png)

検索結果でアプリケーションを選択すると、Azure portal にその概要が表示されます。 アプリケーションに移動するには、 **[IoT Central アプリケーションの URL]** を選択します。

![[IoT Central アプリケーション URL] が強調表示されている [概要] ページを示すスクリーンショット。](media/howto-manage-iot-central-from-portal/highlight-application.png)

別のリソース グループにアプリケーションを移動するには、リソース グループの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるリソース グループを選択します。

![[リソースグループ (変更)] が強調表示されている [概要] ページを示すスクリーンショット。](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

アプリケーションを別のサブスクリプションに移動するには、サブスクリプションの横にある **[変更]** を選択します。 **[リソースの移動]** ページで、このアプリケーションの移動先となるサブスクリプションを選択します。

![管理ポータル: リソースの管理](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="monitor-application-health"></a>アプリケーションの正常性を監視する

> [!NOTE]
> メトリックは、バージョン 3 の IoT Central アプリケーションでのみ使用できます。 アプリケーションのバージョンを確認する方法については、「[アプリケーションに関する情報はどのような方法で取得できますか?](howto-faq.yml#how-do-i-get-information-about-my-application-)」を参照してください。

IoT Central で提供される一連のメトリックを使用して、IoT Central アプリケーションに接続されているデバイスの正常性、および実行中のデータ エクスポートの正常性を評価することができます。

メトリックは、IoT Central アプリケーションでは既定で有効になっており、[Azure portal](https://portal.azure.com/) からアクセスします。 [Azure Monitor データ プラットフォームでは、これらのメトリックが公開](../../azure-monitor/essentials/data-platform-metrics.md)されており、これらを対話方式で操作する複数の方法が用意されています。 たとえば、Azure portal、REST API、または PowerShell や Azure CLI のクエリでグラフを使用できます。

> [!TIP]
> 無料試用版プランを使用するアプリケーションには、関連付けられた Azure サブスクリプションがないため、Azure Monitor メトリックはサポートされません。 [アプリケーションを標準の料金プランに変換](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)して、これらのメトリックにアクセスすることができます。

### <a name="view-metrics-in-the-azure-portal"></a>Azure portal でメトリックを表示する

次の手順では、[IoT Central アプリケーション](./howto-create-iot-central-application.md)に、[接続されているデバイス](./tutorial-connect-device.md)または実行中の[データ エクスポート](howto-export-data.md)があることを前提としています。

ポータルで IoT Central メトリックを表示するには:

1. ポータルで IoT Central アプリケーション リソースに移動します。 既定では、IoT Central リソースは **IOTC** という名前のリソース グループに配置されます。
1. アプリケーションのメトリックからグラフを作成するには、 **[監視]** セクションで **[メトリック]** を選択します。

![Azure メトリック](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Azure portal アクセス許可

Azure portal のメトリックへのアクセスは [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)によって管理されます。 Azure portal を使用して、IoT Central アプリケーション/リソース グループ/サブスクリプションにユーザーを追加し、アクセス権を付与します。 ユーザーは、既に IoT Central アプリケーションに追加されている場合でも、ポータルに追加する必要があります。 [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を使用して、きめ細かなアクセス制御を行うことができます。

### <a name="iot-central-metrics"></a>IoT Central メトリック

IoT Central で現在使用できるメトリックの一覧については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)」を参照してください。

### <a name="metrics-and-invoices"></a>メトリックと請求書

メトリックは、Azure IoT Central の請求書に表示される数値とは異なる場合があります。 こうした状況は、次のようなさまざまな原因によって発生します。

* IoT Central の[標準料金プラン](https://azure.microsoft.com/pricing/details/iot-central/)には、2 つのデバイスとさまざまなメッセージ クォータが無料で含まれています。 無料の項目は課金から除外されますが、メトリックには引き続きカウントされます。

* IoT Central は、アプリケーション内のデバイス テンプレートごとに 1 つのテスト デバイス ID を自動生成します。 このデバイス ID は、デバイス テンプレートの **[テスト デバイスの管理]** ページに表示されます。 これらのテスト デバイス ID を使用するコードを生成することによって、公開する前にデバイス テンプレートを検証することができます。 これらのデバイスは課金から除外されますが、メトリックには引き続きカウントされます。

* メトリックはデバイスからクラウドへの通信のサブセットを示す場合がありますが、デバイスとクラウド間のすべての通信は、[課金のメッセージとしてカウントされます](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="monitor-connected-iot-edge-devices"></a>接続されている IoT Edge デバイスを監視する

Azure Monitor と組み込みのメトリック統合を使用して、お使いの IoT Edge フリートをリモートで監視する方法については、「[メトリックの収集と転送](../../iot-edge/how-to-collect-and-transport-metrics.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ここでは、Azure portal で Azure IoT Central アプリケーションを管理および監視する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)