---
title: ローカル エージェントをデプロイする | Microsoft Docs
description: Azure Stack のサービスとしての検証に使用するローカル エージェントをデプロイします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 6a7d1f886bd3a8457b6cad2e9fea11820805e7c9
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651946"
---
# <a name="deploy-the-local-agent"></a>ローカル エージェントをデプロイする

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) のローカル エージェントを使ってハードウェアをチェックする方法について説明します。 検証テストを実行する前に、その対象となる Azure Stack ソリューションにローカル エージェントをデプロイしておく必要があります。

> [!Note]  
> ローカル エージェントを実行するマシンのインターネットへの送信アクセスが失われていないことを確認してください。 テナントに代わって VaaS を使用する権限が付与されたユーザーのみがこのマシンにアクセスできるようにする必要があります。

ローカル エージェントをデプロイするには:

1. ローカル エージェントをインストールする
2. サニティ チェックを実行する
3. ローカル エージェントを実行する

## <a name="download-and-start-the-local-agent"></a>ローカル エージェントのダウンロードと起動

データセンター内の前提条件を満たしたマシンのうち、Azure Stack システムに属してはいないものの、Azure Stack のすべてのエンドポイントにアクセスできるマシンにエージェントをダウンロードします。

### <a name="machine-prerequisites"></a>マシンの前提条件

対象のマシンが次の条件を満たしていることを確認します。

- すべての Azure Stack エンドポイントへのアクセス
- .NET 4.6 と PowerShell 5.0 のインストール
- 8 GB 以上の RAM
- 8 コア以上のプロセッサ
- 200 GB 以上のディスク領域
- インターネットへの安定したネットワーク接続

Azure Stack がテスト対象のシステムです。 対象のマシンは、Azure Stack に属していないこと、また Azure Stack クラウドでホストされていないことが必要です。

### <a name="download-and-install-the-agent"></a>エージェントのダウンロードとインストール

1. テストの実行に使用するマシンから、管理者特権でのコマンド プロンプトで Windows PowerShell を開きます。
2. 次のコマンドを実行してローカル エージェントをダウンロードします。

    ```PowerShell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. 次のコマンドを実行して、ローカル エージェントの依存関係をインストールします。

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **パラメーター**

    | パラメーター | 説明 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD テナントの全体管理者ユーザー  (例: vaasadmin@contoso.onmicrosoft.com)。 |
    | aadServiceAdminPassword | 全体管理者ユーザーのパスワード。 |
    | AadTenantId | サービスとしての検証に登録された Azure アカウントの Azure AD テナント ID。 |
    | ExternalFqdn | 完全修飾ドメイン名は、構成ファイルから取得できます。 その手順については、「[Azure Stack のサービスとしての検証の一般的なワークフロー パラメーター](azure-stack-vaas-parameters.md)」を参照してください。 |
    | リージョン | Azure AD テナントのリージョン。 |

このコマンドによって、PIR (Public Image Repository) イメージ (OS VHD) がダウンロードされて、Azure Blob Storage から Azure Stack Storage にコピーされます。

![前提条件のダウンロード](media/installingprereqs.png)

> [!Note]
> これらのイメージをダウンロードするときに、ネットワーク速度が遅い場合は、それらを 1 つずつローカル共有にダウンロードし、パラメーター **-LocalPackagePath** *FileShareOrLocalPath* を指定してください。 PIR のダウンロードに関する詳しいガイダンスについては、「[サービスとしての検証のトラブルシューティング](azure-stack-vaas-troubleshoot.md)」の「[低速なネットワーク接続の処理](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)」セクションを参照してください。

## <a name="checks-before-starting-the-tests"></a>テスト開始前のチェック

テストでは、リモート アクションを実行します。 テストを実行するマシンは、Azure Stack エンドポイントにアクセスできる必要があります。そうでないと、テストは機能しません。 VaaS のローカル エージェントを使用する場合は、エージェントを実行するマシンを使用します。 次のチェックを実行すると、マシンが Azure Stack エンドポイントにアクセスできることを確認できます。

1. ベース URI に到達できることを確認します。 CMD プロンプトまたは bash シェルを開きます。`<EXTERNALFQDN>` をお使いの環境の外部 FQDN に置き換えて、次のコマンドを実行します。

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. MAS ポータルに到達できることを確認するために、Web ブラウザーを開き、`https://adminportal.<EXTERNALFQDN>` に移動します。

3. テスト成功の作成時に指定した Azure AD サービス管理者の名前とパスワードの値を使用してサインインします。

4. 「[Azure Stack の検証テストを実行する](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)」の説明に従って、**Test-AzureStack** PowerShell コマンドレットを実行してシステムの正常性を確認します。 警告やエラーがあれば、テストを開始する前にすべて解消しておいてください。

## <a name="run-the-agent"></a>エージェントの実行

1. 管理者特権のプロンプトで Windows PowerShell を開きます。

2. 次のコマンドを実行します。

    ````PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **パラメーター**  
    | パラメーター | 説明 |
    | --- | --- |
    | VaaSUserId | VaaS ポータルにサインインするためのユーザー ID (例: UserName@Contoso.com) |
    | VaaSTenantId | サービスとしての検証に登録された Azure アカウントの Azure AD テナント ID。 |

    > [!Note]  
    > エージェントを実行するときは、タスク エンジン ホストの実行可能ファイル (**Microsoft.VaaSOnPrem.TaskEngineHost.exe**) が置かれている場所を現在の作業ディレクトリとしてください。

エラーが一切報告されなければ、ローカル エージェントは正常に実行されたことになります。 次の例に示すテキストがコンソール ウィンドウに表示されます。

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![起動されたエージェント](media/startedagent.png)

エージェントは、その名前で一意に識別されます。 既定では、その起動元となったマシンの完全修飾ドメイン名 (FQDN) が使用されます。 うっかり選択してしまうことのないようウィンドウは最小化してください。フォーカスが変わると他のアクションがすべて一時停止されます。

## <a name="next-steps"></a>次の手順

- [サービスとしての検証のトラブルシューティング](azure-stack-vaas-troubleshoot.md)
- [サービスとしての検証の主要概念](azure-stack-vaas-key-concepts.md)
- [クイック スタート: サービスとしての検証ポータルを使用して初めてのテストをスケジュール設定する](azure-stack-vaas-schedule-test-pass.md)