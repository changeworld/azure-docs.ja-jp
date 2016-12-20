---
title: "Web アプリ リソースを他のリソース グループに移動する"
description: "Web アプリおよびアプリ サービスを一つのリソース グループから他のリソース グループに移動するシナリオについて記述しています。"
services: app-service
documentationcenter: 
author: ZainRizvi
manager: wpickett
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2016
ms.author: zarizvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08b8953b91a87a131f70309e41be3a9ce0123299


---
# <a name="supported-move-configurations"></a>サポートされている移動の構成
Azure Web アプリ リソースを [ARM Move Resources Api](../resource-group-move-resources.md)を使用して移動できます。

現在、Azure の Web アプリでは次の移動のシナリオがサポートされています。

* リソース グループ (Web アプリ、アプリのサービス プラン、および証明書) の内容全体を別のリソース グループに移動します。 
  * 注: このシナリオでは、変換先のリソース グループにすべてのMicrosoft.Web リソースを含めることはできません。
* 個々の Web アプリをそれぞれの異なるリソース グループに移動しますが、この時 Web アプリはまだ現行のアプリのサービス プランでホストされています (アプリのサービス プランは古いリソース グループにとどまっています)。




<!--HONumber=Nov16_HO3-->


