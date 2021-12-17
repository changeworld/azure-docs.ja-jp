---
title: 'ツアー: Azure IoT Central の UI | Microsoft Docs'
description: IoT ソリューションの作成、管理、および使用に使用する Azure IoT Central の UI の主要な領域を把握しておきましょう。
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 187db7eefb019584835bd7f67fdb8d6abd91f45c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083906"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>ツアー: Azure IoT Central の UI

この記事では、Azure IoT Central の UI について取り上げます。 この UI を使用して、IoT Central アプリケーションとその接続デバイスを作成、管理、使用することができます。

## <a name="iot-central-homepage"></a>IoT Central ホームページ

[IoT Central ホームページ](https://apps.azureiotcentral.com/)のページでは、IoT Central で利用できる最新のニュースと機能の詳細を確認したり、新しいアプリケーションを作成したり、既存のアプリケーションを表示および起動したりできます。

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central ホームページ":::

### <a name="create-an-application"></a>アプリケーションの作成

**[ビルド]** セクションでは、業界に関連する IoT Central テンプレートの一覧を参照したり、カスタム アプリケーション テンプレートを使用してゼロから始めたりできます。  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="IoT Central の [ビルド] ページ":::

詳細については、クイックスタートの「[Azure IoT Central アプリケーションの作成](quick-deploy-iot-central.md)」を参照してください。

### <a name="launch-your-application"></a>アプリケーションの起動

IoT Central アプリケーションを起動するには、アプリの作成時に選択した URL に移動します。 [IoT Central アプリ マネージャー](https://apps.azureiotcentral.com/myapps)で、アクセス権を持つすべてのアプリケーションの一覧を表示することもできます。

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="IoT Central アプリ マネージャー":::

## <a name="navigate-your-application"></a>アプリケーションのナビゲート

IoT アプリケーション内では、左側のペインを使用してさまざまな機能にアクセスします。 左ペインの上部にある 3 本線のアイコンを選択すると、左ペインを展開または折りたたむことができます。

> [!NOTE]
> 左ペインに表示される項目は、ユーザー ロールによって異なります。 詳細については、[ユーザーとロールの管理](howto-manage-users-roles.md)に関するページを参照してください。 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="左ウィンドウ":::

  :::column-end:::
  :::column span="2":::
     **[ダッシュボード]** には、すべてのアプリケーション ダッシュボードと個人用ダッシュボードが表示されます。 
     
     **[デバイス]** では、すべてのデバイスを管理できます。

     **[デバイス グループ]** では、クエリによって指定されたデバイスのコレクションを表示および作成できます。 デバイス グループは、アプリケーションを通じて一括操作を実行するために使用されます。

     **[ルール]** では、デバイスを監視するルールを作成および編集できます。 ルールは、デバイスのデータに基づいて評価され、カスタマイズ可能なアクションをトリガーします。

     **[分析]** では、履歴の傾向を分析し、デバイスからのさまざまなテレメトリを関連付けるための豊富な分析機能を利用できます。

     **[ジョブ]** では、一括操作を実行してデバイスを大規模に管理できます。

     **[デバイス テンプレート]** では、アプリケーションに接続するデバイスの特性を作成および管理できます。

     **[データのエクスポート]** では、ストレージやキューなどの外部サービスへの連続エクスポートを構成できます。

     **[管理]** では、アプリケーションの設定、カスタマイズ、課金、ユーザー、およびロールを管理できます。

     **[マイ アプリ]** を使用すると、IoT Central アプリ マネージャーに戻ることができます。
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>検索、ヘルプ、テーマ、およびサポート

すべてのページには、次のトップ メニューが表示されます。

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central のツール バー":::

* デバイスを検索するには、 **[検索]** に値を入力します。
* UI の言語またはテーマを変更するには、 **[設定]** アイコンを選択します。 詳細については、「[アプリケーションのユーザー設定を管理する](howto-manage-preferences.md)」を参照してください。
* ヘルプやサポートを利用するには、 **[ヘルプ]** ドロップダウンを選択するとリソースが一覧表示されます。 **[About your app]\(アプリのバージョン情報\)** リンクから [アプリに関する情報を取得する](howto-faq.yml#how-do-i-get-information-about-my-application-)ことができます。
* アプリケーションからサインアウトするには、 **[アカウント]** アイコンを選択します。

UI 用に淡色テーマまたは濃色テーマを選ぶことができます。

> [!NOTE]
> 管理者によってアプリケーションのカスタム テーマが構成されている場合、淡色と濃色のテーマから選択するオプションは使用できません。

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="IoT Central の [テーマの選択] のスクリーンショット。":::

### <a name="dashboard"></a>ダッシュボード

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="IoT Central ダッシュボードのスクリーンショット。":::

* **[ダッシュボード]** は、IoT Central アプリケーションにサインインしたときに最初に表示されるページです。 複数のアプリケーション ダッシュボードを作成してカスタマイズすることができます。 詳細については、[ダッシュボードへのタイルの追加](howto-manage-dashboards.md)に関するページを参照してください。

* 個人用ダッシュボードを作成して、関心のある項目を監視することもできます。 詳細については、ハウツー記事にある [Azure IoT Central の個人用ダッシュボードの作成](howto-manage-dashboards.md)に関するページを参照してください。

### <a name="devices"></a>デバイス

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="[デバイス] ページのスクリーンショット。":::

このページには、お使いの IoT Central アプリケーションのデバイスが "_デバイス テンプレート_" のグループごとに表示されます。

* アプリケーションに接続できるデバイスの種類は、デバイス テンプレートによって定義されます。
* デバイスとは、対象アプリケーションにおける実デバイスまたはシミュレートされたデバイスを表します。

### <a name="device-groups"></a>デバイス グループ

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="[デバイス グループ] ページ":::

このページを使用すると、対象の IoT Central アプリケーションのデバイス グループを作成して表示できます。 デバイス グループを使用して、アプリケーションで一括操作を実行したり、データを分析したりできます。 詳細については、「[Azure IoT Central アプリケーションにおけるデバイス グループの使用](tutorial-use-device-groups.md)」を参照してください。

### <a name="rules"></a>ルール
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="[ルール] ページのスクリーンショット。":::

このページでは、デバイス データに基づいてルールを表示および作成できます。 ルールを適用すると、メールの送信や Webhook の呼び出しなど、1 つ以上のアクションをトリガーできます。 詳細については、チュートリアルの[ルールの構成](tutorial-create-telemetry-rules.md)に関するページを参照してください。

### <a name="analytics"></a>Analytics

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="[Analytics] ページのスクリーンショット。":::

[分析] では、履歴の傾向を分析し、デバイスからのさまざまなテレメトリを関連付けるための豊富な分析機能を利用できます。 詳細については、[Azure IoT Central アプリケーションに使用する分析の作成](howto-create-analytics.md)に関するページを参照してください。

### <a name="jobs"></a>ジョブ

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="[ジョブ] ページ":::

このページでは、デバイスに対する一括管理操作に使用できるジョブを表示および作成できます。 デバイスのプロパティと設定を更新し、デバイス グループに対してコマンドを実行できます。 詳細については、[ジョブの実行](howto-manage-devices-in-bulk.md)に関するページを参照してください。

### <a name="device-templates"></a>デバイス テンプレート

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="[デバイス テンプレート] のスクリーンショット。":::

[デバイス テンプレート] ページでは、アプリケーションのデバイス テンプレートを表示および作成できます。 詳細については、「[Define a new device type in your Azure IoT Central application (Azure IoT Central アプリケーションに新しいデバイスの種類を定義する)](howto-set-up-template.md)」のチュートリアルを参照してください。

### <a name="data-export"></a>データのエクスポート

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="データのエクスポート":::

[データのエクスポート] を使用すると、データのストリームを外部システムに設定できます。 詳しくは、「[Azure IoT Central でデータをエクスポートする](./howto-export-data.md)」をご覧ください。

### <a name="administration"></a>管理

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="IoT 管理のスクリーンショット。":::

[管理] ページでは、IoT Central アプリケーションの構成とカスタマイズを行うことができます。 ここでは、アプリケーション名、URL、テーマを変更したり、ユーザーとロールを管理したり、API トークンを作成したり、アプリケーションをエクスポートしたりできます。 詳細については、[Azure IoT Central アプリケーションの管理](howto-administer.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

これで Azure IoT Central の概要と UI のレイアウトに関する説明は終了です。推奨される次の手順として、「[Create an Azure IoT Central application (Azure IoT Central アプリケーションの作成)](quick-deploy-iot-central.md)」クイック スタートに進みましょう。
