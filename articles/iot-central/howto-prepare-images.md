---
title: イメージを Azure IoT Central アプリケーションにアップロードする | Microsoft Docs
description: 開発者として、イメージを準備して Azure IoT Central アプリケーションにアップロードする方法を学習します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b55b9929129b882f893b1fea58b7061131daffb8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005140"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>イメージを準備して Azure IoT Central アプリケーションにアップロードする

この記事では、開発者として、カスタム イメージをアップロードすることによって Azure IoT Central アプリケーションをカスタマイズする方法について説明します。 たとえば、デバイスの画像を使用してデバイス ダッシュボードをカスタマイズできます。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

1. Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
1. イメージ ファイルをスケーリングおよびサイズ変更するためのツール。

## <a name="choose-where-to-use-custom-images"></a>カスタム イメージを使用する場所を選択する

カスタム イメージは、Azure IoT Central アプリケーション内の次の場所に追加できます。

* **[Application Manager] (アプリケーション マネージャー)** ページ

    ![[Application Manager] (アプリケーション マネージャー) ページ上のイメージ](media/howto-prepare-images/applicationmanager.png)

* ホーム ページ

    ![ホーム ページ上のイメージ](media/howto-prepare-images/homepage.png)

* デバイス テンプレート

    ![デバイス テンプレート上のイメージ](media/howto-prepare-images/devicetemplate.png)

* デバイス ダッシュボード上のタイル

    ![デバイス タイル上のイメージ](media/howto-prepare-images/devicetile.png)

* デバイス セット ダッシュボード上のタイル

    ![デバイス セット タイル上のイメージ](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>イメージを準備する

4 つのすべての場所で、PNG、GIF、または JPEG イメージのいずれかを使用できます。

次の表は、使用できるイメージ サイズをまとめたものです。

| Location | サイズ |
| -------- | ------ |
| アプリケーション マネージャー | 268 x 160 ピクセル |
| デバイス テンプレート | 64 x 64 ピクセル |
| ホーム ページおよびダッシュボード タイル | 最小サイズのタイルは 200 x 200 ピクセルであり、より大きいタイルは小さいタイルの正方形または長方形の倍数にすることができます。 たとえば、200 x 400 ピクセル、400 x 200 ピクセル、または 400 x 400 ピクセルにできます。 |

アプリケーションで最適に表示されるには、前の表に示されている寸法に一致するイメージを作成する必要があります。

## <a name="upload-the-images"></a>イメージをアップロードする

以降のセクションでは、さまざまな場所にあるイメージをアップロードする方法について説明します。

### <a name="application-manager"></a>アプリケーション マネージャー

**アプリケーション マネージャー**にイメージをアップロードするには、**[管理]** セクションの **[アプリケーションの設定]** ページに移動します。 このタスクを完了するには、管理者である必要があります。

![アプリケーションのイメージをアップロードする](media/howto-prepare-images/uploadapplicationmanager.png)

[Application Image]\(アプリケーション イメージ\) タイルをクリックして、準備済みのイメージ (268 × 160 ピクセル) をローカル マシンからアップロードします。

### <a name="home-page"></a>ホーム ページ

ホーム ページ上のイメージをアップロードするには、アプリケーションの **[ホームページ]** に移動して、**[編集]** をクリックします。 このタスクを完了するには、開発者である必要があります。

![ホーム ページのイメージをアップロードする](media/howto-prepare-images/uploadhomepage.png)

[Configure Image]\(イメージの構成\) で [イメージ] タイルをクリックして、準備済みのイメージをローカル マシンからアップロードします。 最小サイズのタイルは 200 x 200 ピクセルであり、より大きいタイルは小さいタイルの正方形または長方形の倍数にすることができます。 たとえば、200 x 400 ピクセル、400 x 200 ピクセル、または 400 x 400 ピクセルにできます。

アップロードされたイメージを **[保存]** します。 編集モードでは、イメージのサイズを変更することができます。 終了したら、**[完了]** をクリックします。 

### <a name="device-template"></a>デバイス テンプレート

イメージをデバイス テンプレートにアップロードするには、**Device Explorer** に移動し、デバイス テンプレート、デバイスの順に選択します。 このタスクを完了するには、開発者である必要があります。

![デバイス テンプレートのイメージをアップロードする](media/howto-prepare-images/uploaddevicetemplate.png)

イメージのタイルをクリックして、準備済みのイメージ (64 x 64 ピクセル) をローカル マシンからアップロードします。 

### <a name="device-dashboard"></a>デバイス ダッシュボード

イメージをデバイス ダッシュボードにアップロードするには、**Device Explorer** に移動し、デバイス テンプレート、デバイスの順に選択します。 次に、**[ダッシュボード]** ページを選択し、**[テンプレートの編集]** をクリックします。 このタスクを完了するには、開発者である必要があります。

![デバイス ダッシュボードのイメージをアップロードする](media/howto-prepare-images/uploaddevicedashboard.png)

[Configure Image]\(イメージの構成\) で [イメージ] タイルをクリックして、ローカル マシンからアップロードするファイルを選択します。 最小サイズのタイルは 200 x 200 ピクセルであり、より大きいタイルは小さいタイルの正方形または長方形の倍数にすることができます。 たとえば、200 x 400 ピクセル、400 x 200 ピクセル、または 400 x 400 ピクセルにできます。

アップロードされたイメージを **[保存]** します。 編集モードでは、イメージのサイズと配置を変更することができます。 終了したら、**[完了]** をクリックします。

### <a name="device-set-dashboard"></a>デバイス セット ダッシュボード

イメージをデバイス セット ダッシュボードにアップロードするには、**[Device Sets]\(デバイス セット\)** に移動し、デバイス セット、デバイスの順に選択します。 次に、**[ダッシュボード]** ページを選択し、**[編集]** をクリックします。

![デバイス セット ダッシュボードのイメージをアップロードする](media/howto-prepare-images/uploaddevicesetdashboard.png)

[Configure Image]\(イメージの構成\) で [イメージ] タイルをクリックして、準備済みのイメージをローカル マシンからアップロードします。 最小サイズのタイルは 200 x 200 ピクセルであり、より大きいタイルは小さいタイルの正方形または長方形の倍数にすることができます。 たとえば、200 x 400 ピクセル、400 x 200 ピクセル、または 400 x 400 ピクセルにできます。

アップロードされたイメージを **[保存]** します。 編集モードでは、イメージのサイズと配置を変更することができます。 終了したら、**[完了]** をクリックします。

## <a name="next-steps"></a>次の手順

ここでは、イメージを準備して Azure IoT Central アプリケーションにアップロードする方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションでデバイスを管理する](howto-manage-devices.md)