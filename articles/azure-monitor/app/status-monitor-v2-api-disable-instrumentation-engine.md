---
title: Azure Application Insights エージェント Disable-InstrumentationEngine
description: Application Insights エージェント API リファレンス。 Disable-InstrumentationEngine。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998382"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights エージェント API:Disable-InstrumentationEngine

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

## <a name="parameters"></a>パラメーター 

### <a name="-verbose"></a>-Verbose
**共通パラメーター。** 詳細なログを出力する場合はこのスイッチを使用します。

## <a name="output"></a>出力


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>インストルメンテーション エンジンが正常に無効化された場合の出力例

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>次のステップ

 Application Insights エージェントをさらに活用する:
 - Application Insights エージェントのトラブルシューティングを行う場合は、[こちらのガイド](status-monitor-v2-troubleshoot.md)を使用してください。
