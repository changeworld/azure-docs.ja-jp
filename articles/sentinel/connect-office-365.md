---
title: Azure Sentinel プレビューで Office 365 データを収集する | Microsoft Docs
description: Azure Sentinel で Office 365 データを収集する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7cd90b987550dc60b30d3aa0cd1016b681eec85f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547031"
---
# <a name="collect-data-from-office-365-logs"></a>Office 365 のログからデータを収集する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) から Azure Sentinel にシングル クリックで監査ログをストリーミングできます。 複数のテナントから Azure Sentinel の 1 つのワークスペースに監査ログをストリーミングできます。 Office 365 アクティビティ ログのコネクタは、継続的なユーザー アクティビティに分析情報を提供します。 Office 365 からさまざまなユーザー、管理者、システム、ポリシーのアクションとイベントに関する情報を取得します。 Office 365 のログをAzure Sentinel に接続することで、このデータを使用して、ダッシュボードの表示、カスタムのアラートの作成、調査プロセスの向上を実現できます。


## <a name="prerequisites"></a>前提条件

- テナントのグローバル管理者またはセキュリティ管理者である
- テナントを追加するには、接続を実行するコンピューター上でポート 4433 を Web トラフィックに対して開きます。

## <a name="connect-to-office-365"></a>Office 365 への接続

1. Azure Sentinel で **[データ収集]** を選択し、**[Office 365]** タイルをクリックします。

2. まだ有効でない場合は、**[接続]** で、**[有効化]** を使用して Office 365 ソリューションを有効にします。 既に有効な場合は、既に有効になっていることが接続画面で識別されます。
1. Office 365 により、複数のテナントから Azure Sentinel にデータをストリーミングできます。 接続先のテナントごとに、**[Connect tenants to Azure Sentinel]\(Azure Sentinel にテナントを接続\)** でテナントを追加します。 
1. Active Directory Domain Services の画面が開きます。 Azure Sentinel に接続するテナントごとにグローバル管理者ユーザーによる認証を求められるので、ログを読み取るための Azure Sentinel へのアクセス許可を付与します。 
5. Stream の Office 365 アクティビティ ログで、**[選択]** をクリックし、Azure Sentinel にストリーミングするログの種類を選択します。 現時点では、Azure Sentinel は、Exchange および SharePoint をサポートしています。

4. **[変更の適用]** をクリックします。

3. Log Analytics で Office 365 のログに関連するスキーマを使用するために、**OfficeActivity** を検索します。


## <a name="next-steps"></a>次の手順
このドキュメントでは、Office 365 を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化する](quickstart-get-visibility.md)方法を確認する。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)を開始する。

