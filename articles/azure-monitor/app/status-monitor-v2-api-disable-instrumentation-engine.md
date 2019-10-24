---
title: 'Azure Application Insights Agent API リファレンス: インストルメンテーション エンジンを無効にする |Microsoft Docs'
description: Application Insights Agent API リファレンス。 Disable-InstrumentationEngine。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: aaf493b5f2075ead62087824773f21643a1246e4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388304"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights Agent API: Disable-InstrumentationEngine

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

## <a name="description"></a>説明
いくつかのレジストリ キーを削除することで、インストルメンテーション エンジンを無効にします。
変更を有効にするために IIS を再起動してください。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。

## <a name="examples"></a>例

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>parameters 

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に無効化された場合の出力例

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>次の手順

 Application Insights Agent をさらに活用する:
 - Application Insights Agent の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行うには、このガイドを参照してください。
