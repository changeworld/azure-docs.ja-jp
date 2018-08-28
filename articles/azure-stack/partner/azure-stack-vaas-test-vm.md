---
title: Azure Stack のサービスとしての検証でローカル エージェントとテスト イメージの仮想マシンをデプロイする | Microsoft Docs
description: Azure Stack のサービスとしての検証に使用するローカル エージェントとテスト イメージ仮想マシンをデプロイします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234155"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>ローカル エージェントおよびテスト用仮想マシンのデプロイ

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) のローカル エージェントを使ってハードウェアをチェックする方法について説明します。 検証テストを実行する前に、その対象となる Azure Stack ソリューションにローカル エージェントをデプロイしておく必要があります。

> [!Note]  
> ローカル エージェントを実行するマシンのインターネット接続が失われていないことを確認してください。 このマシンへのアクセスは、Azure Stack VaaS の使用を承認されたユーザーに限定する必要があります。

仮想マシンをテストするには:

1. ローカル エージェントをインストールする
2. システムにフォールトを挿入する
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
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. 次のコマンドを実行して、ローカル エージェントの依存関係をインストールします。

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **パラメーター**

    | パラメーター | 説明 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD テナントの全体管理者ユーザー  (例: vaasadmin@contoso.onmicrosoft.com)。 |
    | aadServiceAdminPassword | 全体管理者ユーザーのパスワード。 |
    | AadTenantId | サービスとしての検証に登録された Azure アカウントの Azure AD テナント ID。 |
    | ExternalFqdn | 完全修飾ドメイン名は、構成ファイルから取得できます。 その手順については、「[Test parameters for validation as a service Azure Stack (Azure Stack のサービスとしての検証に使用されるテスト パラメーター)](azure-stack-vaas-parameters-test.md)」を参照してください。 |
    | リージョン | Azure AD テナントのリージョン。 |

このコマンドによって、PIR (Public Image Repository) イメージ (OS VHD) がダウンロードされて、Azure Blob Storage から Azure Stack Storage にコピーされます。 

![前提条件のダウンロード](media/installingprereqs.png)

> [!Note]  
> これらのイメージをダウンロードするときに、ネットワーク速度が遅い場合は、それらを 1 つずつローカル共有にダウンロードし、パラメーター **-LocalPackagePath** *FileShareOrLocalPath* を指定してください。 PIR のダウンロードに関する詳しいガイダンスについては、「[Troubleshoot validation as a service (サービスとしての検証のトラブルシューティング)](azure-stack-vaas-troubleshoot.md)」の「[Handle slow network connectivity (低速なネットワーク接続への対応)](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)」セクションを参照してください。

## <a name="fault-injection"></a>フォールト挿入

Azure Stack は、回復性を意図して設計され、またさまざまな種類のソフトウェア障害とハードウェア障害に耐えるように設計されています。 フォールト挿入は、システムの障害発生率を上昇させます。 発生率を高めて問題を事前に明らかにすることで、システム ダウンにつながるインシデントの発生件数を減らすことができます。

システムにフォールトを挿入するには、次のコマンドを実行します。

1. 管理者特権のプロンプトで Windows PowerShell を開きます。

2. 次のコマンドを実行します。

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

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

エージェントは、その名前で一意に識別されます。 既定では、その起動元となったマシンの完全修飾ドメイン名 (FQDN) が使用されます。 うっかりクリックしてしまうことのないようウィンドウは最小化してください。フォーカスが変わると他のアクションがすべて一時停止されます。

## <a name="next-steps"></a>次の手順

- [新しい Azure Stack ソリューションの検証](azure-stack-vaas-validate-solution-new.md)  
- インターネット接続が低速または断続的である場合は、PIR イメージをダウンロードしてください。 詳細については、「[Handle slow network connectivity (低速なネットワーク接続への対応)](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)」を参照してください。
- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
