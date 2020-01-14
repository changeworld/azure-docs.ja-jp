---
title: Azure Sentinel に Fortinet データを接続する | Microsoft Docs
description: Azure Sentinel に Fortinet データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 3ed83f794cdb92f709cbf5c0ea236a5a9b4c187b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610541"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Azure Sentinel に Fortinet を接続する



この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について説明します。 Fortinet データ コネクタを使用すると、Azure Sentinel に Fortinet のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Azure Sentinel で Fortinet を使用すると、組織のインターネット使用状況に関するより多くの分析情報が得られ、セキュリティ操作機能が強化されます。 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet のログを Syslog エージェントに転送する

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように Fortinet を構成します。

1. お使いの Fortinet アプライアンスで CLI を開き、次のコマンドを実行します。

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - サーバーの **IP アドレス**を、エージェントの IP アドレスに置き換えます。
    - **syslog のポート**を **514** に設定するか、エージェントに設定されているポートに設定します。
    - 初期の FortiOS のバージョンで CEF 形式を有効にするため、コマンド セット **csv disable** を実行する必要が生じる場合があります。
 
   > [!NOTE] 
   > 詳細については、[Fortinet のドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)を参照してください。 バージョンを選択してから、**Handbook** (ハンドブック) と **Log Message Reference** (ログ メッセージ リファレンス) を利用します。

1. Azure Monitor Log Analytics で Fortinet イベントに関連するスキーマを使用する場合は、`CommonSecurityLog` を検索します。

1. 手順 3 [に進みます:接続](connect-cef-verify.md)を検証します。


## <a name="next-steps"></a>次のステップ
この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブック](tutorial-monitor-your-data.md) を使用してデータを監視します。


