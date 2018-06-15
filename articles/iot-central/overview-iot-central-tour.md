---
title: 'ツアー: Azure IoT Central の UI | Microsoft Docs'
description: ビルダーとして、IoT ソリューションの作成に使用する Azure IoT Central の UI の主要な領域を把握しておきましょう。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/13/2018
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 69898358026eab716c057f339d8594df43db136f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629373"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>ツアー: Azure IoT Central の UI

この記事では、Microsoft Azure IoT Central の UI について取り上げます。 Azure IoT Central のソリューションとそこに接続されるデバイスは、UI を使用して作成、管理、使用することができます。

"_ビルダー_" は、Azure IoT Central の UI を使用して、Azure IoT Central ソリューションを定義します。 この UI を使用して次の作業を行うことができます。

- ソリューションに接続するデバイスの種類を定義します。
- デバイスのルールとアクションを構成します。
- ソリューションを使用する "_オペレーター_" 向けに UI をカスタマイズします。

"_オペレーター_" は、Azure IoT Central の UI を使用して、Azure IoT Central ソリューションを管理します。 この UI を使用して次の作業を行うことができます。

- デバイスを監視します。
- デバイスを構成します。
- デバイスの問題をトラブルシューティングして修復します。
- 新しいデバイスをプロビジョニングします。

## <a name="use-the-left-navigation-menu"></a>左側のナビゲーション メニューの使用

アプリケーションのさまざまな領域には、左側のナビゲーション メニューを使用してアクセスします。

| メニュー | 説明 |
| ---- | ----------- |
| ![左側のナビゲーション メニュー](media/overview-iot-central-tour/navigationbar.png) | <ul><li>**[ホーム]** ボタンには、アプリケーションのホーム ページが表示されます。 このホーム ページは、オペレーター向けにビルダーがカスタマイズできます。</li><li>**[Device Explorer]** ボタンでは、アプリケーションに定義されているデバイス テンプレートのほか、各デバイス テンプレートに関連付けられている実デバイスとシミュレートされたデバイスが一覧表示されます。 接続されているデバイスは、オペレーターが **Device Explorer** を使用して管理できます。</li><li>**[Device Sets]\(デバイス セット\)** ボタンを使用すると、デバイス セットを表示したり作成したりすることができます。 デバイス セットは、クエリによって指定されたデバイスの論理上のコレクションとしてオペレーターが作成できます。</li><li>デバイスやデバイス セットのデバイス テレメトリから得られた分析結果は、**[分析]** ボタンで表示します。 オペレーターは、デバイス データに基づくカスタム ビューを作成することで、アプリケーションから分析情報を取得することができます。</li><li>**[Create Device Template]\(デバイス テンプレートの作成\)** ツールなど、ビルダーが使用するツールは、**[Application Builder]\(アプリケーション ビルダー\)** ボタンで表示します。</li><li>管理者がアプリケーションの設定やユーザー、役割を管理するためのアプリケーション管理ページは、**[管理]** ボタンで表示します。</li></ul> |

## <a name="search-help-and-support"></a>検索、ヘルプ、サポート

すべてのページには、次のトップ メニューが表示されます。

![ツール バー](media/overview-iot-central-tour/toolbar.png)

- デバイス テンプレートやデバイスを検索するには、**[検索]** アイコンを選択します。
- ヘルプやサポートを利用するには、**[ヘルプ]** ドロップダウンを選択するとリソースが一覧表示されます。
- チュートリアルを制御したり、UI テーマを変更したり、アプリケーションからサインアウトしたりするには、**[アカウント]** アイコンを選択します。

UI 用に淡色テーマまたは濃色テーマを選ぶことができます。

![UI に使用するテーマを選択](media/overview-iot-central-tour/themes.png)

## <a name="home-page"></a>ホーム ページ

![ホーム ページ](media/overview-iot-central-tour/homepage.png)

ホーム ページは、Azure IoT Central アプリケーションにサインインしたときに最初に表示されるページです。 ホーム ページは、ビルダーがタイルを追加することによって他のアプリケーション ユーザー向けにカスタマイズできます。 詳細については、「[Customize the Azure IoT Central operator's view (Azure IoT Central オペレーター ビューのカスタマイズ)](tutorial-customize-operator.md)」のチュートリアルを参照してください。

## <a name="device-explorer"></a>デバイス エクスプローラー

![[エクスプローラー] ページ](media/overview-iot-central-tour/explorer.png)

エクスプローラー ページには、対象となる Azure IoT Central アプリケーションの "_デバイス テンプレート_" と "_デバイス_" が表示されます。

* アプリケーションに接続できるデバイスの種類は、デバイス テンプレートによって定義されます。 詳細については、「[Define a new device type in your Azure IoT Central application (Azure IoT Central アプリケーションに新しいデバイスの種類を定義する)](tutorial-define-device-type.md)」を参照してください。
* デバイスとは、対象アプリケーションにおける実デバイスまたはシミュレートされたデバイスを表します。 詳細については、[Azure IoT Central アプリケーションへの新しいデバイスの追加](tutorial-add-device.md)に関するページを参照してください。

## <a name="device-sets"></a>デバイス セット

![[Device Sets]\(デバイス セット\) ページ](media/overview-iot-central-tour/devicesets.png)

_[device sets]\(デバイス セット\)_ ページには、ビルダーによって作成されたデバイス セットが表示されます。 デバイス セットは、関連するデバイスのコレクションです。 デバイス セットに含まれるデバイスは、ビルダーがクエリを定義することによって識別します。 デバイス セットは、対象アプリケーションにおける分析をカスタマイズするときに使用します。 詳細については、「[Use device sets in your Azure IoT Central application (Azure IoT Central アプリケーションにおけるデバイス セットの使用)](howto-use-device-sets.md)」を参照してください。

## <a name="analytics"></a>Analytics

![[分析] ページ](media/overview-iot-central-tour/analytics.png)

[分析] ページには、対象アプリケーションに接続されているデバイスの動作をわかりやすく示したグラフが表示されます。 オペレーターは、このページを使用して、接続されているデバイスの問題を監視したり調査したりします。 このページに表示されるグラフは、ビルダーが定義できます。 詳細については、[Azure IoT Central アプリケーションに使用するカスタム分析の作成](howto-create-analytics.md)に関する記事を参照してください。

## <a name="application-builder"></a>Application Builder (アプリケーション ビルダー)

![[Application Builder]\(アプリケーション ビルダー\) ページ](media/overview-iot-central-tour/applicationbuilder.png)

[Application Builder]\(アプリケーション ビルダー\) ページには、デバイス テンプレートの作成やホーム ページの構成など、ビルダーが Azure IoT Central アプリケーションを作成するために使うツールへのリンクが表示されます。 詳細については、「[Define a new device type in your Azure IoT Central application (Azure IoT Central アプリケーションに新しいデバイスの種類を定義する)](tutorial-define-device-type.md)」のチュートリアルを参照してください。

## <a name="administration"></a>[管理]

![[管理] ページ](media/overview-iot-central-tour/administration.png)

[Administration]\(管理\) ページには、アプリケーションのユーザーや役割を定義するためのツールなど、管理者が使うツールへのリンクが表示されます。 詳細については、[Azure IoT Central アプリケーションの管理](howto-administer.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

これで Azure IoT Central の概要と UI のレイアウトに関する説明は終了です。推奨される次の手順として、「[Create an Azure IoT Central application (Azure IoT Central アプリケーションの作成)](quick-deploy-iot-central.md)」クイック スタートに進みましょう。