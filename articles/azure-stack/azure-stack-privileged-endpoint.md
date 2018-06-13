---
title: Azure Stack での特権エンドポイントの使用 | Microsoft Docs
description: Azure Stack での特権エンドポイント (PEP) の使用方法を説明します (Azure Stack オペレーター向け)。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: 9fb928b7cb8e1a83734b64a8b9c19bc3cf3203ba
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153186"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Azure Stack での特権エンドポイントの使用

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack オペレーターは、管理ポータル、PowerShell、または Azure Resource Manager API を使用して、ほとんどの日常的な管理タスクを実行します。 ただし、あまり一般的でない一部の操作については、*特権エンドポイント* (PEP) を使用する必要があります。 この PEP は、あらかじめ構成されたリモート PowerShell コンソールであり、必要なタスクを実行するために十分な機能だけを提供します。 エンドポイントは [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) を使用して、コマンドレットの限定的なセットのみを公開します。 PEP にアクセスしてコマンドレットの限定的なセットを起動するために、低権限のアカウントが使用されます。 管理者アカウントは必要ありません。 セキュリティ強化のため、スクリプトは許可されません。

PEP を使用して、次のようなタスクを実行することができます。

- [診断ログの収集](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool)などの低レベル タスクを実行するため。
- デプロイ後の Domain Name System (DNS) フォワーダーの追加、Microsoft Graph 統合のセットアップ、Active Directory Federation Services (AD FS) 統合、証明書ローテーションなど、統合システムのための多くのデプロイ後データセンター統合タスクを実行するため。
- 統合システムの詳細なトラブルシューティングのために、サポート部門と連携して一時的な高レベル アクセスを取得するため。

PEP では、PowerShell セッションで実行するすべてのアクション (および、それに対応する出力) がログに記録されます。 これにより、完全な透明性と操作の完全な監査が提供されます。 これらのログ ファイルは将来の監査のために保持できます。

> [!NOTE]
> Azure Stack Development Kit (ASDK) では、PEP で利用可能なコマンドの一部を、開発キットのホスト上の PowerShell セッションから直接実行できます。 ただし、これは統合システム環境で特定の操作を実行するために利用可能な唯一の手段であるため、ログ収集など、PEP を使用した一部の操作をテストすることが必要な場合があります。

## <a name="access-the-privileged-endpoint"></a>特権エンドポイントへのアクセス

PEP には、PEP をホストする仮想マシン上のリモート PowerShell セッションを介してアクセスします。 ASDK では、この仮想マシンの名前は **AzS-ERCS01** です。 統合システムを使用している場合、PEP の 3 つのインスタンスがあり、それぞれ異なるホスト上の仮想マシン (*Prefix*-ERCS01、*Prefix*-ERCS02、または *Prefix*-ERCS03) 内で動作することで、回復性を確保しています。 

統合システムに対してこの手順を開始する前に、IP アドレスによって、または DNS を介して PEP にアクセスできることを確認してください。 Azure Stack の初期デプロイ後は、DNS 統合がまだセットアップされていないため、IP アドレスでしか PEP にアクセスできません。 PEP の IP アドレス を含む **AzureStackStampDeploymentInfo** という名前の JSON ファイルが、OEM ハードウェア ベンダーから提供されます。


> [!NOTE]
> セキュリティ上の理由から、PEP への接続は、ハードウェア ライフサイクル ホスト上で実行するセキュリティを強化された仮想マシンから、または [Privileged Access Workstation](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) のような専用のセキュリティで保護されたコンピューターからに限定して行う必要があります。 ハードウェア ライフサイクル ホストは、元の構成から変更しないようにするか (新しいソフトウェアのインストールするなど)、または PEP への接続に使わないようにする必要があります。

1. 信頼関係を確立します。

    - 統合システムで、管理者特権の Windows PowerShell セッションから次のコマンドを実行して、ハードウェア ライフサイクル ホストまたは Privileged Access Workstation で実行されているセキュリティ強化された仮想マシンの信頼されたホストとして PEP を追加します。

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - ADSK を実行している場合、開発キットのホストにサインインします。

2. ハードウェア ライフサイクル ホストまたは Privileged Access Workstation で実行しているセキュリティ強化された仮想マシンで、Windows PowerShell セッションを開きます。 次のコマンドを実行して、PEP をホストする仮想マシン上でリモート セッションを確立します。
 
    - 統合システム上で:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` パラメーターは、PEP をホストする仮想マシンの 1 台の IP アドレスまたは DNS 名のどちらかです。 
    - ADSK を実行している場合:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   入力を求められたら、次の資格情報を使用します。

      - **ユーザー名**: **&lt;*Azure Stack ドメイン*&gt;\cloudadmin** の形式で CloudAdmin アカウントを指定します。 (ASDK の場合、ユーザー名は **azurestack\cloudadmin** です。)
      - **パスワード**: インストール中に AzureStackAdmin ドメイン管理者アカウントのパスワードとして指定したものと同じパスワードを入力します。

    > [!NOTE]
    > ERCS エンドポイントに接続できない場合は、まだ接続を試みていない ERCS VM の IP アドレスを使用して、手順 1 と手順 2 を再試行してください。

3.  接続後、環境に応じて **[*IP アドレスまたは ERCS VM 名*]: PS>** または **[azs-ercs01]: PS>** プロンプトが変わります。 ここから `Get-Command` を実行して、利用可能なコマンドレットの一覧を表示します。

    これらのコマンドレットの多くは、統合システム環境での使用のみが意図されています (データセンター統合に関連するコマンドレットなど)。 ASDK では、次のコマンドレットが検証済みです。

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>特権エンドポイントを使用するためのヒント 

前述のとおり、PEP は、[PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) エンドポイントです。 JEA エンドポイントにより、強力なセキュリティ層が提供される一方で、スクリプトやタブ補完などの基本的な PowerShell の機能の一部が失われます。 何らかの種類のスクリプト操作を試みると、エラー **ScriptsNotAllowed** で操作は失敗します。 これは正しい動作です。

そのため、たとえば、特定のコマンドレットについてパラメーターの一覧を取得するには、次のコマンドを実行します。

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

または、[Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) コマンドレット使用して、ローカル コンピューターの現在のセッションにすべての PEP コマンドレットをインポートすることもできます。 これにより、PEP のすべてコマンドレットと関数を、タブ補完や、より一般にはスクリプトと共に、ローカル コンピューターで利用できるようになります。 

ローカル コンピューターに PEP セッションをインポートするには、次の手順を実行します。

1. 信頼関係を確立します。

    統合システムで、管理者特権の Windows PowerShell セッションから次のコマンドを実行して、ハードウェア ライフサイクル ホストまたは Privileged Access Workstation で実行されているセキュリティ強化された仮想マシンの信頼されたホストとして PEP を追加します。

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - ADSK を実行している場合、開発キットのホストにサインインします。

2. ハードウェア ライフサイクル ホストまたは Privileged Access Workstation で実行しているセキュリティ強化された仮想マシンで、Windows PowerShell セッションを開きます。 次のコマンドを実行して、PEP をホストする仮想マシン上でリモート セッションを確立します。
 
    - 統合システム上で:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` パラメーターは、PEP をホストする仮想マシンの 1 台の IP アドレスまたは DNS 名のどちらかです。 
    - ADSK を実行している場合:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   入力を求められたら、次の資格情報を使用します。

      - **ユーザー名**: **&lt;*Azure Stack ドメイン*&gt;\cloudadmin** の形式で CloudAdmin アカウントを指定します。 (ASDK の場合、ユーザー名は **azurestack\cloudadmin** です。)
      - **パスワード**: インストール中に AzureStackAdmin ドメイン管理者アカウントのパスワードとして指定したものと同じパスワードを入力します。

3. ローカル コンピューターに PEP セッションをインポートします
    ````PowerShell 
        Import-PSSession $session
    ````
4. これで、Azure Stack のセキュリティ対策を損なうことなく、PEP のすべての関数およびコマンドレットと共に、ローカルの PowerShell セッションで通常どおりにタブ補完を使用し、スクリプトを実行できるようになりました。 機能を有効にご活用ください。


## <a name="close-the-privileged-endpoint-session"></a>特権エンドポイント セッションを閉じる

 前述のとおり、PEP では、PowerShell セッションで実行するすべてのアクション (および、それに対応する出力) がログに記録されます。 `Close-PrivilegedEndpoint` コマンドレットを使ってセッションを閉じる必要があります。 このコマンドレットは、エンドポイントを正しく閉じて、ログ ファイルを保管用の外部ファイル共有に転送します。

エンドポイント セッションを閉じるには:

1. PEP からアクセス可能な外部ファイル共有を作成します。 開発キット環境では、開発キットのホスト上にファイル共有を作成することができます。
2. `Close-PrivilegedEndpoint` コマンドレットを実行します。 
3. トランスクリプト ログ ファイルを保存するパスの指定を求められます。 作成済みのファイル共有を &#92;&#92;*servername*&#92;*sharename* の形式で指定します。 パスを指定しない場合、コマンドレットは失敗し、セッションは開いたままです。 

    ![トランスクリプトの保存先パスとして指定した場所を示す Close-PrivilegedEndpoint コマンドレットの出力](media/azure-stack-privileged-endpoint/closeendpoint.png)

トランスクリプト ログ ファイルがファイル共有に正常に転送された後、それらのファイルは PEP から自動的に削除されます。 

> [!NOTE]
> コマンドレット `Exit-PSSession` または `Exit` を使用して PEP セッションを閉じた、または単に PowerShell コンソールを閉じた場合、トランスクリプト ログはファイル共有に転送されません。 それらは PEP に残ります。 次に `Close-PrivilegedEndpoint` を実行してファイル共有をインクルードしたときに、以前のセッションのトランスクリプト ログも併せて転送されます。 `Exit-PSSession` または `Exit` を使って PEP セッションを閉じないでください。代わりに、`Close-PrivilegedEndpoint` を使ってください。


## <a name="next-steps"></a>次の手順
[Azure Stack の診断ツール](azure-stack-diagnostics.md)
