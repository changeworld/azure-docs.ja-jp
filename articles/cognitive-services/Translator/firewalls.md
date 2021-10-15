---
title: ファイアウォールの内側で翻訳する - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator による翻訳は、ドメイン名フィルタリングまたは IP フィルタリングを使用して、ファイアウォールの内側で実行できます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401866"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Translator を使用して IP ファイアウォールの内側で翻訳する方法

Translator による翻訳は、ドメイン名フィルタリングまたは IP フィルタリングを使用して、ファイアウォールの内側で実行できます。 可能であればドメイン名フィルタリングの使用をお勧めします。 それでも IP フィルタリングが必要な場合は、[サービス タグを使用して IP アドレスの詳細](../../virtual-network/service-tags-overview.md#service-tags-on-premises)を取得することをお勧めします。 Translator は、"CognitiveServicesManagement" サービス タグの下にあります。 

特定の IP フィルタリング ファイアウォールの内側から Microsoft Translator を実行することは **お勧めしません**。 この構成は将来、予告なく動作不能になる可能性があります。

2021 年 9 月 21 日の時点の Translator の地理的エンドポイントの IP アドレスは以下の通りです。

|[地理的な場所]|ベース URL (地域のエンドポイント)|IP アドレス|
|:--|:--|:--|
|United States|api-nam.cognitive.microsofttranslator.com|20.42.6.144、20.49.96.128、40.80.190.224、40.64.128.192|
|ヨーロッパ|api-eur.cognitive.microsofttranslator.com|20.50.1.16、20.38.87.129|
|アジア太平洋|api-apc.cognitive.microsofttranslator.com|40.80.170.160、20.43.132.96、20.37.196.160、20.43.66.16|
