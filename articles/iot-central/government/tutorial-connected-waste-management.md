---
title: チュートリアル - Azure IoT コネクテッド廃棄物管理 | Microsoft Docs
description: このチュートリアルでは、IoT Central のコネクテッド廃棄物管理アプリケーション テンプレートをデプロイして使用する方法について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1ed898b02f2c30c3dcb043903bd3c3261d088539
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183288"
---
# <a name="tutorial-deploy-and-walk-through-the-connected-waste-management-application-template"></a>チュートリアル: コネクテッド廃棄物管理アプリケーション テンプレートをデプロイして調べる

IoT Central の "*コネクテッド廃棄物管理*" アプリケーション テンプレートとこの記事のガイダンスを使用して、エンドツーエンドのコネクテッド廃棄物管理ソリューションを開発します。

:::image type="content" source="media/tutorial-connectedwastemanagement/concepts-connected-waste-management-architecture-1.png" alt-text="コネクテッド廃棄物管理のアーキテクチャ。":::

### <a name="devices-and-connectivity"></a>デバイスと接続

オープンな環境で使用されるごみ箱などのデバイスは、省電力広域ネットワーク (LPWAN) またはサードパーティのネットワーク オペレーターを介して接続できます。 これらの種類のデバイスでは、[Azure IoT Central デバイス ブリッジ](../core/howto-build-iotc-device-bridge.md)を使用して、Azure IoT Central の IoT アプリケーションに自分のデバイス データを送信します。 また、IP 対応で IoT Central に直接接続可能なデバイス ゲートウェイを使用することもできます。

### <a name="iot-central"></a>IoT Central

Azure IoT Central は、IoT ソリューションを迅速に構築してデプロイするのに役立つ IoT アプリケーション プラットフォームです。 サードパーティのサービスを使用して、ソリューションをブランド化、カスタマイズ、統合することができます。

スマート廃棄物デバイスを IoT Central に接続することで、デバイス コマンドと制御、監視とアラート、RBAC が組み込まれたユーザー インターフェイス、構成可能な分析情報ダッシュボード、拡張オプションを提供します。

### <a name="extensibility-and-integrations"></a>拡張性と統合

IoT Central で IoT アプリケーションを拡張し、必要に応じて次のことを行うことができます。

* IoT Central アプリケーションからの継続的なデータ エクスポートを使用して、機械学習モデルのトレーニングなどの高度な分析のために IoT データを変換して統合します。
* Power Automate または IoT Central アプリケーションの Webhook を使用してアクションをトリガーし、他のシステムのワークフローを自動化します。
* IoT Central API を使用して IoT Central で IoT アプリケーションにプログラムでアクセスします。

### <a name="business-applications"></a>ビジネス アプリケーション

IoT データを使用して、廃棄物ユーティリティ内のさまざまなビジネス アプリケーションを強化することができます。 たとえば、接続された廃棄物管理ソリューションで、ごみ回収車の配車を最適化できます。 最適化は、接続されたごみ箱からの IoT センサー データに基づいて行うことができます。[IoT Central に接続された廃棄物管理アプリケーション](./tutorial-connected-waste-management.md)で、ルールとアクションを構成し、[Connected Field Service](/dynamics365/field-service/connected-field-service) でアラートを作成するように、それらを設定できます。 IoT Central のルールで Power Automate を構成し、アプリケーションやサービス間のワークフローを自動化します。 また、Connected Field Service のサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。

IoT Central と Connected Field Service を使用して、次の統合プロセスを簡単に構成できます。

* Azure IoT Central では、Connected Field Service に、デバイスの異常に関する情報を診断のために送信することができます。
* Connected Field Service では、デバイスの異常によってトリガーされるケースまたは作業指示を作成することができます。
* Connected Field Service では、ダウンタイム インシデントを防止するために、技術者による検査をスケジュールできます。
* Azure IoT Central デバイス ダッシュボードは、関連するサービスおよびスケジュール情報で更新することができます。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]

> * Azure IoT Central の "*コネクテッド廃棄物管理*" テンプレートを使用してアプリを作成します。
> * ダッシュボードをいろいろ試してみてからカスタマイズします。 
> * コネクテッド廃棄物処理デバイス テンプレートを探索します。
> * シミュレートされたデバイスを探索します。
> * ルールを探索して構成します。
> * ジョブを構成します。
> * アプリケーションのブランドをカスタマイズします。

## <a name="prerequisites"></a>前提条件

* このアプリをデプロイするために必要な前提条件は特にありません。
* 無料価格プランを使用するか、Azure サブスクリプションを使用することができます。

## <a name="create-connected-waste-management-application"></a>コネクテッド廃棄物管理アプリを作成する

1. [Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[政府機関]** タブを選択します。:::image type="content" source="media/tutorial-connectedwastemanagement/iot-central-government-tab-overview.png" alt-text="コネクテッド廃棄物管理テンプレート":::

1. **[コネクテッド廃棄物管理]** で **[アプリの作成]** を選択します。

詳細については、「[IoT Central アプリケーションを作成する](../core/howto-create-iot-central-application.md)」を参照してください。

## <a name="walk-through-the-application"></a>アプリケーションを調べる

以降のセクションでは、アプリケーションの主な機能について見ていきます。

### <a name="dashboard"></a>ダッシュボード 

アプリケーションテンプレートをデプロイした後、既定のダッシュボードは **[Wide World 廃棄物管理ダッシュボード]** です。

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-1.png" alt-text="Wide World 廃棄物管理ダッシュボードのスクリーンショット。":::


ビルダーは、オペレーターのダッシュボードのビューを作成したりカスタマイズしたりすることができます。 まず、ダッシュボードを詳しく見ていきましょう。 

>[!NOTE]
>ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。 

ダッシュボードは、各種のタイルで構成されています。

* **Wide World Water Utility 画像タイル**: ダッシュボードの最初のタイルは、架空の公益水道事業 "Wide World Waste" の画像タイルです。 タイルをカスタマイズしたり、独自の画像を追加したり、削除したりできます。 

* **Waste bin (ごみ箱) 画像タイル**: 画像およびコンテンツ タイルを使用して、説明と共に、監視対象のデバイスの視覚的表現を作成できます。 

* **Fill level (充填レベル) KPI タイル**: このタイルには、ごみ箱の "*充填レベル*" センサーによってレポートされた値が表示されます。 ごみ箱の充填レベルとその他のセンサー ("*臭気メーター*"、"*重量*" など) は、リモートで監視できます。 オペレーターは、ごみ収集トラックの派遣などのアクションを実行できます。 

* **Waste monitoring area map (廃棄物監視エリア マップ)** : このタイルでは Azure Maps が使用されており、Azure IoT Central で直接構成できます。 マップ タイルにデバイスの[場所](../core/howto-use-location-data.md)が表示されます。 マップの上にポインターを置いて、マップ上でズームイン、ズームアウト、展開などのコントロールを試してみてください。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-map.png" alt-text="コネクテッド廃棄物管理ダッシュボードのマップのスクリーンショット。":::



* **Fill, odor, weight level (充填、臭気、重量レベル) 棒グラフ**: 1 つまたは複数の種類のデバイス テレメトリ データを棒グラフで視覚化できます。 棒グラフを展開することもできます。  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-bar-chart.png" alt-text="コネクテッド廃棄物管理テンプレート ダッシュボードの棒グラフのスクリーンショット。":::


* **Field Services (フィールド サービス)** : このダッシュボードには、Azure IoT Central アプリケーションから Dynamics 365 フィールド サービスと統合する方法へのリンクが含まれています。 たとえば、フィールド サービスを使用して、ごみ収集サービスを派遣するためのチケットを作成できます。 

### <a name="customize-the-dashboard"></a>ダッシュボードのカスタマイズ 

ダッシュボードは、 **[編集]** メニューを選択することによってカスタマイズできます。 新しいタイルを追加したり、既存のものを構成したりできます。 編集モードのダッシュボードは次のように表示されます。 

:::image type="content" source="media/tutorial-connectedwastemanagement/edit-dashboard.png" alt-text="コネクテッド廃棄物管理ダッシュボードの編集モードのスクリーンショット。":::


**[+ 新規]** を選択して新しいダッシュボードを作成し、最初から構成することもできます。 複数のダッシュボードを用意して、ダッシュボード メニューからダッシュボードを切り替えることができます。 

### <a name="explore-the-device-template"></a>デバイス テンプレートを探索する

Azure IoT Central のデバイス テンプレートでは、テレメトリ、プロパティ、コマンドなど、デバイスの機能を定義します。 ビルダーは、接続するデバイスの機能を表すデバイス テンプレートを定義できます。 

コネクテッド廃棄物管理アプリケーションには、コネクテッド廃棄物処理デバイス テンプレートのサンプルが付属しています。

デバイス テンプレートを表示するには:

1. Azure IoT Central で、アプリの左ペインから **[デバイス テンプレート]** を選択します。 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template.png" alt-text="アプリケーションのデバイス テンプレートを一覧表示する画面のスクリーンショット。":::


1. **[デバイス テンプレート]** リストで **[Connected Waste Bin]\(コネクテッド廃棄物処理\)** を選択します。

1. このデバイス テンプレートの機能を確認します。 **Fill level (充填レベル)** 、**Odor meter (臭気メーター)** 、**Weight (重量)** 、**Location (場所)** などのセンサーが定義されていることがわかります。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-connected-bin.png" alt-text="コネクテッド廃棄物処理デバイス テンプレートの詳細を表示する画面のスクリーンショット。":::


### <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

次のカスタマイズを行います。

1. デバイス テンプレート メニューから **[カスタマイズ]** を選択します。
1. **[Odor meter]\(臭気メーター\)** テレメトリ タイプを見つけます。
1. **[Odor meter]\(臭気メーター\)** の **[表示名]** を「**Odor level**」に更新します。
1. 測定単位を更新するか、**最小値** と **最大値** を設定してみましょう。
1. **[保存]** を選択します。 

### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する 

その方法は次のとおりです。

1. デバイス テンプレート メニューから **[クラウド プロパティ]** を選択します。
1. **[クラウド プロパティの追加]** を選択します。 Azure IoT Central では、デバイスには関連するものの、デバイスから送信されることは考えにくいようなプロパティを追加できます。 たとえば、設置区域、アセット情報、メンテナンス情報ごとのアラートしきい値がクラウド プロパティとして考えられます。 
1. **[保存]** を選択します。 
 
### <a name="views"></a>ビュー 

コネクテッド廃棄物処理デバイス テンプレートには、事前定義済みのビューが付属しています。 ビューを確認し、必要に応じて更新しましょう。 このビューには、オペレーターによるデバイス データの表示方法とクラウド プロパティの入力方法が定義されています。 

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-views.png" alt-text="コネクテッド廃棄物管理テンプレートのデバイス テンプレート ビューのスクリーンショット。":::


### <a name="publish"></a>発行 

変更を行ったデバイス テンプレートは、必ず発行してください。 

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成 

新しいデバイス テンプレートを作成するには、 **[+ 新規]** を選択して、手順に従います。 カスタム デバイス テンプレートを最初から作成することも、Azure デバイス カタログからデバイス テンプレートを選択することもできます。 

### <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する

Azure IoT Central では、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。 

コネクテッド廃棄物管理アプリケーションには、コネクテッド廃棄物処理デバイス テンプレートに関連付けられた 2 つのシミュレートされたデバイスがあります。 

### <a name="view-the-devices"></a>デバイスを表示する

1. Azure IoT Central の左ペインから **[デバイス]** を選択します。 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices.png" alt-text="コネクテッド廃棄物管理テンプレートの [デバイス] のスクリーンショット。":::


1. **[Connected Waste Bin]\(コネクテッド廃棄物処理\)** デバイスを選択します。  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices-bin-1.png" alt-text="コネクテッド廃棄物管理テンプレートの [Device Properties]\(デバイスのプロパティ\) のスクリーンショット。":::


1. **[Cloud Properties]\(クラウドのプロパティ\)** タブに移動します。 **[Bin full alert threshold]\(ごみ箱容量オーバー アラートしきい値\)** を **95** から **100** に更新します。 

**[Device Properties]\(デバイスのプロパティ\)** と **[Device Dashboard]\(デバイス ダッシュボード\)** のタブを調べます。 

> [!NOTE]
> すべてのタブはデバイス テンプレート ビューから構成されています。

### <a name="add-new-devices"></a>新しいデバイスを追加する

新しいデバイスは、 **[デバイス]** タブの **[+ 新規]** を選択することで追加できます。 

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、ルールを作成して、デバイスのテレメトリを自動的に監視し、1 つまたは複数の条件が満たされたときにアクションをトリガーできます。 アクションには、メール通知の送信や Power Automate アクションのトリガー、他のサービスにデータを送信する Webhook アクションの開始などがあります。

Connected waste management (接続された廃棄物管理) アプリケーションには、4 つのサンプル ルールがあります。

### <a name="view-rules"></a>ルールを表示する

1. Azure IoT Central の左ペインから **[Rules]\(ルール\)** を選択します。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-rules.png" alt-text="コネクテッド廃棄物管理テンプレートの [Rules]\(ルール\) のスクリーンショット。":::


1. **[Bin full alert]\(ごみ箱容量オーバーアラート\)** を選択します。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-bin-full-alert.png" alt-text="ごみ箱容量オーバーアラートのスクリーンショット。":::


 1. **[Bin full alert]\(ごみ箱容量オーバー アラート\)** では、**充填レベルがごみ箱容量オーバー アラートしきい値以上** であるかどうかの条件がチェックされます。

    **[Bin full alert threshold]\(ごみ箱容量オーバー アラートしきい値\)** は、コネクテッド廃棄物処理デバイス テンプレートに定義されているクラウド プロパティです。 

次に、メール アクションを作成しましょう。

### <a name="create-an-email-action"></a>メール アクションを作成する

メール アクションの構成は、ルールの **[Actions]\(アクション\)** リストで行うことができます。
1. **[+ Email]\(+ メール\)** を選択します。 
1. **[表示名]** に「**High pH alert**」と入力します。
1. 自分の Azure IoT Central アカウントに関連付けられているメール アドレスを **[送信先]** に入力します。 
1. 必要に応じて、メールのテキストに含めるメモを入力します。
1. **[Done]\(完了\)**  >  **[Save]\(保存\)** の順に選択します。 

今後、構成した条件が満たされるとメールが送信されます。

>[!NOTE]
>アプリケーションから、条件が満たされるたびにメールが送信されます。 自動ルールからのメールの受信を停止するには、ルールを無効にします。 
  
新しいルールを作成するには、 **[Rules]\(ルール\)** の左ペインから **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

Azure IoT Central では、ジョブを使用して、複数のデバイスで、デバイスまたはクラウドのプロパティの更新をトリガーできます。 また、ジョブを使用して、複数のデバイス上でデバイス コマンドをトリガーすることもできます。 Azure IoT Central によって、ワークフローが自動化されます。 

1. Azure IoT Central の左ペインから **[ジョブ]** を選択します。 
1. **[+ 新規]** を選択し、1 つまたは複数のジョブを構成します。 

## <a name="customize-your-application"></a>アプリケーションのカスタマイズ 

ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

### <a name="change-the-application-theme"></a>アプリケーションのテーマを変更する

その方法は次のとおりです。
1. **[管理]**  >  **[アプリケーションのカスタマイズ]** の順に移動します。
1. **[変更]** を選択して、**アプリケーション ロゴ** 用にアップロードする画像を選択します。
1. **[変更]** を選択して、**ブラウザー アイコン** (ブラウザーのタブに表示される画像) 用にアップロードする画像を選択します。
1. HTML 16 進数のカラー コードを追加して、既定のブラウザーの色を置き換えることもできます。 その場合は **[ヘッダー]** と **[アクセント]** のフィールドを使用します。

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-customize-your-application.png" alt-text="コネクテッド廃棄物管理テンプレートの [アプリケーションのカスタマイズ] のスクリーンショット。":::


1. アプリケーションの画像を変更することもできます。 **[管理]**  >  **[アプリケーションの設定]**  >  **[画像の選択]** の順に選択し、アプリケーションの画像としてアップロードする画像を選択します。
1. 最後に、アプリケーションのタイトルで **[設定]** を選択して、テーマを変更することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. 自分の Azure IoT Central アプリの左ペインから **[管理]** を選択します。
1. **[アプリケーションの設定]**  >  **[削除]** の順に選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [接続された水消費の概念](./tutorial-water-consumption-monitoring.md)
