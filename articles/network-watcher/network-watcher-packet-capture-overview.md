---
title: Azure Network Watcher のパケット キャプチャの概要 | Microsoft Docs
description: このページでは、Network Watcher のパケット キャプチャ機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 78be49efd75db5a0bbff7f01734b418c9a0a7685
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290773"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Azure Network Watcher の可変パケット キャプチャの概要

Network Watcher の可変パケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するパケット キャプチャ セッションを作成できます。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。

パケット キャプチャは、Network Watcher によりリモートで開始される、仮想マシン拡張機能です。 この機能により、目的の仮想マシンでパケット キャプチャを手動で実行する負担が軽減され、貴重な時間の節約になります。 パケット キャプチャは、ポータル、PowerShell、CLI、REST API を介してトリガーできます。 パケット キャプチャをトリガーできる方法の一例は、仮想マシンのアラートの使用です。 監視したいトラフィックを確実にキャプチャするために、キャプチャ セッション用のフィルターが用意されています。 フィルターは 5 組 (プロトコル、ローカル IP アドレス、リモート IP アドレス、ローカル ポート、リモート ポート) の情報に基づいています。 キャプチャしたデータは、ローカル ディスクまたはストレージ BLOB に格納されます。 

> [!IMPORTANT]
> パケット キャプチャには仮想マシン拡張機能 `AzureNetworkWatcherExtension` が必要です。 Windows VM への拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-windows.md)に関する記事をご覧ください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-linux.md)に関する記事をご覧ください。

キャプチャする情報を減らし、必要な情報だけを取得するために、パケット キャプチャ セッション用の次のオプションを利用できます。

**キャプチャの構成**

|プロパティ|説明|
|---|---|
|**[1 パケットあたりの最大バイト数] \(バイト単位)** | 各パケットからキャプチャされるバイト数です。空白のままの場合、すべてのバイトがキャプチャされます。 各パケットからキャプチャされるバイト数です。空白のままの場合、すべてのバイトがキャプチャされます。 IPv4 ヘッダーだけが必要な場合、ここでは 34 を指定してください |
|**[1 セッションあたりの最大バイト数] \(バイト単位)** | キャプチャされるバイト数の合計値です。この値に達すると、セッションが終了します。|
|**[制限時間 (秒)]** | パケット キャプチャ セッションに時間制限を設定します。 既定値は 18,000 秒 (5 時間) です。|

**フィルター処理 (省略可能)**

|プロパティ|説明|
|---|---|
|**プロトコル** | パケット キャプチャをフィルター処理するためのプロトコルです。 使用可能な値は、[TCP]、[UDP]、[すべて] のいずれかです。|
|**ローカル IP アドレス** | この値でパケット キャプチャをフィルター処理すると、ローカル IP アドレスがこのフィルターの値と一致するパケットだけが出力されます。|
|**ローカル ポート** | この値でパケット キャプチャをフィルター処理すると、ローカル ポートがこのフィルターの値と一致するパケットだけが出力されます。|
|**リモート IP アドレス** | この値でパケット キャプチャをフィルター処理すると、リモート IP アドレスがこのフィルターの値と一致するパケットだけが出力されます。|
|**リモート ポート** | この値でパケット キャプチャをフィルター処理すると、リモート ポートがこのフィルターの値と一致するパケットだけが出力されます。|


## <a name="considerations"></a>考慮事項
各サブスクリプションのリージョンごとに、10,000 並列パケット キャプチャ セッションという制限があります。 この制限はセッションにのみ適用され、VM のローカルまたはストレージ アカウントで保存されたパケット キャプチャ ファイルには適用されません。 制限の完全なリストについては、[Network Watcher サービスの制限に関するページ](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits)をご覧ください。 

### <a name="next-steps"></a>次のステップ

ポータルを使用する場合は [Azure Portal でのパケット キャプチャの管理](network-watcher-packet-capture-manage-portal.md)に関するページ、PowerShell を使用する場合は [PowerShell でのパケット キャプチャの管理](network-watcher-packet-capture-manage-powershell.md)に関するページを参照して、パケット キャプチャを管理する方法を確認する

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートに基づいて事前にパケット キャプチャを作成する方法を確認する

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png