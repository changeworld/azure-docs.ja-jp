---
title: Azure Data Explorer クラスター作成失敗のトラブルシューティング
description: この記事では、Azure データ エクスプローラーでクラスターを作成できない問題を解消する手順について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562413"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>トラブルシューティング:Azure Data Explorer のクラスターの作成が失敗した

めったにないことですが、もし Azure データ エクスプローラーのクラスターを作成できない場合、次の手順に従ってください。

1. 適切なアクセス許可を持っていることを確認します。 クラスターを作成するには、Azure サブスクリプションの*共同作成者*または*所有者*という役割に属している必要があります。 必要であれば、サブスクリプション管理者に問い合わせて、適切なロールに追加してもらいます。

1. Azure portal の **[クラスターの作成]** で入力したクラスター名に関連して確実に検証エラーが出ないようにします。

1. [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)を確認します。 クラスターを作成しようしているリージョンの Azure データ エクスプローラーの状態を探します。

    状態が**良好** (緑色のチェック マーク) でない場合は、状態が改善されてから、クラスターの作成をもう一度お試しください。

1. 問題解決にさらに手助けが必要な場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート リクエストを作成してください。
