---
title: クラウド ソリューション プロバイダーとして Azure の予約を表示する
description: クラウド ソリューション プロバイダーとして Azure の予約を表示する方法を説明します。
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/29/2020
ms.author: banders
ms.openlocfilehash: fde33640505c225167700215f32cb3243acc5196
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424935"
---
# <a name="view-azure-reservations-as-a-cloud-solution-provider-csp"></a>クラウド ソリューション プロバイダー (CSP) として Azure の予約を表示する

クラウド ソリューション プロバイダーは、顧客用に購入された予約にアクセスできます。 Azure portal の予約を表示するには、次の情報を使用します。

1. 全体管理者に連絡して、テナントに**管理エージェント** として追加してもらいます。
    このオプションは、パートナー センターの全体管理者が使用できます。 **[設定]** (ページの右上にある歯車記号) の **[ユーザー管理]** の下にあります。  
1. 管理エージェントの権限を取得したら、 **[Admin on Behalf Of]\(代理管理\)** リンクを使用して Azure portal にアクセスします。
1. パートナー センター > **[顧客]** > 顧客の詳細を展開 > **[Microsoft Azure の管理ポータル]** の順に移動します。
1. Azure portal で **[予約]** に移動します。

> [!NOTE]
> 顧客のテナントのゲストになると、予約を表示できなくなります。 ゲスト アクセスがある場合は、テナントから削除する必要があります。 管理エージェントの特権では、ゲスト アクセスがオーバーライドされません。

- パートナー センターでゲスト アクセスを削除するには、 **[マイ アカウント]**  >  **[[組織]](https://myaccount.microsoft.com/organizations)** に移動し、 **[組織から脱退する]** を選択します。

または、予約にアクセスできる他のユーザーに、予約注文にゲスト アカウントを追加するよう依頼します。

## <a name="next-steps"></a>次のステップ

- [Azure の予約を表示する](view-reservations.md)