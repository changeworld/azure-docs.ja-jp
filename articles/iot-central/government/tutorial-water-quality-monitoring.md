---
title: チュートリアル - Azure IoT 水質の監視 | Microsoft Docs
description: このチュートリアルでは、IoT Central 用の水質の監視アプリケーションのアプリケーション テンプレートを展開して使用する方法について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 481a085b1fd5fec55cd34f885dfcda40fec6f5e7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179139"
---
# <a name="tutorial-deploy-and-walk-through-the-water-quality-monitoring-application"></a>チュートリアル: 水質の監視アプリケーションをデプロイして調べる

IoT Central の "*水質の監視*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドの水質監視ソリューションを開発します。


![水質監視アーキテクチャ](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### <a name="devices-and-connectivity"></a>デバイスと接続

水管理ソリューションでは、流量計、水質モニター、スマート バルブ、漏れ検知器などのスマートウォーター デバイスを使用します。

スマート ウォーター ソリューションのデバイスは、省電力広域ネットワーク (LPWAN) またはサードパーティのネットワーク オペレーターを介して接続できます。 これらの種類のデバイスでは、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションに自分のデバイス データを送信します。 または、IP 対応で IoT Central に直接接続可能なデバイス ゲートウェイを使用することもできます。

### <a name="iot-central"></a>IoT Central

Azure IoT Central は、IoT ソリューションを迅速に構築してデプロイするのに役立つ IoT アプリケーション プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。

スマート ウォーター デバイスを IoT Central に接続すると、アプリケーションによって、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションが提供されます。

### <a name="extensibility-and-integrations"></a>拡張性と統合

IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。

* 高度な分析 (たとえば、IoT Central アプリケーションからの継続的なデータ エクスポートを使用した機械学習モデルのトレーニングなど) のために IoT データを変換して統合します。
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化します。
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスします。

### <a name="business-applications"></a>ビジネス アプリケーション

IoT データを使用して、水道施設内でさまざまなビジネス アプリケーションを強化することができます。 [IoT Central 水消費量のモニタリング アプリケーション](tutorial-water-consumption-monitoring.md)では、ルールやアクションを設定し、[Connected Field Service](/dynamics365/field-service/connected-field-service) でアラートを作成するように設定できます。 IoT Central のルールで Power Automate を構成し、アプリケーションやサービス間のワークフローを自動化します。 また、Connected Field Service のサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]

> * **水質のモニタリング** テンプレートを使用して、水質のモニタリング アプリケーションを作成します。
> * ダッシュボードを調べてカスタマイズします。
> * 水質のモニタリング デバイス テンプレートを探索します。
> * シミュレートされたデバイスを探索します。
> * ルールを探索して構成します。
> * ジョブを構成します。
> * ホワイト ラベルを使用してアプリケーション ブランドをカスタマイズします。

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な前提条件は特にありません。
* 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。

## <a name="create-water-quality-monitoring-application"></a>水質の監視アプリケーションを作成する

次の手順に従ってアプリケーションを作成します。

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[政府機関]** タブを選択します。:::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="アプリケーション テンプレート":::

1. **[水質の監視]** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード

アプリケーションを作成した後、 **[Wide World water quality dashboard]\(Wide World の水質ダッシュボード\)** ペインが開きます。

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="水質の監視ダッシュボード。":::

ビルダーは、オペレーターが使用するダッシュボードのビューを作成したりカスタマイズしたりすることができます。 カスタマイズしてみる前に、ダッシュボードを探索します。

ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。

ダッシュボードには、次の種類のタイルが含まれています。

* **[Wide World Water Utility] 画像タイル**:ダッシュボードの左上隅にある最初のタイルは、Wide World という名前の架空の公益事業を示す画像です。 独自の画像を使用するようにタイルをカスタマイズすることも、タイルを削除することもできます。

* **平均 pH KPI タイル**:**過去 30 分間の平均 pH** などの KPI タイルがダッシュボード ペインの上部に表示されます。 KPI タイルをカスタマイズし、別の種類や時間の範囲に設定できます。

* **[Water monitoring area map]\(水質モニタリング エリア マップ\)** : Azure IoT Central では Azure Maps が使用されています。これをアプリケーション内で直接設定して、デバイスの [場所](../core/howto-use-location-data.md)を表示できます。 また、アプリケーションからの位置情報をデバイスにマップし、Azure Maps を使用して、その情報をマップに表示することもできます。 マップの上にカーソルを移動し、コントロールを試してみてください。

* **平均 pH 分布のヒートマップ グラフ**:さまざまな視覚化グラフを選択して、アプリケーションに最適な方法でデバイス テレメトリを表示できます。

* **重要な品質指標の折れ線グラフ**:デバイス テレメトリを、ある時間範囲にわたる折れ線グラフとしてプロットして視覚化することができます。  

* **化学薬品濃度の棒グラフ**:デバイス テレメトリを棒グラフで視覚化できます。

* **センサー パラメーターのリセット タイル**: ダッシュボードには、オペレーターが監視ダッシュボードから直接開始できるアクションのタイルが含まれています。 このようなアクションの例として、デバイスのプロパティのリセットが挙げられます。

* **プロパティ一覧タイル**:ダッシュボードには、しきい値情報、デバイスの正常性情報、メンテナンス情報を表す複数のプロパティ タイルがあります。

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ

ビルダーは、オペレーターが使用するダッシュボードのビューをカスタマイズすることができます。

1. **[編集]** を選択して、 **[Wide World water quality dashboard]\(Wide World の水質ダッシュボード\)** ペインをカスタマイズします。 ダッシュボードは、 **[編集]** メニューでコマンドを選択することによってカスタマイズできます。 ダッシュボードが編集モードになったら、新しいタイルを追加したり既存のファイルを構成したりできます。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="ダッシュボードを編集する。":::

1. **[+ 新規]** を選択して、構成可能な新しいダッシュボードを作成します。 複数のダッシュボードを作成し、ダッシュボード メニューからダッシュボード間を移動できます。

## <a name="explore-a-water-quality-monitoring-device-template"></a>水質のモニタリング デバイス テンプレートを探索します

Azure IoT Central のデバイス テンプレートには、デバイスの機能が定義されています。 指定可能な機能は、テレメトリ、プロパティ、およびコマンドです。 ビルダーは、接続されたデバイスの機能を表す Azure IoT Central のデバイス テンプレートを定義できます。 また、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。

作成した水質のモニタリング アプリケーションには、水質のモニタリング デバイス テンプレートが付属しています。

デバイス テンプレートを表示するには:

1. Azure IoT Central でアプリケーションの一番左のペインにある **[Device templates]\(デバイス テンプレート\)** を選択します。
1. デバイス テンプレートの一覧から **[Water Quality Monitor]\(水質のモニタリング\)** を選択して、そのデバイス テンプレートを開きます。

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="デバイス テンプレート。":::

### <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

次のデバイス テンプレート設定のカスタマイズを練習します。

1. デバイス テンプレート メニューから **[カスタマイズ]** を選択します。
1. テレメトリの種類 **[Temperature]\(温度\)** に移動します。
1. **[Display name]\(表示名\)** の値を **[Reported temperature]\(報告された温度\)** に変更します。
1. 測定単位を変更するか、**最小値** と **最大値** を設定します
1. **[保存]** を選択します。

#### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する

1. デバイス テンプレート メニューから **[クラウド プロパティ]** を選択します。
1. 新しいクラウド プロパティを追加するには、 **[+ クラウド プロパティの追加]** を選択します。 Azure IoT Central では、デバイスに関連するが、デバイスから送信されることを想定しないプロパティを追加できます。 そのようなプロパティの 1 つの例として、設置区域、アセット情報、またはメンテナンス情報に固有のアラートのしきい値があります。
1. **[表示名]** として「**Installation area**」を入力し、 **[スキーマ]** として **[文字列]** を選択します。
1. **[保存]** を選択します。

### <a name="explore-views"></a>ビューを探索する

水質モニタリング デバイス テンプレートには、定義済みのビューが用意されています。 このビューには、オペレーターにデバイス データがどのように表示されるか、およびオペレーターによってクラウドのプロパティがどのように設定されるかが定義されています。 ビューを探索し、変更を行ってみます。

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="デバイス テンプレートのビュー。":::

### <a name="publish-the-device-template"></a>デバイス テンプレートを公開する

何らかの変更を行った場合は、必ず **[Publish]\(公開\)** を選択してデバイス テンプレートを公開してください。

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成

1. 新しいデバイス テンプレートを作成するには、 **[デバイス テンプレート]** ページで **[+ 新規]** を選択し、作成プロセスに従います。
1. カスタムのデバイス テンプレートを作成するか、Azure IoT デバイス カタログからデバイス テンプレートを選択します。

## <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する

アプリケーション テンプレートから作成した水質のモニタリング アプリケーションには、2 つのシミュレートされたデバイスがあります。 これらのデバイスは、水質のモニタリング デバイス テンプレートにマップされます。

### <a name="view-the-devices"></a>デバイスを表示する

1. アプリケーションの一番左のペインで **[Devices]\(デバイス\)** を選択します。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="デバイス":::

1. シミュレートされたデバイスを 1 つ選択します。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="デバイス 1 を選択する":::

1. **[クラウドのプロパティ]** タブで、 **[Acidity (pH) threshold]\(酸性度 (pH) のしきい値\)** の値を **8** から **9** に変更し、 **[保存]** を選択します。
1. **[デバイスのプロパティ]** タブと **[Device Dashboard]\(デバイス ダッシュボード\)** タブを調べます。

> [!NOTE]
> すべてのタブが **デバイス テンプレート ビュー** から構成されています。

### <a name="add-new-devices"></a>新しいデバイスを追加する

1. **[デバイス]** タブで、 **[+ 新規]** を選択して新しいデバイスを追加します。
1. 推奨される **デバイス ID** を使用するか、独自のデバイス ID を入力します。 新しいデバイスの **デバイス名** を入力することもできます。
1. **[デバイス テンプレート]** として **[Water Quality Monitor]\(水質のモニタリング\)** を選択します。 
1. シミュレートされたデバイスを作成したい場合は、 **[このデバイスをシミュレートしますか?]** が **[はい]** に設定されていることを確認します。 
1. **［作成］** を選択します  

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、デバイス テレメトリを自動的に監視するルールを作成できます。 これらのルールは、いずれかの条件が満たされたときにアクションをトリガーします。 実行できる 1 つのアクションとして、電子メール通知の送信があります。 それ以外には、Power Automate アクションや、他のサービスにデータを送信する Webhook アクションなどが可能です。

作成した水質のモニタリング アプリケーションには、2 つの事前構成済みのルールがあります。

### <a name="view-rules"></a>ルールを表示する

1. アプリケーションの一番左のペインで **[ルール]** を選択します。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="ルール":::

1. **[High pH alert]\(高 pH アラート\)** を選択します。これは、アプリケーションに事前構成されたルールの 1 つです。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="高 pH アラート ルール。":::

   **[High pH alert]\(高 pH アラート\)** ルールは、酸性度 (pH) の条件が 8 を超えていることを確認するために構成されています。

次に、ルールにメール アクションを追加します。

1. **[+ Email]\(+ メール\)** を選択します。
1. **[表示名]** ボックスに、「**High pH alert**」と入力します。
1. **[宛先]** ボックスに、Azure IoT Central アカウントに関連付けられているメール アドレスを入力します。
1. 必要に応じて、メールのテキストに含めるメモを入力します。
1. **[完了]** を選択して、アクションを完了します。
1. ルールを **[有効]** に設定し、 **[保存]** を選択します。

構成された条件が満たされると、数分以内にメールを受信するはずです。

> [!NOTE]
> アプリケーションから、条件が満たされるたびにメールが送信されます。 ルールからの自動電子メールの受信を停止する場合は、そのルールの **[Disable]\(無効にする\)** を選択します。
  
新しいルールを作成するには、アプリケーションの一番左のペインで **[ルール]** を選択し、 **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

Azure IoT Central のジョブを使用すると、デバイスに対する、または複数のデバイスのクラウド プロパティに対する更新をトリガーできます。 また、ジョブを使用して、複数のデバイス上でデバイス コマンドをトリガーすることもできます。 Azure IoT Central によって、ワークフローが自動化されます。

1. アプリケーションの一番左のペインで **[ジョブ]** を選択します。
1. **[+ 新しいジョブ]** を選択し、1 つまたは複数のジョブを構成します。

## <a name="customize-your-application"></a>アプリケーションのカスタマイズ

ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

1. **[管理]**  >  **[アプリケーションのカスタマイズ]** を選択します。
1. **[タイトル ロゴ]** で **[変更]** を選択し、ロゴとしてアップロードする画像を選択します。
1. **[Browser icon]\(ブラウザー アイコン\)** で **[変更]** を選択し、ブラウザーのタブに表示される画像を選択します。
1. **[ブラウザーの色]** で、既定値を HTML 16 進数のカラー コードで置き換えることができます。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="アプリケーションのカスタマイズ":::

### <a name="update-the-application-image"></a>アプリケーション イメージを更新する

1. **[管理]**  >  **<自分のアプリケーション>** を選択します。

1. **[変更]** を選択して、アプリケーションの画像としてアップロードする画像を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. アプリケーションの一番左のペインで **[Administration]\(管理\)** タブを開きます。
1. **[お客様のアプリケーション]** を選択し、 **[削除]** ボタンを選択します。

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="アプリケーションを削除する。":::
