---
title: Azure portal で IoT Central を管理して監視する | Microsoft Docs
description: この記事では、IoT Central アプリケーションを作成、管理、監視し、Azure portal からマネージド ID を有効にする方法について説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: dc9611eebdce1e988e760fda3b000cb52d0ff95a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488276"
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

## <a name="configure-a-managed-identity"></a>マネージド ID の構成

IoT Central アプリケーションでデータ エクスポートを構成する場合は、接続文字列またはマネージド ID を使用して、宛先への接続[を構成できます](../../active-directory/managed-identities-azure-resources/overview.md)。 セキュリティは、マネージド ID の方が優れています。その理由は次のとおりです。

* IoT Central アプリケーションの接続文字列にリソースの資格情報が格納されません。
* 資格情報は、IoT Central アプリケーションの有効期間に自動的に関連付けられます。
* マネージド ID では、セキュリティ キーの定期的なローテーションが自動的に行われます。

IoT Central は [、現在、システムによって割り当てられたマネージド id](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)を使用します。 アプリケーションのマネージド ID を作成するには、アプリケーションまたはアプリケーションのAzure portalを使用REST API。

> [!NOTE]
> マネージド ID は、リージョン内に作成IoT Centralアプリケーションにのみ追加できます。 すべての新しいアプリケーションがリージョンに作成されます。 詳細については、「更新プログラム」を [参照してください](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/)。

管理対象 id を構成すると、その構成には *スコープ* と *ロール* が含まれます。

* スコープでは、マネージド id を使用できる場所を定義します。 たとえば、Azure リソースグループをスコープとして使用できます。 この場合、IoT Central アプリケーションと宛先の両方が同じリソースグループに存在する必要があります。
* ロールは、IoT Central アプリケーションが宛先サービスで付与するアクセス許可を定義します。 たとえば、IoT Central アプリケーションでイベントハブにデータを送信するには、 **Azure Event Hubs データ送信者** のロールの割り当てが必要です。

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

ロールの割り当ては、次のAzure portal使用して構成Azure CLI。

* 特定の宛先に対して Azure portal でロールの割り当てを構成する方法の詳細については、「データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする」 [を参照してください](howto-export-data.md)。
* Azure CLI を使用してロールの割り当てを構成する方法の詳細については、「IoT Central または PowerShell からAzure CLI [を参照してください](howto-manage-iot-central-from-cli.md)。

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