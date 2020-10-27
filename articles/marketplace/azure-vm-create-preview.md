---
title: Azure Marketplace での仮想マシン オファー プレビュー対象ユーザーの追加
description: Azure Marketplace で、仮想マシン オファーのプレビュー対象ユーザーを追加する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 48d111decf2145a843733b1f61b1daa87b84b85c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283395"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>仮想マシン オファーのプレビュー対象ユーザーを追加する方法

**[プレビュー]** タブを選択し、限られた **プレビュー対象ユーザー** を選択して、コマーシャル マーケットプレースの幅広い対象ユーザーに対して一般公開する前にオファーを検証します。

> [!IMPORTANT]
> **[プレビュー]** ペインでオファーを確認した後、 **[一般公開する]** を選択して、コマーシャル マーケットプレースのパブリック対象ユーザーに対してオファーを発行します。

プレビュー対象ユーザーは、Azure サブスクリプション ID の GUID と、それぞれの対するオプションの説明によって識別されます。 いずれのフィールドも顧客は表示できません。 Azure サブスクリプション ID は、Azure portal の **[サブスクリプション]** ページにあります。

少なくとも 1 つの Azure サブスクリプション ID を、個別に (最大 10 ID)、または CSV ファイルをアップロードすることによって (最大 100 ID) 追加します。 これらのサブスクリプション ID を追加することで、オファーを一般公開する前にプレビューできるユーザーを定義します。 オファーが既に公開されている場合も、オファーの変更や更新をテストするために、プレビュー対象ユーザーを定義することができます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 プレビュー対象ユーザーは、Azure Marketplace で一般公開される " *前に* "、オファーにアクセスすることができます。 プレビュー対象ユーザーは、オファーが Azure Marketplace に完全に発行された後でプライベート対象ユーザーだけが使用できるプランも含めて、すべてのプランを表示し、検証することができます。 プライベート対象ユーザーは ( **[価格と利用可否]** ペインで定義します)、特定のプランに排他的にアクセスできます。

**[下書きの保存]** を選択してから、次のセクションに進みます。

## <a name="next-steps"></a>次の手順

- [オファーのレビューと発行](review-publish-offer.md)
