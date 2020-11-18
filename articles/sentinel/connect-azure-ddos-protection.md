---
title: Azure Sentinel に Azure DDoS Protection データを接続する
description: Azure Sentinel に Azure DDoS Protection データを取り込む方法について説明します。これにより、データの表示、分析、調査を行うことができます。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: b5d98a834ed4f87fb95a13e666967a7b2d80975d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656056"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Azure DDoS Protection からデータを接続する

> [!IMPORTANT]
> Azure Sentinel の Azure DDoS Protection データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

分散型サービス拒否 (DDoS) 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。 [Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md) をアプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃に対する堅固な防御が提供されます。 Azure DDoS Protection ログを Azure Sentinel に接続すると、ブック内でのログ データの表示、ログ データを使用したカスタム アラートの作成、および調査改善に向けたログ データの組み込みを行うことができます。 

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- [Azure の DDoS 標準保護プラン](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)が構成されている必要があります。

- [Azure DDoS Standard が有効になっている仮想ネットワーク](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)が構成されている必要があります。

## <a name="connect-to-azure-ddos-protection"></a>Azure DDoS Protection に接続する
    
1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure DDoS Protection]** を選択し、プレビュー ペインで **[Open Connector Page]\(コネクタ ページを開く\)** を選択します。

1. 接続するログが存在するすべてのファイアウォールで **診断ログ** を有効にします。

    1. **[Open Diagnostics settings]\(診断設定を開く\)** リンクを選択し、リストから **[パブリック IP アドレス]** リソースを選択します。

    1. **[+ 診断設定の追加]** を選択します。

    1. **[診断設定]** 画面で次の操作を行います。
       - **[診断設定の名前]** フィールドに名前を入力します。

       - **[Log Analytics への送信]** チェック ボックスにマークを付けます。 2 つの新しいフィールドが下に表示されます。 関連する **[サブスクリプション]** と **[Log Analytics ワークスペース]** (Azure Sentinel が存在する場所) を選択します。

       - ログを取り込むルールの種類のチェック ボックスにマークを付けます。 **DDoSProtectionNotifications**、**DDoSMitigationFlowLogs**、**DDoSMitigationReports** をお勧めします。

    1. 画面の上部にある **[保存]** をクリックします。 DDoS 保護を有効にした追加のファイアウォール (パブリック IP アドレス) に対して、この手順を繰り返します。

1. Log Analytics 内の関連するスキーマを Azure DDoS Protection アラートに使用するには、**AzureDiagnostics** を検索します。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 2 週間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 2 週間が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure DDoS Protection ログを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。