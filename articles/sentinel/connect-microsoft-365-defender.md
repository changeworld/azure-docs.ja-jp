---
title: Microsoft 365 Defender の生データを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft 365 Defender から Azure Sentinel に未加工のイベント データを取り込む方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: e9e892ae08c65ee5c5008bbdc1b419d8dec30183
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90931077"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender から Azure Sentinel にデータを接続する

> [!IMPORTANT]
>
> **Microsoft 365 Defender** は、以前は **Microsoft Threat Protection** または **MTP** と呼ばれていました。
>
> **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
> 以前の名前がしばらくの間使用されることがあります。

## <a name="background"></a>背景

新しい [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-threat-protection) コネクタを使用すると、Microsoft 365 Defender から Azure Sentinel に**高度なハンティング** ログ (未加工のイベント データの種類) をストリーミングできます。 

[Microsoft Defender for Endpoint (MDATP)](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) と Microsoft 365 Defender セキュリティ保護の統合により、Microsoft 365 Defender コネクタを使用して、Microsoft Defender for Endpoint の[高度なハンティング](https://aka.ms/mdatpAH) イベントを収集し、Azure Sentinel ワークスペースの新しい用途別のテーブルに直接ストリーミングできるようになりました。 これらのテーブルは Microsoft 365 Defender ポータルで使用されているのと同じスキーマに基づいて構築されており、高度なハンティング ログの完全なセットにフル アクセスして、次の操作を行うことができます。

- 既存の Microsoft Defender ATP の高度なハンティング クエリを Azure Sentinel に簡単にコピーする。

- 未加工のイベント ログを使用して、アラート、ハンティング、調査に関する追加の分析情報を取得し、Azure Sentinel の追加データ ソースのデータとイベントを関連付ける。

- Microsoft Defender for Endpoint または Microsoft 365 Defender の 30 日間の既定の保有期間を超えて、延長された保有期間でログを保存する。 これを行うには、ワークスペースの保有期間を構成するか、Log Analytics でテーブルごとの保有期間を構成します。

> [!IMPORTANT]
>
> 現在、Microsoft 365 Defender コネクタはパブリック プレビューの段階にあります。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- [Microsoft Defender for Endpoint 展開の設定](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)に関する説明に従い、Microsoft Defender for Endpoint の有効なライセンスを持っている必要があります。 

- ユーザーには、(Azure Active Directory で) テナントのグローバル管理者ロールが割り当てられている必要があります。

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender に接続する

Microsoft Defender for Endpoint がデプロイされ、データの取り込みを行っている場合は、イベント ログを Azure Sentinel に簡単にストリーミングすることができます。

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、ギャラリーから **[Microsoft 365 Defender (プレビュー)]** を選択して、 **[コネクタを開く]** ページを選択します。

1. 次の種類のイベントは、対応する高度なハンティング テーブルから収集できます。 収集するイベントの種類のチェック ボックスをオンにします。

    | イベントの種類 | テーブル名 |
    |-|-|
    | マシン情報 (OS 情報を含む) | DeviceInfo |
    | マシンのネットワーク プロパティ | DeviceNetworkInfo |
    | プロセスの作成と関連イベント | DeviceProcessEvents |
    | ネットワーク接続と関連イベント | DeviceNetworkEvents |
    | ファイルの作成、変更、およびその他のファイル システム イベント | DeviceFileEvents |
    | レジストリ エントリの作成と変更 | DeviceRegistryEvents |
    | サインインとその他の認証イベント | DeviceLogonEvents |
    | DLL 読み込みイベント | DeviceImageLoadEvents |
    | その他のイベントの種類 | DeviceEvents |
    |

1. **[変更の適用]** をクリックします。 

1. Log Analytics で高度なハンティング テーブルに対してクエリを実行するには、クエリ ウィンドウの上の一覧からテーブル名を入力します。

## <a name="verify-data-ingestion"></a>データ インジェストを検証する

コネクタ ページのデータ グラフは、データを取り込んでいることを示しています。 これには、有効になっているすべてのテーブルのイベント ボリュームを集計した単一の行が表示されていることがわかります。 コネクタを有効にしたら、次の KQL クエリを使用して、1 つのテーブルのイベント ボリュームの同様のグラフを生成できます (*DeviceEvents* テーブルを、選択した必要なテーブルに変更します)。

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

**[次の手順]** タブには、いくつかのサンプル クエリが含まれています。 これらのクエリは、そこから実行するか、変更して保存することができます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft 365 Defender コネクタを使用して、Microsoft Defender for Endpoint から Azure Sentinel に未加工のイベント データを取得する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
