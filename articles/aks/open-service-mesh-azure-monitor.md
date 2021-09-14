---
title: Azure Monitor と Application Insights の使用
description: Azure Monitor と Application Insights を Open Service Mesh と一緒に使用する方法
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: e3a180e0dbe39bd078868d879d8eb78dc5136aec
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440071"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor と Application Insights を使用した Open Service Mesh (OSM) の監視

Azure Monitor と Azure Application Insights のどちらも、アプリケーションとサービスの可用性とパフォーマンスの最大化を支援します。 これは、クラウドおよびオンプレミス環境のテレメトリを収集、分析し、対応する包括的なソリューションを提供することで実現します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM AKS アドオンは、これらの Azure サービスの両方に緊密に統合され、OSM メトリックによって提供される重要な KPI を表示して対応するためにシームレスな Azure のエクスペリエンスが提供されます。 OSM AKS アドオン用にこれらのサービスを有効にして構成する方法の詳細については、[OSM の Azure Monitor](https://aka.ms/azmon/osmpreview) に関するページを参照してください。