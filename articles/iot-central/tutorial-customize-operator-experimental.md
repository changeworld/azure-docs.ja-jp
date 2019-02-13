---
title: Azure IoT Central でオペレーター用ビューをカスタマイズする | Microsoft Docs
description: 作成者として、Azure IoT Central アプリケーションのオペレーター用ビューをカスタマイズします。
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 267a619fe32a8d4af0ee9cc8a5001d7a321c3098
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765151"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>チュートリアル:Azure IoT Central オペレーターのビューをカスタマイズする (新しい UI デザイン)

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>前提条件

開始する前に、前の 2 つのチュートリアルを完了しておく必要があります。

* [Azure IoT Central アプリケーションで新しいデバイスの種類を定義する](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。
* [デバイスのルールとアクションを構成する](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

## <a name="configure-your-device-dashboard"></a>デバイス ダッシュボードを構成する

作成者はデバイス ダッシュボードに表示される情報を定義できます。 [アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)に関するチュートリアルでは、折れ線グラフとその他の情報を **Connected Air Conditioner** ダッシュボードに追加しました。

1. **Connected Air Conditioner** デバイス テンプレートを編集するには、左側のナビゲーション メニューにある **[デバイス テンプレート]** を選択します。

    ![[デバイス テンプレート] ページ](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. デバイス ダッシュボードをカスタマイズするには、[アプリケーションでの新しいデバイスの種類の定義](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)に関するチュートリアルで作成した **Connected Air Conditioner (1.0.0)** デバイス テンプレートを選択します。

3. ダッシュボードを編集するには、**[ダッシュボード]** タブを選択します。

4. ダッシュボードに主要業績評価指標 (KPI) タイルを追加するには、**[KPI]** を選択します。

    KPI を定義するには、次の表の情報を使用します。

    | Setting     | 値 |
    | ----------- | ----- |
    | Name        | Maximum temperature |
    | 時間範囲  | Past 1 week |
    | 測定タイプ | テレメトリ |
    | Measurement | 温度 |
    | 集計 | 最大値 |
    | 表示  | Enabled |

    ![KPI を追加する](media/tutorial-customize-operator-experimental/addkpi.png)

5. **[Save]** をクリックします。 ダッシュボードに KPI タイルが表示されます。

    ![KPI タイル](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. ダッシュボード上のタイルを移動させたり、サイズ変更したりするには、そのタイルの上にマウス ポインターを移動させます。 タイルを新しい場所にドラッグしたり、サイズ変更したりできます。

## <a name="configure-your-settings-layout"></a>設定のレイアウトを構成する

作成者は、オペレーター用のデバイス設定ビューを構成することもできます。 オペレーターは、デバイス設定タブを使用してデバイスを構成します。 たとえば、オペレーターはコネクテッド空調機の目標温度を設定するために設定タブを使用します。

1. コネクテッド空調機の設定のレイアウトを編集するには、**[設定]** タブを選択します。

2. 設定タイルの移動とサイズ変更を行えます。

    ![設定のレイアウトを編集する](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>プロパティのレイアウトを構成する

ダッシュボードと設定に加えて、オペレーター用のデバイス プロパティ ビューを構成することもできます。 オペレーターはデバイス プロパティ タブを使用してデバイス メタデータを管理します。 たとえばオペレーターは、デバイスのシリアル番号を表示したり、製造元の連絡先の詳細を更新したりするためにプロパティ タブを使用します。

1. コネクテッド空調機のプロパティのレイアウトを編集するには、**[プロパティ]** タブを選択します。

2. プロパティ フィールドの移動とサイズ変更を行えます。

    ![プロパティのレイアウトを編集する](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>オペレーターとしてコネクテッド空調デバイスをプレビューする

オペレーター用のダッシュボード タブ、設定タブ、プロパティ タブをカスタマイズするには、**[デバイス テンプレート]** ページを使用します。 デバイス テンプレートを表示および使用するには、**Device Explorer** ページを使用します。

1. オペレーターとして Connected Air Conditioner テンプレートを表示および使用するには、**Device Explorer** ページに移動し、IoT Central でテンプレートから生成された、シミュレートされたデバイスを選択します。

    ![デバイス テンプレートを表示および使用する](media/tutorial-customize-operator-experimental/usetemplate.png)

2. このデバイスの場所を更新するには、**[プロパティ]** を選択して、[場所] タイルの値を編集します。 その後、**[保存]** をクリックします。

    ![プロパティ値を編集する](media/tutorial-customize-operator-experimental/editproperty.png)

3. 設定をコネクテッド空調機に送信するには、**[設定]** を選択してタイルの設定値を変更し、**[更新]** を選択します。

    ![設定をデバイスに送信する](media/tutorial-customize-operator-experimental/sendsetting.png)

    デバイスが新しい設定値を認識すると、タイルに表示される設定が**同期済み**になります。

4. オペレーターは、作成者が構成したデバイス ダッシュボードを表示できます。

    ![オペレーター用のデバイス ダッシュボード ビュー](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>既定のホーム ページを構成する

作成者またはオペレーターが Azure IoT Central アプリケーションにサインインすると、ホーム ページが表示されます。 作成者は、オペレーターにとって最も有益で関連性の高い内容が含まれるよう、このホーム ページの内容を構成できます。

1. 既定のホーム ページをカスタマイズするには、**[ホーム]** ページに移動し、ページの右上にある **[編集]** を編集します。 **[ホーム]** ページに追加できるオブジェクトの一覧が含まれるパネルが、左側からスライドして出てきます。

    ![[Application Builder]\(アプリケーション ビルダー\) ページ](media/tutorial-customize-operator-experimental/builderhome.png)

2. **[ホーム]** ページをカスタマイズするには、**[ライブラリ]** からタイルを追加します。 **[リンク]** を選択し、組織の Web サイトの詳細を追加します。 その後、**[保存]** をクリックします。

    ![リンクをホーム ページに追加する](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > リンクは Azure IoT Central アプリケーション内のページに追加することもできます。 たとえば、デバイス ダッシュボードまたは設定のページにリンクを追加することができます。

3. 必要に応じて **[イメージ]** を選択し、ホーム ページに表示するイメージをアップロードします。 イメージには、クリックしたときの移動先となる URL を指定できます。

    ![イメージをホーム ページに追加する](media/tutorial-customize-operator-experimental/addimage.png)

    詳細については、[イメージを準備して Azure IoT Central アプリケーションにアップロードする方法](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)に関するページを参照してください。

## <a name="preview-the-default-home-page-as-an-operator"></a>オペレーターとして既定のホーム ページをプレビューする

オペレーターとしてホーム ページをプレビューするには、ページの右上隅にある **[完了]** をクリックします。

![デザイン モードを切り替える](media/tutorial-customize-operator-experimental/operatorviewhome.png)

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

* [(オペレーターとして) デバイスを監視する](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [(オペレーターおよびデバイス開発者として) 新しいデバイスをアプリケーションに追加する](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
