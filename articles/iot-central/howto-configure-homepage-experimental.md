---
title: Azure IoT Central アプリケーションのホーム ページを構成する | Microsoft Docs
description: 開発者として、Azure IoT Central アプリケーションのホーム ページを構成する方法を学習します。
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772256"
---
# <a name="configuring-homepage"></a>ホーム ページの構成

ホーム ページは、アプリケーションにアクセスできるユーザーがアプリケーションの URL に移動したときに読み込まれるページです。 アプリケーションの作成時に、[Sample Contoso]\(サンプル Contoso\) または [Sample Devkits]\(サンプル Devkits\) アプリケーション テンプレートのいずれかを選択した場合、アプリケーションには定義済みのホーム ページが含まれます。 一方、[カスタム アプリケーション] アプリケーション テンプレートを選択した場合、ホーム ページは空白になります。

## <a name="add-tiles"></a>タイルを追加する

たとえば、次に示すのは、[Sample Contoso]\(サンプル Contoso\) テンプレートに基づくアプリケーションのホーム ページです。 ご利用のアプリケーションのホーム ページをカスタマイズするには、まず右上にある **[編集]** を選択します。 

![[Sample Contoso]\(サンプル Contoso\) テンプレートに基づくアプリケーションのホーム ページ](media/howto-configure-homepage-experimental/image1.png)

**[編集]** を選択すると、左側のパネルにダッシュボード ライブラリが開きます。 ホーム ページをカスタマイズするために追加できるタイルとダッシュボード プリミティブにはさまざまな種類があります。

![ダッシュボード ライブラリ](media/howto-configure-homepage-experimental/image2.png)

たとえば、**[設定とプロパティ]** タイルを追加して、設定とプロパティの現在の値の選択を表示できます。 これを行うには、まず **[デバイス テンプレート]** を選択し、次に **[デバイス インスタンス]** を選択します。 その後、タイルのタイトルを指定し、表示する**設定**または**プロパティ**を選択します。 ここでは、**[Set Temperature]\(温度の設定\)** が選択されています。 **[完了]** をクリックすると、このタイルがホーム ページに表示されます。

![設定とプロパティの詳細が表示された [Configure Device Details]\(デバイスの詳細の構成\) フォーム](media/howto-configure-homepage-experimental/image3.png)

これで、オペレーターがこのホーム ページを表示したときに、デバイスの設定とプロパティを表示するこのタイルを確認できます。

![タイルの設定とプロパティが表示された [Dashboard]\(ダッシュボード\) タブ](media/howto-configure-homepage-experimental/image4.png)

ライブラリ内の他のさまざまなタイルの種類を使用して、アプリケーションのホーム ページをどのようにカスタマイズできるかを試してみてください。

## <a name="next-steps"></a>次の手順

これで、Azure IoT Central のホーム ページの構成方法を習得したので、次を実行できます。

> [!div class="nextstepaction"]
> [イメージを準備してアップロードする](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
