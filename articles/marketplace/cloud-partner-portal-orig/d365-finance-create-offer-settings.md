---
title: オファー設定フォームに記入する方法 | Microsoft Docs
description: 新しい Dynamics 365 Business Central アプリケーションのオファー設定フォームで値を指定する必要があるさまざまなフィールドについて説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a050a8cfdd67f662786fc0b9b7ed5451c88dccb1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808439"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>プラン設定フォームに記入する方法
=======================================

プラン設定フォームは、プランの設定を指定する基本的なフォームです。
以下では必須フィールドについて説明します。

### <a name="offer-id"></a>プラン ID

`OfferId` は、発行元プロファイル内のオファーを表す一意識別子です。
この ID は製品 URL に含まれます。 小文字の英数字またはハイフン (-) のみで構成できます。 この ID はダッシュで終えることはできず、最大で 50 文字の長さにできます。 このフィールドは、プランの運用が開始されるとロックされます。

たとえば、パートナー "Contoso" がオファー ID "sample-Web App" を作成した場合、AppSource には次のように表示されます。

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>パブリッシャー ID

このドロップダウンでは、このプランを発行するためのパブリッシャー プロファイルを選択することができます。 このフィールドは、プランの運用が開始されるとロックされます。


### <a name="name"></a>Name

これはアプリ/オファーの表示名であり、Microsoft の [AppSource](https://appsource.microsoft.com/) に表示されます。 最大で 50 文字の長さにできます。

> [!NOTE]
> 短い名前は、アプリ マニフェストで指定されている発行者名と同じである必要があります。

**[保存]** をクリックしてここまでの作業を保存します。 次のステップでは、オファーの技術情報を追加します。
