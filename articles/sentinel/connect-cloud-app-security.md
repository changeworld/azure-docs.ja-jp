---
title: Azure Sentinel に Cloud App Security データを接続する | Microsoft Docs
description: Azure Sentinel に Cloud App Security データを接続する方法について説明します。
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
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588367"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security からデータを接続する 



[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) から Azure Sentinel にシングル クリックでログをストリーミングできます。 この接続により、Cloud App Security からのアラートを Azure Sentinel にストリーミングすることができます。 

## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー
- Cloud Discovery のログを Azure Sentinel にストリーミングするには、[Microsoft Cloud App Security 内で Azure Sentinel を SIEM として有効にします](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> Cloud Discovery ログの取り込みは、現在パブリック プレビューの段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security に接続する

Cloud App Security が既にある場合は、[ネットワークで有効](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)になっていることを確認します。
Cloud App Security がデプロイされていてデータを取り込んでいる場合は、アラート データを Azure Sentinel に簡単にストリーミングすることができます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Cloud App Security]** タイルをクリックし、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Azure Sentinel にストリーミングするログを選択します。 **[アラート]** と **[Cloud Discovery logs]\(Cloud Discovery ログ\)** (プレビュー) を選択できます。 

1. **[Connect]** をクリックします。

1. Cloud App Security アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Cloud App Security を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
