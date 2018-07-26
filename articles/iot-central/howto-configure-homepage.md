---
title: Azure IoT Central アプリケーションのホーム ページを構成する | Microsoft Docs
description: 開発者として、Azure IoT Central アプリケーションのホーム ページを構成する方法を学習します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 93f406a1d5e4a8c2ce5ad1db0c3936dd3ad2bfb9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992355"
---
## <a name="configuring-homepage"></a>ホーム ページの構成

ホーム ページは、アクセス権を持つユーザーがアプリケーションの URL に移動したときに読み込まれるページです。 アプリケーションの作成時に、[Sample Contoso]\(サンプル Contoso\) または [Sample Devkits]\(サンプル Devkits\) アプリケーション テンプレートのいずれかを選択した場合、アプリケーションには定義済みのホーム ページが含まれます。 一方、[カスタム アプリケーション] アプリケーション テンプレートを選択した場合、ホーム ページは空白になります。

たとえば、次に示すのは、[Sample Contoso]\(サンプル Contoso\) テンプレートに基づくアプリケーションのホーム ページです。 アプリケーションのホーム ページをカスタマイズするには、まず右上にある **[デザイン モード]** を **[オン]** にします。 

![[Sample Contoso]\(サンプル Contoso\) テンプレートに基づくアプリケーションのホーム ページ](media\howto-configure-homepage\image1.png)

**[デザイン モード]** を **[オン]** にすると、左側のパネルにダッシュボード ライブラリが開きます。 ホーム ページをカスタマイズするために追加できるタイルとダッシュボード プリミティブにはさまざまな種類があります。

![ダッシュボード ライブラリ](media\howto-configure-homepage\image2.png)

たとえば、**[設定とプロパティ]** タイルを追加して、設定とプロパティの現在の値の選択を表示できます。 これを行うには、まず **[デバイス テンプレート]** を選択し、次に **[デバイス インスタンス]** を選択します。 その後、タイルのタイトルを指定し、表示する**設定**または**プロパティ**を選択します。 ここでは、**[ファン速度]** が選択されています。 **[保存]** をクリックすると、このタイルがホーム ページに表示されます。

![設定とプロパティの詳細が表示された [Configure Device Details]\(デバイスの詳細の構成\) フォーム](media\howto-configure-homepage\image3.png)

これで、オペレーターがこのホーム ページを表示したときに、デバイスの設定とプロパティを表示するこのタイルを確認できます。

![タイルの設定とプロパティが表示された [Dashboard]\(ダッシュボード\) タブ](media\howto-configure-homepage\image4.png)

ライブラリ内の他のさまざまなタイルの種類を使用して、アプリケーションのホーム ページをどのようにカスタマイズできるかを試してみてください。

## <a name="next-steps"></a>次の手順

これで、Azure IoT Central のホーム ページの構成方法を習得したので、次を実行できます。

> [!div class="nextstepaction"]
> [イメージを準備してアップロードする](howto-prepare-images.md)
