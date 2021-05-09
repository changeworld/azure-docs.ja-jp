---
title: Azure サブスクリプションをプログラムから作成する
description: この記事では、Azure サブスクリプションをプログラムから作成する際に利用できる選択肢について説明しています。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379011"
---
# <a name="create-azure-subscriptions-programmatically"></a>Azure サブスクリプションをプログラムから作成する

この記事では、Azure サブスクリプションをプログラムから作成する際に利用できる選択肢について説明しています。

各種の REST API を使用して、次の Azure 契約タイプのサブスクリプションを作成できます。

- Enterprise Agreement (EA)
- Microsoft 顧客契約 (MCA)
- Microsoft Partner Agreement (MPA)

プログラムから REST API を使用して、他の契約タイプのサブスクリプションを追加で作成することはできません。

サブスクリプションを作成するうえでの要件と詳細は、契約と API のバージョンによって異なります。 実際の状況に応じて、以下の記事を参照してください。

最新の API を使用した次の作業について取り上げます。

- [EA サブスクリプションを作成する](programmatically-create-subscription-enterprise-agreement.md)
- [MCA サブスクリプションを作成する](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA サブスクリプションを作成する](programmatically-create-subscription-microsoft-partner-agreement.md)

これらの記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用してサブスクリプションを作成する方法も紹介しています。 サブスクリプション作成プロセスは、ARM テンプレートで自動化できます。

[プレビュー API](programmatically-create-subscription-preview.md) を使用している場合は、引き続きそれらの API を使用してサブスクリプションを作成できます。 

## <a name="next-steps"></a>次のステップ

* サブスクリプションを作成し終えたら、他のユーザーやサービス プリンシパルにサブスクリプションを作成する権限を付与することができます。 詳細については、「[Azure Enterprise サブスクリプションを作成する権限を付与する (プレビュー)](grant-access-to-create-subscription.md)」を参照してください。
* 管理グループを使用して大量のサブスクリプションを管理する方法の詳細については、[Azure 管理グループによるリソースの整理](../../governance/management-groups/overview.md)に関する記事を参照してください。
