---
title: Azure Sentinel に Office 365 のログを接続する | Microsoft Docs
description: Office 365 のログ コネクタを使用し、Exchange、Teams、SharePoint (OneDrive を含む) でユーザーと管理者の実行中のアクティビティに関する情報を取得する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/03/2021
ms.author: yelevin
ms.openlocfilehash: c451becdd6b0ae53b459d15b69f7241f84d3730d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731024"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Azure Sentinel に Office 365 のログを接続する

[Office 365](/office/) ログ コネクタは、**Exchange** と **SharePoint** (**OneDrive** を含む)、**Teams** における進行中のユーザーと管理者の活動に関する情報を Azure Sentinel に提供します。 この情報には、ファイルのダウンロード、送信されたアクセス要求、グループ イベントへの変更、メールボックスの操作、Teams イベント (チャット イベント、チーム イベント、メンバー イベント、チャンネル イベントなど) などのアクションの詳細と、アクションを実行したユーザーの詳細が含まれます。 Office 365 のログを Azure Sentinel に接続することで、ブックでのこのデータの表示と分析、カスタム アラートの作成のためのこのデータに対するクエリの実行、調査プロセスの改善のためのこのデータの組み込みが可能になり、Office 365 のセキュリティをより深く把握できるようになります。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- テナントのグローバル管理者またはセキュリティ管理者である必要があります。

- Office 365 のデプロイは、Azure Sentinel ワークスペースと同じテナントに存在する必要があります。

> [!IMPORTANT]
> - コネクタが Office 365 Management Activity API を使用してデータにアクセスできるようにするには、Office 365 のデプロイで **統合監査ログ** を有効にする必要があります。 お使いの Office 365 または Microsoft 365 のライセンスの種類によっては、既定で有効になっている場合もあれば、有効になっていない場合もあります。 お使いのライセンスの種類に応じた統合監査ログの状態を確認するには、[Office 365 セキュリティ/コンプライアンス センター](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)を参照してください。
> - Office 365 統合監査ログの現在の状態を手動で有効にしたり、無効にしたり、確認したりすることもできます。 手順については、「[Turn Office 365 audit log search on or off](/office365/securitycompliance/turn-audit-log-search-on-or-off)」 (Office 365 監査ログの検索を有効または無効にする) を参照してください。
> - 詳細については、「[Office 365 管理アクティビティ API リファレンス](/office/office-365-management-api/office-365-management-activity-api-reference)」を参照してください。


   > [!NOTE]
   > 上述したように、またコネクタ ページの **[データ型]** にもあるように、現在 Azure Sentinel Office 365 コネクタは、Microsoft Exchange、SharePoint (OneDrive を含む)、Teams からの監査ログの取り込みのみをサポートしています。 ただし、[他の Office のデータ](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)を Azure Sentinel に取り込むことに関心をお持ちの場合は、外部ソリューションがいくつか存在します。 

## <a name="enable-the-office-365-log-connector"></a>Office 365 ログ コネクタを有効にする

### <a name="instructions-tab"></a>[手順] タブ

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. **データ コネクタ** ギャラリーから、 **[Office 365]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. **[構成]** というラベルのセクションで、Azure Sentinel に接続する Office 365 アクティビティ ログのチェック ボックスをオンにし、 **[変更を適用]** をクリックします。 

   > [!NOTE]
   > 以前に複数のテナントを Azure Sentinel に接続し、その際にこれをサポートする古いバージョンの Office 365 コネクタを使用した場合は、各テナントから収集するログを表示して変更することができます。 テナントをさらに追加することはできませんが、以前に追加したテナントを削除することはできます。

### <a name="next-steps-tab"></a>[次のステップ] タブ

- **Office 365** ログ コネクタにバンドルされている推奨ブック、クエリ サンプル、分析ルール テンプレートを参照し、SharePoint、OneDrive、Exchange、Teams ログ データに関する分析情報を取得します。

- **ログ** で Office 365 ログ データに対してクエリを手動実行するには、クエリ ウィンドウの最初の行に「`OfficeActivity`」と入力します。
   - 特定のログ タイプに対してクエリをフィルター処理するには、クエリの 2 行目に「`| where OfficeWorkload == "<logtype>"`」と入力します。 *\<logtype\>* は `SharePoint`、`OneDrive`、`Exchange`、`MicrosoftTeams` のいずれかになります。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [組み込み](detect-threats-built-in.md)または[カスタム](detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。