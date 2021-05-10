---
title: クイックスタート - Azure Communication Services に電話番号を移行する
description: Communication Services リソースに電話番号を移行する方法について説明します。
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729738"
---
# <a name="quickstart-port-a-phone-number"></a>クイックスタート: 電話番号を移行する

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

お使いの電話番号を Azure Communication Services リソースに移行して、Azure Communication Services を始めましょう。 米国を拠点とする無料電話番号と局番が、移行の対象となります。 電話番号の種類の詳細については、[電話番号の概念に関するドキュメント](../../concepts/telephony-sms/plan-solution.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [アクティブな Communication Services リソース。](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Azure リソースの詳細を収集する

移行リクエストを開始する前に Azure portal に移動し、Communication Services リソースを選択します。 [`Overview`]\(概要\) ペインを選択した後、右上隅の [`JSON View`]\(JSON ビュー\) リンクをクリックします。

:::image type="content" source="./media/number-port/json-view.png" alt-text="[JSON View]\(JSON ビュー\) の選択を示すスクリーンショット。":::

リソースの **Azure ID** と **Immutable Resource ID** を記録します。

:::image type="content" source="./media/number-port/json-properties.png" alt-text="JSON プロパティの選択を示すスクリーンショット。":::

## <a name="initiate-the-port-request"></a>移行リクエストを開始します

米国を拠点とする無料電話番号と局番が、移行の対象となります。 次のいずれかのフォームを使用して、移行リクエストを送信します。

- 無料電話番号の場合: [無料電話番号の移行リクエスト](https://aka.ms/acs-port-form-tollfree)
- 米国を拠点とする局番: [局番の移行リクエスト](https://aka.ms/acs-port-form-geographic)

必要事項を記入したら、完成した移行リクエスト フォームを acsporting@microsoft.com 宛てに送信します。 メールの件名は必ず "ACS Port-In Request" で始めるようにしてください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * Communication Services リソースのメタデータを取得する
> * 電話番号の移行リクエストを送信する

> [!div class="nextstepaction"]
> [SMS の送信](../telephony-sms/send.md)
> [通話の概要](../voice-video-calling/getting-started-with-calling.md)
