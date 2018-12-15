---
title: Azure Monitor で他の監視サービスからのアラートを管理する
description: Azure Monitor で Nagios、Zabbix、SCOM のアラートを管理する
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345479"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>他の監視サービスからのアラートを管理する

Nagios、Zabbix、および System Center Operations Manager からのアラートを[統合アラート エクスペリエンス](https://aka.ms/azure-alerts-overview)で確認できるようになりました。 Nagios/Zabbix サーバーまたは System Center Operations Manager と Log Analytics が統合されたことで、このようなアラートが実現しました。 

## <a name="prerequisites"></a>前提条件
Log Analytics リポジトリの種類がアラートのレコードは、統合アラート エクスペリエンスにインポートされるので、これらのレコードを収集するために必要な構成を実行する必要があります。
1. **Nagios** と **Zabbix** のアラートの場合は、Log Analytics にアラートを送信するように[これらのサーバーを構成](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)します。
1. **System Center Operations Manager** のアラートの場合は、[Log Analytics ワークスペースに Operations Manager 管理グループを接続します](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)。 System Center Operations Manager で作成されたすべてのアラートが Log Analytics にインポートされます。

## <a name="view-your-alert-instances"></a>アラート インスタンスを表示する
Log Analytics へのインポートを構成すると、[統合アラート エクスペリエンス](https://aka.ms/azure-alerts-overview)でこれらの監視サービスからのアラート インスタンスを表示することができます。 統合アラート エクスペリエンスに表示された後は、[アラート インスタンスを管理](https://aka.ms/managing-alert-instances)し、[これらのアラートに対して作成されたスマート グループを管理](https://aka.ms/managing-smart-groups)し、[アラートとスマート グループの状態を変更する](https://aka.ms/managing-alert-smart-group-states)ことができます。

> [!NOTE]
>  統合アラート エクスペリエンスの Nagios アラートはステートフルではありません。たとえば、アラートの[監視条件](https://aka.ms/azure-alerts-overview)は "発生" から "解決済み" に移行しません。 代わりに、"発生" と "解決済み" の両方が別々の警告インスタンスとして表示されます。 
