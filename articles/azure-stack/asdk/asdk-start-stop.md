---
title: Azure Stack Development Kit (ASDK) の起動と終了 | Microsoft Docs
description: Azure Stack Development Kit (ASDK) を起動、シャット ダウンする方法を学習します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0b1affb708a8d481a1378fa691b6547865b0b214
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963591"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK) の起動と終了
ASDK ホスト コンピューターをただ再起動することはお勧めしません。 この記事の手順に従い、正しくシャット ダウンして ASDK サービスを再起動してください。 

## <a name="stop-azure-stack"></a>Azure Stack の停止 
次の PowerShell コマンドを使用し、Azure Stack サービス、および ASDK ホスト コンピューターを正しくシャット ダウンします。

1. ASDK ホスト コンピューターで AzureStack\CloudAdmin としてログインします。
2. PowerShell を (PowerShell ISE ではなく) 管理者として開きます。
3. 次のコマンドを実行し、特権エンドポイント (PEP) セッションを確立します。 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 次に、PEP セッションでは **Stop AzureStack**コマンドレットを使用して、Azure Stack サービスを停止し、ASDK ホスト コンピューターをシャット ダウンします。

   ```powershell
   Stop-AzureStack
   ```
5. PowerShell の出力を確認し、ASDK ホスト コンピューターがシャット ダウンする前にすべての Azure Stack サービスが正常にシャット ダウンされたことを確認します。 シャットダウンのプロセスには数分かかります。

## <a name="start-azure-stack"></a>Azure Stack の開始 
ASDK サービスは、ホスト コンピューターが起動したときに自動的に開始する必要があります。 ただし、ASDK インフラストラクチャ サービスの起動時間は、ASDK ホスト コンピューターのハードウェア構成のパフォーマンスによって変わります。 すべてのサービスが正常に再起動するまでに、場合によっては数時間がかかることもあります。

ASDK のシャット ダウン方法に関係なく、次のステップを実行して、ホスト コンピューターの電源投入後に、すべての Azure Stack サービスが起動し完全に動作していることを確認する必要があります。 

1. ASDK ホスト コンピューターの電源を投入します。 
2. ASDK ホスト コンピューターで AzureStack\CloudAdmin としてログインします。
3. PowerShell を (PowerShell ISE ではなく) 管理者として開きます。
4. 次のコマンドを実行し、特権エンドポイント (PEP) セッションを確立します。

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 次に、PEP セッションでは、次のコマンドを実行し Azure Stack サービスのスタートアップの状態を確認します。

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. 出力を確認して Azure Stack サービスが正常に再起動したことを確認してください。

Microsoft Azure Stack サービスの正しいシャットダウンと再起動について、推奨される手順の詳細を見るには、[Azure Stack の開始および停止](../azure-stack-start-and-stop.md)をご覧ください。 

## <a name="troubleshoot-startup-and-shutdown"></a>スタートアップおよびシャットダウンのトラブルシューティング 
ASDK ホスト コンピューターの電源投入後 2 時間以内に Azure Stack サービスが正常に起動しない場合は、次の手順を実行します。

1. ASDK ホスト コンピューターで AzureStack\CloudAdmin としてログインします。
2. PowerShell を (PowerShell ISE ではなく) 管理者として開きます。
3. 次のコマンドを実行し、特権エンドポイント (PEP) セッションを確立します。

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 次に、PEP セッションでは、次のコマンドを実行し Azure Stack サービスのスタートアップの状態を確認します。

   ```powershell
   Test-AzureStack
   ```
5. 出力を確認しエラーを解決します。 詳細については、[Azure Stack の検証テストの実行](../azure-stack-diagnostic-test.md)に関するページをご覧ください。
6. PEP セッション内で **Start-AzureStack**コマンドレットを実行してから Azure Stack サービスを再起動します。

   ```powershell
   Start-AzureStack
   ```

**Start-AzureStack**の実行が失敗した場合、[Azure Stack サポート フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack)で ASDK トラブルシューティングのサポートを得てください。 

## <a name="next-steps"></a>次の手順 
Azure Stack 診断ツールと問題のログ記録については、「[Azure Stack の診断ツール](../azure-stack-diagnostics.md)」をご覧ください。
