---
title: Azure Sentinel に Fortinet データを接続する | Microsoft Docs
description: Fortinet アプライアンスを Azure Sentinel に接続して、ダッシュボードの表示、カスタム アラートの作成、および調査の改善を行います。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 9fb3de09195c8dd88110a7723cbe7b4d89e89309
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695118"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Azure Sentinel に Fortinet を接続する



この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について説明します。 Fortinet データ コネクタを使用すると、Azure Sentinel に Fortinet のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Azure Sentinel で Fortinet を使用すると、組織のインターネット使用状況に関するより多くの分析情報が得られ、セキュリティ操作機能が強化されます。 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet のログを Syslog エージェントに転送する

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように Fortinet を構成します。

1. お使いの Fortinet アプライアンスで CLI を開き、次のコマンドを実行します。

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - サーバーの **IP アドレス** を、エージェントの IP アドレスに置き換えます。
    - **syslog のポート** を **514** に設定するか、エージェントに設定されているポートに設定します。
    - 初期の FortiOS のバージョンで CEF 形式を有効にするため、コマンド セット **csv disable** を実行する必要が生じる場合があります。
 
   > [!NOTE] 
   > 詳細については、[Fortinet のドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)を参照してください。 バージョンを選択してから、**Handbook** (ハンドブック) と **Log Message Reference** (ログ メッセージ リファレンス) を利用します。

1. Azure Monitor Log Analytics で Fortinet イベントに関連するスキーマを使用する場合は、`CommonSecurityLog` を検索します。

1. 「[CEF 接続の検証](troubleshooting-cef-syslog.md#validate-cef-connectivity)」に進みます。


## <a name="next-steps"></a>次のステップ
この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。