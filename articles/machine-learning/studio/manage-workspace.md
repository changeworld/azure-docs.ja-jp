---
title: Machine Learning Studio ワークスペースの管理 | Microsoft Docs
description: Azure Machine Learning ワークスペースへのアクセスを管理し、ML API Web サービスをデプロイおよび管理します
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c5dfd82a7bf0d1985869c8de4e3b313ef885947
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270775"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Azure Machine Learning Studio ワークスペースの管理

> [!NOTE]
> Machine Learning Web サービス ポータルでの Web サービスの管理については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。
> 
> 

Machine Learning ワークスペースは、Azure Portal で管理できます。



## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure Portal のワークスペースは次のとおりに管理します。

1. Azure サブスクリプション管理者アカウントを使用して [Azure Portal](https://portal.azure.com/) にサインインします。
2. ページ上部の検索ボックスに、"machine learning workspaces" と入力して、**[Machine Learning ワークスペース]** を選択します。
3. 管理するワークスペースをクリックします。

標準のリソース管理情報と利用可能なオプションに加え、以下のことも可能です。

- **[プロパティ]** の表示 - このページではワークスペースとリソースの情報が表示され、このワークスペースに接続されているサブスクリプションとリソース グループが変更できます。
- **[ストレージの再同期]** - ワークスペースによってキーがストレージ アカウントに維持されます。 ストレージ アカウントによってキーが変更された場合は、**[キーの再同期]** をクリックして、ワークスペースでキーを同期できます。

このワークスペースに関連付けられた Web サービスを管理するには、Machine Learning Web サービス ポータルを使用します。 詳細な情報については、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](manage-new-webservice.md)」をご覧ください。

> [!NOTE]
> New Web サービスをデプロイして管理するには、Web サービスがデプロイされているサブスクリプションで共同作成者または管理者ロールが割り当てられている必要があります。 別のユーザーを Machine Learning ワークスペースに招待したら、Web サービスをデプロイまたは管理する前に、そのユーザーを共同作成者または管理者ロールに割り当てる必要があります。 
> 
>アクセス許可の設定の詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順
* [Azure Resource Manager テンプレートを使用した Machine Learning のデプロイ](deploy-with-resource-manager-template.md)の詳細を確認する。 