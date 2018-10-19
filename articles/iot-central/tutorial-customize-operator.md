---
title: Azure IoT Central でオペレーター用ビューをカスタマイズする | Microsoft Docs
description: 作成者として、Azure IoT Central アプリケーションのオペレーター用ビューをカスタマイズします。
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d99b76faf618439e51735d5f1096fd4f1cfd2364
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038291"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>チュートリアル: Azure IoT Central オペレーターのビューをカスタマイズする

このチュートリアルでは、アプリケーションのオペレーター用ビューをカスタマイズする方法を作成者向けに説明します。 作成者としてアプリケーションに変更を行う場合、Microsoft Azure IoT Central アプリケーションのオペレーター用ビューをプレビューできます。

このチュートリアルでは、コネクテッド空調デバイスの関連情報がオペレーターに表示されるよう、アプリケーションをカスタマイズします。 カスタマイズを行うことで、オペレーターがアプリケーションに接続された空調デバイスを管理できるようになります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス ダッシュボードを構成する
> * デバイス設定のレイアウトを構成する
> * デバイス プロパティのレイアウトを構成する
> * オペレーターとしてデバイスをプレビューする
> * 既定のホーム ページを構成する
> * オペレーターとして既定のホーム ページをプレビューする

## <a name="prerequisites"></a>前提条件

開始する前に、前の 2 つのチュートリアルを完了しておく必要があります。

* [Azure IoT Central アプリケーションで新しいデバイスの種類を定義する](tutorial-define-device-type.md)。
* [デバイスのルールとアクションを構成する](tutorial-configure-rules.md)。

## <a name="configure-your-device-dashboard"></a>デバイス ダッシュボードを構成する

作成者はデバイス ダッシュボードに表示される情報を定義できます。 [アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルでは、折れ線グラフとその他の情報を **Connected Air Conditioner-1** ダッシュボードに追加しました。

1. **Connected Air Conditioner** デバイス テンプレートを編集するには、左側のナビゲーション メニューにある **[エクスプローラー]** を選択します。

    ![[エクスプローラー] ページ](media/tutorial-customize-operator/explorer.png)

2. コネクテッド空調デバイスのダッシュボードのカスタマイズを開始するには、**Connected Air Conditioner (1.0.0)** デバイス テンプレートを選択します。 [アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type.md)に関するチュートリアルで作成した **Connected Air Conditioner-1** デバイスを選択します。

    ![コネクテッド空調デバイスを選択する](media/tutorial-customize-operator/selectdevice.png)

    **Connected Air Conditioner-1** などのデバイス内の場合、**[テンプレートの編集]** を選択して基礎となるテンプレートに変更を加えることができます。 詳細については、「[Create a new device template version (デバイス テンプレートの新しいバージョンを作成する)](howto-version-devicetemplate.md)」を参照してください。

3. ダッシュボードを編集するには、**[ダッシュボード]** を選択し、**[テンプレートの編集]** を選択します。

    ![デバイス テンプレートのダッシュボードのページ](media/tutorial-customize-operator/dashboard.png)

4. ダッシュボードに KPI タイルを追加するには、**[KPI]** を選択します。

    ![KPI を追加する](media/tutorial-customize-operator/addkpi.png)

    KPI を定義するには、次の表の情報を使用します。

    | Setting     | 値 |
    | ----------- | ----- |
    | Name        | Maximum temperature |
    | Measurement | 温度 |
    | 集計 | 最大値 |
    | 時間範囲  | Past 1 week |

5. **[保存]** を選択します。 ダッシュボードに KPI タイルが表示されます。

    ![KPI タイル](media/tutorial-customize-operator/temperaturekpi.png)

6. ダッシュボード上のタイルを移動させたり、サイズ変更したりするには、そのタイルの上にマウス ポインターを移動させます。 タイルを新しい場所にドラッグしたり、サイズ変更したりできます。

    ![ダッシュボードのレイアウトを編集する](media/tutorial-customize-operator/dashboardlayout.png)

7. 変更が完了したら、**[完了]** をクリックします。

## <a name="configure-your-settings-layout"></a>設定のレイアウトを構成する

作成者は、オペレーター用のデバイス設定ビューを構成することもできます。 オペレーターは、デバイス設定ページを使用してデバイスを構成します。 たとえば、オペレーターは冷却装置の目標温度を設定するために設定ページを使用します。

1. コネクテッド空調機の設定のレイアウトを編集するには、**[設定]** を選択し、**[テンプレートの編集]** を選択します。

    ![[設定] ページ](media/tutorial-customize-operator/settings.png)

2. 設定タイルの移動とサイズ変更を行えます。

    ![設定のレイアウトを編集する](media/tutorial-customize-operator/settingslayout.png)

3. 変更が完了したら、**[完了]** をクリックします。

> [!NOTE]
> **[テンプレートの編集]** モードでは、設定の値を編集できません。

## <a name="configure-your-properties-layout"></a>プロパティのレイアウトを構成する

ダッシュボードと設定に加えて、オペレーター用のデバイス プロパティ ビューを構成することもできます。 オペレーターはデバイス プロパティ ページを使用してデバイス メタデータを管理します。 たとえばオペレーターは、デバイスのシリアル番号を表示したり、製造元の連絡先の詳細を更新したりするためにプロパティ ページを使用します。

1. コネクテッド空調機のプロパティのレイアウトを編集するには、**[プロパティ]** を選択し、**[テンプレートの編集]** を選択します。

    ![[プロパティ] ページ](media/tutorial-customize-operator/properties.png)

2. プロパティ フィールドの移動とサイズ変更を行えます。

    ![プロパティのレイアウトを編集する](media/tutorial-customize-operator/propertieslayout.png)

3. 変更が完了したら、**[完了]** をクリックします。

> [!NOTE]
> **[テンプレートの編集]** モードでは、プロパティの値を編集できません。

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>オペレーターとしてコネクテッド空調デバイスをプレビューする

**[テンプレートの編集]** モードでは、オペレーター用のダッシュボード ページ、設定ページ、プロパティ ページをカスタマイズできます。 **[テンプレートの編集]** モードではない場合、オペレーターとしてアプリケーションを表示できます。

1. オペレーターとしてコネクテッド空調デバイスを表示するには、テンプレートの編集を停止するために、**[完了]** をクリックする必要があります。 これで、デバイスのオペレーター表示に戻ります。

2. このデバイスの場所を更新するには、[Location]\(場所\) タイルの値を編集して、**[保存]** を選択します。

    ![プロパティ値を編集する](media/tutorial-customize-operator/editproperty.png)

3. 設定をコネクテッド空調機に送信するには、**[設定]** を選択してタイルの設定値を変更し、**[更新]** を選択します。

    ![設定をデバイスに送信する](media/tutorial-customize-operator/sendsetting.png)

    デバイスが新しい設定値を認識すると、タイルに表示される設定が**同期済み**になります。

4. オペレーターは、作成者が構成したデバイス ダッシュボードを表示できます。

    ![オペレーター用のデバイス ダッシュボード ビュー](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>既定のホーム ページを構成する

作成者またはオペレーターが Azure IoT Central アプリケーションにサインインすると、ホーム ページが表示されます。 作成者は、オペレーターにとって最も有益で関連性の高い内容が含まれるよう、このホーム ページの内容を構成できます。

1. 既定のホーム ページをカスタマイズするには、**ホーム** ページに移動して、ページの右上にある **[編集]** を選択します。 **[編集]** を選択すると、ホーム ページに追加できるオブジェクトの一覧を含むパネルが、右側からスライドして表示されます。

    ![[Application Builder]\(アプリケーション ビルダー\) ページ](media/tutorial-customize-operator/builderhome.png)

2. ホーム ページをカスタマイズするには、**[ライブラリ]** からタイルを追加します。 **[リンク]** を選択し、組織の Web サイトの詳細を追加します。 次に、**[保存]** を選択します。

    ![リンクをホーム ページに追加する](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > リンクは Azure IoT Central アプリケーション内のページに追加することもできます。 たとえば、デバイス ダッシュボードまたは設定のページにリンクを追加することができます。

3. 必要に応じて **[イメージ]** を選択し、ホーム ページに表示するイメージをアップロードします。 イメージには、クリックしたときの移動先となる URL を指定できます。

    ![イメージをホーム ページに追加する](media/tutorial-customize-operator/addimage.png)

    詳細については、[イメージを準備して Azure IoT Central アプリケーションにアップロードする方法](howto-prepare-images.md)に関するページを参照してください。

## <a name="preview-the-default-home-page-as-an-operator"></a>オペレーターとして既定のホーム ページをプレビューする

オペレーターとしてホーム ページをプレビューし、編集を終了するには、ページの右上隅にある **[完了]** を選択します。

![デザイン モードを切り替える](media/tutorial-customize-operator/operatorviewhome.png)

リンク タイルとイメージ タイルをクリックすると、作成者として設定した URL に移動できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、アプリケーションのオペレーター用ビューをカスタマイズする方法について説明しました。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * デバイス ダッシュボードを構成する
> * デバイス設定のレイアウトを構成する
> * デバイス プロパティのレイアウトを構成する
> * オペレーターとしてデバイスをプレビューする
> * 既定のホーム ページを構成する
> * オペレーターとして既定のホーム ページをプレビューする

これで、アプリケーションのオペレーター用ビューをカスタマイズする方法についての説明は終了です。次の手順として、以下が推奨されます。

* [(オペレーターとして) デバイスを監視する](tutorial-monitor-devices.md)
* [(オペレーターおよびデバイス開発者として) 新しいデバイスをアプリケーションに追加する](tutorial-add-device.md)
