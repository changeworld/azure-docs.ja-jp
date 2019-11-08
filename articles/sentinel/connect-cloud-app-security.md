---
title: Azure Sentinel に Cloud App Security データを接続する | Microsoft Docs
description: Azure Sentinel に Cloud App Security データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: 95e38cee8f6995e09dbbb2194cd5a9d0ebc301c1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498670"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security からデータを接続する 



[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) から Azure Sentinel にシングル クリックでログをストリーミングできます。 この接続により、Cloud App Security からのアラートを Azure Sentinel にストリーミングすることができます。 

## <a name="prerequisites"></a>前提条件

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー
- Cloud Discovery のログを Azure Sentinel にストリーミングするには、[Microsoft Cloud App Security 内で Azure Sentinel を SIEM として有効にします](aka.ms. https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> Cloud Discovery ログの取り込みは、現在パブリック プレビューの段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
 
## <a name="connect-to-cloud-app-security"></a>Cloud App Security に接続する

Cloud App Security が既にある場合は、[ネットワークで有効](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)になっていることを確認します。
Cloud App Security がデプロイされていてデータを取り込んでいる場合は、アラート データを Azure Sentinel に簡単にストリーミングすることができます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Cloud App Security]** タイルをクリックし、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Azure Sentinel にストリーミングするログを選択します。 **[アラート]** と **[Cloud Discovery logs]\(Cloud Discovery ログ\)** (プレビュー) を選択できます。 

1. **[接続]** をクリックします。

1. Cloud App Security アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Cloud App Security を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
