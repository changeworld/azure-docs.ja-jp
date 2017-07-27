---
title: "ポータルを使用した Azure Event Hubs Capture の有効化 | Microsoft Docs"
description: "Azure Portal を使用して Event Hubs Capture 機能を有効にします。"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---

<a id="enable-event-hubs-capture-using-the-azure-portal" class="xliff"></a>

# Azure Portal を使用して Event Hubs Capture を有効にする

[Azure Portal](https://portal.azure.com) を使用して、イベント ハブの作成時に Capture を構成することができます。 Capture を有効にするには、ポータルの **[イベント ハブの作成]** ブレードで **[オン]** ボタンをクリックします。 次に、ストレージ アカウントとコンテナーを構成するには、そのブレードの **[コンテナー]** セクションをクリックします。 Event Hubs Capture ではストレージによるサービス間サービス認証が使用されるため、ストレージ接続文字列を指定する必要はありません。 リソース ピッカーにより、ストレージ アカウントのリソース URI が自動的に選択されます。 Azure Resource Manager を使用している場合は、この URI を文字列として明示的に指定する必要があります。

既定の時間ウィンドウは 5 分です。 最小値は 1、最大値は 15 です。 **サイズ** ウィンドウの範囲は 10 ～ 500 MB です。

![][1]

<a id="adding-capture-to-an-existing-event-hub" class="xliff"></a>

## 既存のイベント ハブに Capture を追加する

Event Hubs 名前空間内の既存のイベント ハブで Capture を構成できます。 この機能は以前の **Messaging** または **Mixed** 型の名前空間では利用できません。 既存のイベント ハブで Capture を有効にするか、Capture の設定を変更するには、名前空間をクリックして **[要点]** ブレードを読み込み、Capture の有効化または Capture 設定の変更を行う対象のイベント ハブをクリックします。 最後に、次の図に示すように、開いているブレードの **[プロパティ]** セクションをクリックします。

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

<a id="next-steps" class="xliff"></a>

## 次のステップ

Event Hubs Capture は Azure Resource Manager テンプレートを使用して構成することもできます。 詳細については、[Azure Resource Manager テンプレートを使用した Capture の有効化](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)に関するページを参照してください。

