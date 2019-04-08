---
title: Azure Sentinel プレビューで Cloud App Security データを収集する | Microsoft Docs
description: Azure Sentinel で Cloud App Security データを収集する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b0033f5f8636053f88825541b8b2cfcbf2fc9f8b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245490"
---
# <a name="collect-data-from-microsoft-cloud-app-security"></a>Microsoft Cloud App Security からデータを収集する 

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) から Azure Sentinel にシングル クリックでログをストリーミングできます。 この接続により、Cloud App Security からのアラートを Azure Sentinel にストリーミングすることができます。 

## <a name="prerequisites"></a>前提条件

- 全体管理者またはセキュリティ管理者のアクセス許可を持つユーザー

## <a name="connect-to-cloud-app-security"></a>Cloud App Security に接続する

Cloud App Security が既にある場合は、[ネットワークで有効](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)になっていることを確認します。
Cloud App Security がデプロイされていてデータを取り込んでいる場合は、アラート データを Azure Sentinel に簡単にストリーミングすることができます。


1. Azure Sentinel で **[データ収集]** を選択し、**[Cloud App Security]** タイルをクリックします。

2. **[接続]** をクリックします。

3. Cloud App Security アラートで Log Analytics 内の関連スキーマを使用するには、**SecurityAlert** を検索します。


## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Cloud App Security を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
