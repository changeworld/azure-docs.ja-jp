---
title: Azure Purview アカウントの接続 
description: Azure Purview アカウントを Synapse ワークスペースに接続します。
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918470"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>クイック スタート:  Azure Purview アカウントを Synapse ワークスペースに接続する 

> [!IMPORTANT]
> Azure Synapse Analytics と Azure Purview の統合は現在、プレビュー段階です。 Synapse で Azure Purview を試したい場合は、Microsoft の営業担当者にお問い合わせください。

このクイックスタートでは、Azure Purview アカウントを Synapse ワークスペースに登録します。 この接続により、Azure Purview の資産を検出し、Synapse 機能を使用してそれらを操作できるようになります。 

Synapse では、次のタスクを実行できます。 
- 上部にある検索ボックスを使用して、キーワードに基づいて Purview の資産を検索する 
- メタデータ、系列、注釈に基づいてデータを理解する 
- リンクされたサービスまたは統合データセットを使用してこれらのデータをワークスペースに接続する 
- Synapse Apache Spark、Synapse SQL、および Data Flow を使用してこれらのデータセットを分析する 

## <a name="prerequisites"></a>前提条件 
- [Azure Purview アカウント](../../purview/create-catalog-portal.md) 
- [Synapse ワークスペース](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Synapse ワークスペースにサインインする 

https://web.azuresynapse.net にアクセスし、自分のワークスペースにサインインします。 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure Purview アカウントを接続するためのアクセス許可 

- Azure Purview アカウントを Synapse ワークスペースに接続するには、Azure portal IAM からの Synapse ワークスペースの **共同作成者** ロールが必要です。また、その Azure Purview アカウントにアクセスできる必要があります。

## <a name="connect-an-azure-purview-account"></a>Azure Purview アカウントの接続  

- Synapse ワークスペースで、 **[管理]**  ->  **[Azure Purview]** に移動します。 **[Connect to a Purview account]\(Purview アカウントに接続する\)** を選択します。 
- **[Azure サブスクリプションから]** または **[手動で入力]** を選択できます。 **Azure サブスクリプションから**、自分がアクセスできるアカウントを選択できます。 
- 接続されると、 **[Azure Purview account]\(Azure Purview アカウント\)** タブで Purview アカウントの名前を確認できます。 
- Synapse ワークスペースの上部中央にある検索バーを使用して、データを検索できます。 

## <a name="nextsteps"></a>次のステップ 

[Azure Purview での Azure Synapse 資産の登録とスキャン](../../purview/register-scan-azure-synapse-analytics.md)

[Azure Purview を使用した Synapse でのデータの検出、接続、調査](how-to-discover-connect-analyze-azure-purview.md)   