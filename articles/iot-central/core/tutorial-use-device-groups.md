---
title: チュートリアル - Azure IoT Central アプリケーションでデバイス グループを使用する | Microsoft Docs
description: チュートリアル - デバイス グループを使用して Azure IoT Central アプリケーションでデバイスからのテレメトリを分析する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da7c1c0268f04b183ba48491bd5f0d0b01e15b41
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113224111"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>チュートリアル:デバイス グループを使用してデバイス テレメトリを分析する

この記事では、デバイス グループを使用して Azure IoT Central アプリケーションでデバイス テレメトリを分析する方法について説明します。

デバイス グループは、指定されたいくつかの条件に一致しているためにグループ化されたデバイスの一覧です。 デバイス グループは、デバイスをより小さい論理グループにグループ化することで、デバイスを大規模に管理、視覚化、および分析するのに役立ちます。 たとえば、シアトルのすべてのエアコン デバイス グループの一覧を作成して、技術者が自分の担当するデバイスを検索できるようにできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス グループを作成する
> * デバイス グループを使用してデバイス テレメトリを分析する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

## <a name="add-and-customize-a-device-template"></a>デバイス テンプレートを追加およびカスタマイズする

デバイス カタログからデバイス テンプレートを追加します。 このチュートリアルでは、**ESP32-Azure IoT Kit** デバイス テンプレートを使用します。

1. 新しいデバイス テンプレートを追加するには、 **[デバイス テンプレート]** ページで **[+ 新規]** を選択します。

1. **[種類の選択]** ページの **[Use a preconfigured device template]\(構成済みのデバイス テンプレートを使用する\)** セクションで、 **[ESP32-Azure IoT Kit]** タイルが表示されるまで下にスクロールします。

1. **[ESP32-Azure IoT Kit]** タイルを選択し、 **[Next: Review]\(次へ: 確認\)** をクリックします。

1. **[Review]\(レビュー\)** ページで、 **[Create]\(作成\)** を選択します。

作成したテンプレートの名前は **Sensor Controller** です。 このモデルには、**Sensor Controller**、**SensorTemp**、**Device Information interface** などのコンポーネントが含まれています。 コンポーネントによって、ESP32 デバイスの機能が定義されます。 機能には、テレメトリ、プロパティ、コマンドが含まれます。

**Sensor Controller** デバイス テンプレートに 2 つのクラウド プロパティを追加します。

1. **[クラウド プロパティ]** 、 **[+ クラウド プロパティの追加]** の順に選択します。 下表の情報に従って、デバイス テンプレートに 2 つのクラウド プロパティを追加します。

    | 表示名      | セマンティックの種類 | スキーマ |
    | ----------------- | ------------- | ------ |
    | Last Service Date | なし          | Date   |
    | Customer Name     | なし          | String |

1. **[保存]** を選択して変更を保存します。

デバイスを管理するためにデバイス テンプレートに新しいフォームを追加します。

1. **[ビュー]** ノードを選択し、 **[デバイスとクラウドのデータの編集]** タイルを選択して新しいビューを追加します。

1. フォーム名を「**デバイスの管理**」に変更します。

1. **[顧客名]** および **[Last Service Date]\(前回点検日\)** クラウド プロパティと、 **[Target Temperature]\(目標温度\)** プロパティを選択します。 次に、 **[セクションの追加]** を選択します。

1. **[保存]** を選択して新しいフォームを保存します。

次にデバイス テンプレートを発行します。

## <a name="create-simulated-devices"></a>シミュレートされたデバイスを作成する

デバイス グループを作成する前に、このチュートリアルで使用する **Sensor Controller** デバイス テンプレートに基づいてシミュレートされたデバイスを少なくとも 5 つ追加します。

:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="5 つのシミュレートされたセンサー コントローラー デバイスを示すスクリーンショット":::

シミュレートされたセンサー デバイスの 4 つに対し、 **[デバイスの管理]** ビューを使用して、顧客名を *Contoso* に設定し、 **[保存]** を選択します。

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="[顧客名] クラウド プロパティの設定方法を示すスクリーンショット":::

## <a name="create-a-device-group"></a>デバイス グループを作成する

1. 左側のペインで **[デバイス グループ]** を選択して、デバイス グループ ページに移動します。

1. **[+新規]** を選択します。

1. デバイス グループに *Contoso devices* という名前を付けます。 説明を追加することもできます。 デバイス グループに含めることができるのは、1 つのデバイス テンプレートのデバイスだけです。 このグループに使用する **Sensor Controller** デバイス テンプレートを選択します。

1. **Contoso** に属するデバイスのみを含むようにデバイス グループをカスタマイズするには、 **[+ フィルター]** を選択します。 **[顧客名]** プロパティ、 **[等しい]** 比較演算子、値として **[Contoso]** を選択します。 デバイス グループに設定されている **すべて** のフィルター条件を満たす複数のフィルターとデバイスを追加できます。 作成したデバイス グループには、アプリケーションにアクセスできるすべてのユーザーがアクセスできるため、だれでもデバイス グループを表示、変更、または削除できます。

    > [!TIP]
    > デバイス グループは動的なクエリです。 デバイスの一覧を表示するたびに、一覧内のデバイスが異なることがあります。 一覧は、現在クエリの条件を満たしているデバイスによって異なります。

1. **[保存]** を選択します。

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="デバイス グループのクエリ構成を示すスクリーンショット":::

> [!NOTE]
> Azure IoT Edge デバイスの場合、Azure IoT Edge テンプレートを選択してデバイス グループを作成します。

## <a name="analytics"></a>Analytics

**Analytics** をデバイス グループと共に使用して、グループ内のデバイスからのテレメトリを分析することができます。 たとえば、すべての Contoso 環境センサーによってレポートされた平均温度をプロットできます。

デバイス グループのテレメトリを分析するには、次のようにします。

1. 左側のウィンドウで **[Analytics]** を選択します。

1. 作成した **[Contoso デバイス]** デバイス グループを選択します。 次に、 **[温度]** と **[湿度]** 両方のテレメトリの種類を追加します。

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="分析用に選択されたテレメトリの種類を示すスクリーンショット":::

    テレメトリの種類の横にある省略記号アイコンを使用して、集計の種類を選択します。 既定値は、 **[平均]** です。 **[グループ化]** を使用して、集計データの表示方法を変更します。 たとえば、デバイス ID で分割する場合、 **[分析]** を選択するとデバイスごとにプロットが表示されます。

1. 平均テレメトリ値を表示するには、 **[分析]** を選択します。

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="すべての Contoso デバイスの平均値を示すスクリーンショット":::

    ビューをカスタマイズしたり、表示される期間を変更したり、CSV としてデータをエクスポートしたり、テーブルとしてデータを表示したりできます。

    :::image type="content" source="media/tutorial-use-device-groups/export-data.png" alt-text="Contoso デバイスのデータをエクスポートする方法を示すスクリーンショット":::

分析に関する詳細については、「[分析を使用してデバイス データを分析する方法](howto-create-analytics.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

ここでは、Azure IoT Central アプリケーションでデバイス グループを使用する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [テレメトリのルールを作成する方法](tutorial-create-telemetry-rules.md)
