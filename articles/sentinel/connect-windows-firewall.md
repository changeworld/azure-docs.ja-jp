---
title: Azure Sentinel プレビューで Windows ファイアウォール データを収集する | Microsoft Docs
description: Azure Sentinel で Windows ファイアウォール データを収集する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2356a7e5426037ffe9fc8b304ac113f4a3fe2a17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103351"
---
# <a name="connect-windows-firewall"></a>Windows ファイアウォールの接続

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows ファイアウォールのログが Azure Sentinel ワークスペースに接続されている場合、Windows ファイアウォール コネクタを使用すると、それらのログを簡単に接続できます。 この接続により、ダッシュボードを表示し、カスタム アラートを作成し、調査を改善することができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。  


> [!NOTE]
> 
> - データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="enable-the-connector"></a>コネクタを有効にする 

1. Azure Sentinel ポータルで **[データ収集]** を選択し、次に **[Windows firewall]\(Windows ファイアウォール\)** タイルをクリックします。 
1. ストリーミングするデータの種類を選択します。
1. **[インストール]** をクリックします。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 



## <a name="next-steps"></a>次の手順
このドキュメントでは、Windows ファイアウォールを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法を学習します。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)を開始します。

