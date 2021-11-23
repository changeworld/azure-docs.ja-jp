---
title: Microsoft 365 Defender のデータを Microsoft Azure Sentinel に接続する | Microsoft Docs
description: Microsoft 365 Defender から Microsoft Azure Sentinel にインシデント、アラート、未加工のイベント データを取り込む方法について学習します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ad84db74a303277323c800edf88ff2bd9197738
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718111"
---
# <a name="connect-data-from-microsoft-365-defender-to-microsoft-sentinel"></a>Microsoft 365 Defender から Microsoft Azure Sentinel にデータを接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Microsoft 365 Defender** は、以前は **Microsoft Threat Protection** または **MTP** と呼ばれていました。
>
> **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
> **Microsoft Defender for Office 365** は、以前は **Office 365 Advanced Threat Protection** と呼ばれていました。
>
> 以前の名前がしばらくの間使用されることがあります。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>背景

インシデント統合と共に Microsoft Azure Sentinel の [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) コネクタを使用すると、すべての M365D インシデントとアラートを Microsoft Azure Sentinel にストリーミングし、両方のポータル間でインシデントの同期を維持することができます。 M365D インシデントには、すべてのアラート、エンティティ、その他の関連情報が含まれています。これらは M365D のコンポーネント サービスである **Microsoft Defender for Endpoint**、**Microsoft Defender for Identity**、**Microsoft Defender for Office 365**、**Microsoft Defender for Cloud Apps** によって強化され、アラートがグループ化されています。

このコネクタを使用すると、Microsoft Defender for Endpoint と Microsoft Defender for Office 365 から Microsoft Azure Sentinel に **詳細な捜索** イベントをストリーミングすることもできます。これにより、これらの Defender コンポーネントの詳細な捜索クエリを Microsoft Azure Sentinel にコピーすること、Sentinel アラートを Defender コンポーネントの未加工のイベント データを使用して強化し、追加の分析情報を提供すること、保有期間を長くしてログを Log Analytics に格納することができます。

インシデント統合と詳細な捜索イベントの収集の詳細については、「[Microsoft 365 Defender と Microsoft Azure Sentinel の統合](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection)」を参照してください。

> [!IMPORTANT]
>
> 現在、Microsoft 365 Defender コネクタは **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

- 「[Microsoft 365 Defender の前提条件](/microsoft-365/security/mtp/prerequisites)」で説明されているように、Microsoft 365 Defender の有効なライセンスが必要です。 

- Azure Active Directory の **全体管理者** または **セキュリティ管理者** である必要があります。

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender に接続する

1. Microsoft Azure Sentinel で、 **[データ コネクタ]** を選択し、ギャラリーから **[Microsoft 365 Defender (プレビュー)]** を選択して、 **[コネクタを開く]** ページを選択します。

1. **[インシデントとアラートの接続]** セクションの **[構成]** で、 **[インシデントとアラートの接続]** ボタンを選択します。

1. インシデントの重複を避けるために、 **[Turn off all Microsoft incident creation rules for these products]\(これらの製品のすべての Microsoft インシデント作成規則を無効にする\)** というラベルの付いたチェック ボックスをオンにすることをお勧めします。

    > [!NOTE]
    > Microsoft 365 Defender コネクタを有効にすると、すべての M365D コンポーネントのコネクタ (この記事の冒頭で説明したもの) がバックグラウンドで自動的に接続されます。 コンポーネントのコネクタのいずれかを切断するには、最初に Microsoft 365 Defender コネクタを切断する必要があります。

1. Microsoft 365 Defender インシデント データのクエリを実行するには、クエリ ウィンドウで次のステートメントを使用します。
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Microsoft Defender for Endpoint または Microsoft Defender for Office 365 から詳細な捜索イベントを収集する場合は、対応する詳細な捜索テーブルから次の種類のイベントを収集できます。

    1. 収集するイベントの種類があるテーブルのチェック ボックスをオンにします。

       # <a name="defender-for-endpoint"></a>[Defender for Endpoint](#tab/MDE)

       | テーブル名 | イベントの種類 |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | コンピューター情報 (OS 情報を含む) |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | 物理アダプター、IP および MAC アドレス、接続されているネットワークとドメインを含む、デバイスのネットワーク プロパティ |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | プロセスの作成と関連イベント |
       | **[DeviceNetworkEvents](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | ネットワーク接続と関連イベント |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | ファイルの作成、変更、およびその他のファイル システム イベント |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | レジストリ エントリの作成と変更 |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | デバイスでのサインインとその他の認証イベント |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | DLL 読み込みイベント |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Windows Defender ウイルス対策や Exploit Protection などのセキュリティ制御によってトリガーされるイベントを含む、複数のイベントの種類 |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | エンドポイントで証明書の検証イベントから取得された署名付きファイルの証明書情報 |
       |

       # <a name="defender-for-office-365"></a>[Defender for Office 365](#tab/MDO)

       | テーブル名 | イベントの種類 |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | 電子メールに添付されているファイルに関する情報 |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | Microsoft 365 メール イベント (メール配信とブロックのイベントを含む) |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | 配信後、つまり、Microsoft 365 によって電子メールが受信者のメールボックスに配信された後に発生するセキュリティ イベント |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | 電子メールの URL に関する情報 |
       |

       ---

    1. **[変更の適用]** をクリックします。

    1. Log Analytics で高度なハンティング テーブルに対してクエリを実行するには、クエリ ウィンドウの上の一覧からテーブル名を入力します。

## <a name="verify-data-ingestion"></a>データ インジェストを検証する

コネクタ ページのデータ グラフは、データを取り込んでいることを示しています。 インシデント、アラート、およびイベントごとに 1 行ずつ表示されており、イベント行は有効なすべてのテーブル全体のイベント ボリュームの集計であることがわかります。 コネクタを有効にすると、次の KQL クエリを使用して、より具体的なグラフを生成できます。

受信 Microsoft 365 Defender インシデントのグラフには、次の KQL クエリを使用します。

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

次の KQL クエリを使用して、1 つのテーブルのイベント ボリュームのグラフを生成します (*DeviceEvents* テーブルを、必要な選択したテーブルに変更します)。

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

**[次のステップ]** タブには、便利なブック、サンプル クエリ、含まれている分析ルール テンプレートがいくつか表示されます。 これらのクエリは、そこから実行するか、変更して保存することができます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Microsoft 365 Defender コネクタを使用し、Microsoft 365 Defender インシデントと、Microsoft Defender for Endpoint および Defender for Office 365 からの詳細な捜索イベント データを Microsoft Azure Sentinel に統合する方法について学習しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
