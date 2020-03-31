---
title: Azure Lab Services 用の PowerShell モジュール | Microsoft Docs
description: この記事では、Azure Lab Services でのアーティファクトの管理に役立つ PowerShell モジュールについて説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608543"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell モジュール (プレビュー)
Az.LabServices は、Azure Lab Services の管理を簡略化する PowerShell モジュールです。 ラボ アカウント、ラボ、VM、イメージの作成、クエリ、更新、削除を行う構成可能な機能が用意されています。 このモジュールについて詳しくは、[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)をご覧ください。

> [!NOTE]
> このモジュールは**プレビュー**段階にあります。 

## <a name="example-command"></a>コマンドの例
PowerShell コマンドを使って、すべてのラボで実行中のすべての VM を停止する例を次に示します。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>はじめに
1. コンピューターに [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) が存在しない場合は、インストールします。 
2. [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) をお使いのコンピューターにダウンロードします。
3. モジュールをインポートします。

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. サブスクリプションに含まれるすべてのラボの一覧を表示するには、次のコマンドを実行します。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>次のステップ
[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)を参照してください。
