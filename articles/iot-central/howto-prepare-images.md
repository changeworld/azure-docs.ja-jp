---
title: イメージを Azure IoT Central アプリケーションにアップロードする | Microsoft Docs
description: 開発者として、イメージを準備して Azure IoT Central アプリケーションにアップロードする方法を学習します。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a43f2dd780604235ada1d8e3ae8a20563042fbaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200233"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>イメージを準備して Azure IoT Central アプリケーションにアップロードする

この記事では、開発者として、カスタム イメージをアップロードすることによって Microsoft Azure IoT Central アプリケーションをカスタマイズする方法について説明します。 たとえば、デバイスの画像を使用してデバイス ダッシュボードをカスタマイズできます。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

1. Azure IoT Central アプリケーション。 詳細については、「[Azure IoT Central アプリケーションを作成する](howto-create-application.md)」を参照してください。
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

| 場所 | サイズ |
| -------- | ------ |
| **アプリケーション マネージャー** | 268 x 160 ピクセル |
| デバイス テンプレート | 64 x 64 ピクセル |
| ホーム ページおよびダッシュボード タイル | 最小サイズのタイルは 200 x 200 ピクセルであり、より大きいタイルは小さいタイルの正方形または長方形の倍数にすることができます。 たとえば、200 x 400 ピクセル、400 x 200 ピクセル、または 400 x 400 ピクセルにできます。 |

アプリケーションで最適に表示されるには、前の表に示されている寸法に一致するイメージを作成する必要があります。

## <a name="upload-the-images"></a>イメージをアップロードする

以降のセクションでは、さまざまな場所で使用するイメージをアップロードする方法について説明します。

### <a name="application-manager"></a>アプリケーション マネージャー

**アプリケーション マネージャー**で使用するイメージをアップロードするには、**[管理]** セクションの **[アプリケーションの設定]** ページに移動します。 このタスクを完了するには、管理者である必要があります。

![アプリケーションのイメージをアップロードする](media/howto-prepare-images/uploadapplicationmanager.png)

アップロード イメージをクリックしてから、ローカル コンピューターからアップロードするファイルを選択します。

### <a name="home-page"></a>ホーム ページ

ホーム ページで使用するイメージをアップロードするには、アプリケーションの **[ホームページ]** に移動し、デザイン モードをオンに切り替えます。 このタスクを完了するには、開発者である必要があります。

![ホーム ページのイメージをアップロードする](media/howto-prepare-images/uploadhomepage.png)

アップロード イメージをクリックしてから、ローカル コンピューターからアップロードするファイルを選択します。

イメージ アップロードの後、デザイン モードがオンに切り替えられている間にそのサイズを変更できます。

### <a name="device-template"></a>デバイス テンプレート

デバイス テンプレートで使用するイメージをアップロードするには、**Device Explorer** に移動し、デバイス テンプレート、デバイスの順に選択してから、デザイン モードをオンに切り替えます。 このタスクを完了するには、開発者である必要があります。

![デバイス テンプレートのイメージをアップロードする](media/howto-prepare-images/uploaddevicetemplate.png)

アップロード イメージをクリックしてから、ローカル コンピューターからアップロードするファイルを選択します。

### <a name="device-dashboard"></a>デバイス ダッシュボード

デバイス ダッシュボードで使用するイメージをアップロードするには、**Device Explorer** に移動し、デバイス テンプレート、デバイスの順に選択します。 次に、**[ダッシュボード]** ページを選択し、デザイン モードをオンに切り替えます。 このタスクを完了するには、開発者である必要があります。

![デバイス ダッシュボードのイメージをアップロードする](media/howto-prepare-images/uploaddevicedashboard.png)

アップロード イメージをクリックしてから、ローカル コンピューターからアップロードするファイルを選択します。

イメージ アップロードの後、**デザイン モード**がオンに切り替えられている間にそのサイズや位置を変更できます。

### <a name="device-set-dashboard"></a>デバイス セット ダッシュボード

デバイス セット ダッシュボードで使用するイメージをアップロードするには、**[Device Sets] (デバイス セット)** に移動し、デバイス セット、デバイスの順に選択します。 次に、**[ダッシュボード]** ページを選択し、**デザイン モード**をオンに切り替えます。

![デバイス セット ダッシュボードのイメージをアップロードする](media/howto-prepare-images/uploaddevicesetdashboard.png)

アップロード イメージをクリックしてから、ローカル コンピューターからアップロードするファイルを選択します。

イメージ アップロードの後、デザイン モードがオンに切り替えられている間にそのサイズや位置を変更できます。

## <a name="next-steps"></a>次の手順

ここでは、イメージを準備して Azure IoT Central アプリケーションにアップロードする方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションでデバイスを管理する](howto-manage-devices.md)