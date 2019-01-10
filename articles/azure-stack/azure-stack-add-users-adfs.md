---
title: Azure Stack ADFS のユーザーを追加する | Microsoft Docs
description: Azure Stack の ADFS デプロイ用にユーザーを追加する方法について説明します
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120814"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>AD FS の Azure Stack ユーザーを追加する
**[Active Directory ユーザーとコンピューター]** スナップインを使用して、AD FS を ID プロバイダーとして利用している Azure Stack 環境にユーザーを追加できます。

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory ユーザーを追加する
> [!TIP]
> この例では、既定の azurestack.local ASDK アクティブ ディレクトリを使用します。 

1.  Windows 管理ツールにアクセスできるアカウントを使用してコンピューターにログインし、新しい Microsoft 管理コンソール (MMC) を開きます。
2.  **[ファイル]、[スナップインの追加と削除]** の順にクリックします。
3.  **[Active Directory ユーザーとコンピューター]** > **[AzureStack.local]** > **[Users]** を選択します。
4.  **[操作]** > **[新規作成]** > **[ユーザー]** をクリックします。
5.  [新しいオブジェクト - ユーザー] ウィンドウで、パスワードと、パスワードの確認入力を指定します。
6.  **[次へ]** をクリックして値を確定し、[完了] をクリックしてユーザーを作成します。


## <a name="next-steps"></a>次の手順
[サービス プリンシパルの作成](azure-stack-create-service-principals.md)