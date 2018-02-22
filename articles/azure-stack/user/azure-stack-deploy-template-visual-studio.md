---
title: "Azure Stack で Visual Studio を使用してテンプレートをデプロイする | Microsoft Docs"
description: "Azure Stack で Visual Studio を使用してテンプレートをデプロイする方法について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Visual Studio を使用した Azure Stack でのテンプレートのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

Visual Studio を使用して、Azure Resource Manager テンプレートを Azure Stack Development Kit にデプロイします。

1. Visual Studio で Azure Stack を[インストールして接続](azure-stack-install-visual-studio.md)します。
2. Visual Studio を開きます。
3. **[ファイル]****[新規作成]** の順にクリックし、**[新しいプロジェクト]** ダイアログ ボックスで **[Azure リソース グループ]** をクリックします。
4. 新しいプロジェクトの**名前**を入力し、**[OK]** をクリックします。
5. **[Azure Template の選択]** ダイアログ ボックスで、*[この場所からテンプレートを参照します]* ドロップダウンを **[Azure Stack Quickstart]** に変更します。
6. **[101-create-storage-account]** をクリックし、**[OK]** をクリックします。  
7. 新しいプロジェクトで、利用可能なテンプレートの一覧を表示するには、**[ソリューション エクスプローラー]** ウィンドウで **[テンプレート]** ノードを展開します。
8. **[ソリューション エクスプローラー]** ウィンドウでプロジェクトの名前を右クリックし、**[デプロイ]****[新しい配置]** の順にクリックします。
9. **[リソース グループに配置する]** ダイアログ ボックスの **[サブスクリプション]** ドロップダウン リストで、Microsoft Azure Stack サブスクリプションを選択します。
10. **[リソース グループ]** ボックスの一覧で、既存のリソース グループを選択するか、新しいリソース グループを作成します。
11. **[リソース グループの場所]** の一覧で場所を選択して、**[デプロイ]** をクリックします。
12. **[パラメーターの編集]** ダイアログ ボックスで、パラメーターの値 (テンプレートごとに異なる) を入力し、**[保存]** をクリックします。

## <a name="next-steps"></a>次の手順
[コマンド ラインを使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)

