---
title: Azure IoT Central アプリケーションで請求書を管理し、無料の料金プランから切り替える | Microsoft Docs
description: 管理者として Azure IoT Central アプリケーションで請求書を管理し、無料の料金プランから標準の料金プランに切り替える方法を学習します。
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 58503784f28a3ba0a6290a2209ce9fdccc14b4e4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023771"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>IoT Central アプリケーションでの請求書の管理

この記事では、管理者として Azure IoT Central アプリケーションの管理セクションで請求書を管理する方法について説明します。 無料の料金プランから標準の料金プランにアプリケーションを切り替える方法について学習し、料金プランをアップグレードまたはダウングレードする方法についても学習します。

**[管理]** セクションにアクセスして使用するには、*管理者*ロールか、Azure IoT Central アプリケーションの請求を表示できる*カスタム ユーザー ロール*が必要です。 Azure IoT Central アプリケーションを作成したユーザーは、自動的にそのアプリケーションの**管理者**ロールに割り当てられます。

## <a name="move-from-free-to-standard-pricing-plan"></a>無料から標準に料金プランを切り替える

- 無料の料金プランが使用されているアプリケーションは 7 日間の無料期間後に失効します。 データを失わないために、失効前の任意の時点で標準の料金プランに切り替えることができます。
- 標準の料金プランが使用されているアプリケーションはデバイスごとに課金され、アプリケーションごとに最初の 2 台のデバイスが無料になります。

価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

価格セクションで、無料から標準の料金プランにアプリケーションを切り替えることができます。

このセルフ サービスのプロセスを完了するには、次の手順に従います。

1. **[管理]** セクションの **[価格]** ページに移動します。

    ![試用版の状態](media/howto-view-bill/freetrialbilling.png)

1. **[Convert to a paid plan]\(有料プランに切り替える\)** を選択します。

    ![試用版を変換する](media/howto-view-bill/convert.png)

1. 適切な Azure Active Directory を選択し、有料プランが使用されるアプリケーションに使用する Azure サブスクリプションを選択します。

1. **[変換]** を選択すると、アプリケーションで有料プランが使用され、課金が開始されます。

> [!Note]
> 既定では、 *[Standard 2]\(標準 2\)* 料金プランに切り替えられます。

## <a name="how-to-change-your-application-pricing-plan"></a>アプリケーションの料金プランを変更する方法

標準の料金プランが使用されているアプリケーションはデバイスごとに課金され、アプリケーションごとに最初の 2 台のデバイスが無料になります。

料金セクションで、Azure IoT の料金プランをいつでもアップグレードまたはダウングレードできます。

1. **[管理]** セクションの **[価格]** ページに移動します。

    ![試用版の状態](media/howto-view-bill/pricing.png)

1. **[プラン]** を選択し、 **[保存]** をクリックするとアップグレードまたはダウングレードされます。

## <a name="view-your-bill"></a>課金状況の表示

1. 適切な Azure Active Directory を選択し、有料プランが使用されるアプリケーションに使用する Azure サブスクリプションを選択します。

1. **[変換]** を選択すると、アプリケーションで有料プランが使用され、課金が開始されます。

> [!Note]
> 既定では、 *[Standard 2]\(標準 2\)* 料金プランに切り替えられます。

## <a name="next-steps"></a>次のステップ

Azure IoT Central アプリケーションでの請求書の管理方法を学習したので、次の手順として、Azure IoT Central での[アプリケーション UI のカスタマイズ](howto-customize-ui.md)について学習することをお勧めします。