---
title: Azure portal で Azure Arc データ コントローラー リソースを表示する
description: Azure portal で Azure Arc データ コントローラー リソースを表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fe594e7459f32d0639c39b9f694f31e7816ca118
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558746"
---
# <a name="view-azure-arc-data-controller-resource-in-azure-portal"></a>Azure portal で Azure Arc データ コントローラー リソースを表示する

Azure portal で Azure Arc データ コントローラーを表示するには、kubernetes クラスターの使用状況、メトリック、ログ データの 1 つをエクスポートして Azure にアップロードする必要があります。 

## <a name="direct-connected-mode"></a>直接接続モード
Azure Arc データ コントローラーが **直接** 接続モードでデプロイされている場合、使用状況データは Azure に自動的にアップロードされ、kubernetes リソースは Azure に投影されます。

## <a name="indirect-connected-mode"></a>間接接続モード
**間接** 接続モードの場合、「[使用状況データ、メトリック、およびログを Azure にアップロードする](upload-metrics-and-logs-to-azure-monitor.md)」で説明しているとおり、使用状況データ、メトリック、またはログを最低でも 1 つエクスポートして Azure にアップロードする必要があります。 このアクションにより、Azure に適切なリソースが作成されます。

## <a name="azure-portal"></a>Azure portal

[Azure に対するメトリックまたはログの初めてのアップロード](upload-metrics-and-logs-to-azure-monitor.md)、または[使用状況のアップロード](view-billing-data-in-azure.md)が完了すると、Azure Arc データ コントローラーと、Azure Arc 対応の SQL マネージド インスタンスまたは Azure Arc 対応 Postgres Hyperscale サーバー リソースが Azure portal に表示されます。

Azure portal は、[https://portal.azure.com](https://portal.azure.com) の URL で開きます。

検索バーの名前でデータ コントローラーを検索し、それをクリックします。

