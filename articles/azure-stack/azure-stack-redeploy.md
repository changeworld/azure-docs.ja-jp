---
title: "Azure Stack の再デプロイ | Microsoft Docs"
description: "Azure Stack を再デプロイします。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Azure Stack の再デプロイ
Azure Stack のデプロイ中にエラーが発生した場合は、`.\InstallAzureStackpoc.ps1 -rerun` という PowerShell コマンドを使用して、セットアップをもう一度実行することができます。 このコマンドでは、以前失敗したところから Azure Stack のセットアップが再開するので、最初からやり直す必要はありません。 同じセットアップ エラーが再び発生したら、問題を対処するために、デプロイをすべてやり直す必要がある場合があります。 

Azure Stack を再デプロイするには、次に説明するように、最初から開始する必要があります。

## <a name="steps-to-redeploy-azure-stack"></a>Azure Stack を再デプロイする手順
1. 開発キット ホストで、管理者特権での PowerShell コンソールを開き、asdk-installer.ps1 スクリプトに移動して、それを実行し、**[再起動]** をクリックします。
2. ベース オペレーティング システム (**Azure Stack** でなく) を選択し、**[次へ]** をクリックします。
3. 開発キット ホストが再起動したら、以前のデプロイの一部として使用されていた CloudBuilder.vhdx ファイルを削除します。
4. [開発キットをデプロイ](azure-stack-run-powershell-script.md)します。

## <a name="next-steps"></a>次の手順
[Azure Stack への接続](azure-stack-connect-azure-stack.md)

