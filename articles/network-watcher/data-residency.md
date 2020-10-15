---
title: Azure Network Watcher のデータ所在地 | Microsoft Docs
description: この記事では、Azure Network Watcher サービスのデータ所在地について説明します。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706824"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher のデータ所在地
Azure Network Watcher には、接続モニター (プレビュー) サービスを除き、顧客データが格納されません。


## <a name="connection-monitor-preview-data-residency"></a>接続モニター (プレビュー) のデータ所在地
接続モニター (プレビュー) サービスを使用すると、顧客データが格納されます。 このデータは、Network Watcher によって 1 つのリージョンに自動的に格納されます。 そのため、接続モニター (プレビュー) は、[トラスト センター](https://azuredatacentermap.azurewebsites.net/)で指定された要件など、リージョン内のデータ所在地の要件を自動的に満たします。

## <a name="singapore-data-residency"></a>シンガポールのデータ所在地

Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋 geo の東南アジア リージョン (シンガポール) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[トラスト センター](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) の概要をお読みください。
