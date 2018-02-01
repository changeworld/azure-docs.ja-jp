---
title: "Azure Stack の開始および停止 | Microsoft Docs"
description: "Azure Stack を開始およびシャットダウンする方法を説明します。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 98bf75f5883b734c785ed1a3ed924afca1737c56
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="start-and-stop-azure-stack"></a>Azure Stack の開始および停止

*適用対象: Azure Stack 統合システム (バージョン 1712 以降)*

## <a name="stop-azure-stack"></a>Azure Stack の停止 

Azure Stack は次の手順でシャットダウンします。

1. Azure Stack ERCS VM へのネットワーク アクセスを持つマシンから特権エンドポイント セッション (PEP) を開きます。 手順については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。

2. PEP から次を実行します。

    ```powershell
      Stop-AzureStack
    ```

3. Azure Stack のすべての物理ノードの電源がオフになるまで待ちます。

> [!Note]  
> 物理ノードの電源の状態は、お使いの Azure Stack ハードウェアを提供した OEM の手順に従って確認できます。 

## <a name="start-azure-stack"></a>Azure Stack の開始 

Azure Stack は次の手順で開始します。 Azure Stack を停止した方法に関係なくこれらの手順に従います。

1. Azure Stack 環境内の各物理ノードの電源を入れます。 物理ノードの電源投入手順は、お使いの Azure Stack ハードウェアを提供した OEM の手順に従って確認します。

2. Azure Stack インフラストラクチャ サービスが開始されるまで待ちます。 Azure Stack インフラストラクチャ サービスは、開始プロセスの終了に 2 時間かかることがあります。 Azure Stack の開始状態は、[**Get-ActionStatus** コマンドレット](#get-the-startup-status-for-azure-stack)を使って確認できます。


## <a name="get-the-startup-status-for-azure-stack"></a>Azure Stack のスタートアップ状態の取得

Azure Stack のスタートアップ ルーチンのスタートアップは次の手順で取得します。

1. Azure Stack ERCS VM へのネットワーク アクセスを持つマシンから特権エンドポイント セッションを開きます。

2. PEP から次を実行します。

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure Stack のスタートアップおよびシャットダウンのトラブルシューティング

Azure Stack 環境の電源投入後 2 時間経ってもインフラストラクチャおよびテナントのサービスが 正常に開始されない場合は、次の手順を実行します。 

1. Azure Stack ERCS VM へのネットワーク アクセスを持つマシンから特権エンドポイント セッションを開きます。

2. 次のコマンドを実行します。 

    ```powershell
      Test-AzureStack
      ```

3. 出力を確認し、正常性エラーを解決します。 詳細については、[Azure Stack の検証テストの実行](azure-stack-diagnostic-test.md)に関するページをご覧ください。

4. 次のコマンドを実行します。

    ```powershell
      Start-AzureStack
    ```

5. **Start-AzureStack** の実行に失敗した場合は、Microsoft カスタマー サービス サポートにお問い合わせください。 

## <a name="next-steps"></a>次の手順 

Azure Stack 診断ツールと問題のログ記録について説明します。「Azure Stack diagnostics tools (Azure Stack の診断ツール)」をご覧ください。 Azure Stack の診断ツール。 Azure Stack の診断ツール。 Azure Stack の診断ツール。
