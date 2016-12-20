---
title: "Azure Government サブスクリプション | Microsoft Docs"
description: "Azure Government でサブスクリプションを管理する方法"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 40432290f50ce08320fef1604e764b5a93a9eb0b
ms.openlocfilehash: a49552a68a0f1f077d4416cb2ba70fe2f7a632ff


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Azure Government でのサブスクリプションの管理と接続
Azure Government には、環境を管理するための独自の URL とエンドポイントが用意されています。 Azure Portal または PowerShell を介して環境を管理するには、適切な接続を使用する必要があります。 コンポーネントが展開されていれば、Azure Government 環境への接続後は、通常の操作でサービスを管理できます。

## <a name="connecting-via-the-portal"></a>Azure Portal 経由の接続
大半のユーザーは Azure Portal を主に使用して Azure Government に接続します。  接続するには、[https://portal.azure.us](https://portal.azure.us) で Azure Portal にアクセスします。  レガシ バージョンの Azure Portal には、[https://manage.windowsazure.us](https://manage.windowsazure.us) でアクセスできます。

アカウント用にサブスクリプションを作成するには、[https://account.windowsazure.us](https://account.windowsazure.us) にアクセスしてください。

## <a name="connecting-via-powershell"></a>PowerShell 経由の接続
大規模なサブスクリプションを管理する際に、Azure PowerShell のスクリプトを使用している場合でも、現時点では Azure Portal で提供されていないアクセス機能を使用している場合でも、Azure パブリックではなく Azure Government に接続する必要があります。  Azure パブリックで PowerShell を使用した経験をお持ちであれば、操作の大部分は同じです。  Azure Government で異なるのは次の点です。

* アカウントへの接続方法
* リージョン名

> [!NOTE]
> PowerShell を初めて使用する場合は、[Azure PowerShell の概要](../powershell-install-configure.md)に関するページを確認してください。
> 
> 

PowerShell を開始する際、Azure Government に接続するよう Azure PowerShell に指示するために、環境パラメーターを指定する必要があります。  パラメーターを指定することで、PowerShell は適切なエンドポイントに接続できます。  エンドポイントのコレクションはアカウントにログインする際に決定されます。  API ごとに必要な環境スイッチのバージョンが異なります。

| 接続の種類 | コマンド |
| --- | --- |
| [サービス管理](https://msdn.microsoft.com/library/dn708504.aspx)のコマンド |`Add-AzureAccount -Environment AzureUSGovernment` |
| [リソース管理](https://msdn.microsoft.com/library/mt125356.aspx)のコマンド |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) のコマンド |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory コマンド v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI コマンド ライン](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

New-azurestoragecontext でストレージ アカウントに接続して AzureUSGovernment を指定する際にも、環境スイッチを使用できます。

### <a name="determining-region"></a>リージョンの特定
接続が完了した後にも、相違点が 1 つあります。サービスの対象を設定する際に使用するリージョンです。  Azure クラウドごとに異なるリージョンが用意されています。  リージョンの詳細は、利用可能なサービスに関するページで確認できます。  通常、コマンドの Location パラメーターには、このリージョンを使用します。

その際に注意点が 1 つあります。  Azure Government のリージョンには、一般的な名前とは異なる形式を使用する必要があります。

| 一般的な名前 | コマンド |
| --- | --- |
| 米国政府バージニア州 |米国政府バージニア州 |
| 米国政府アイオワ州 |米国政府アイオワ州 |

> [!NOTE]
> Location パラメーターを使用する際に、US と Gov 間にスペースを挿入しません。
> 
> 

Azure Government で利用可能なリージョンを確認したい場合は、次のコマンドを実行し、最新のリストを印刷してください。

    Get-AzureLocation

Azure で利用可能な環境を調べる場合は、次のコマンドをしてください。

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>Visual Studio 経由の接続
開発者は、Visual Studio を使用して、ソリューションの構築時に Azure サブスクリプションを簡単に管理することができます。  現在 Visual Studio では、ユーザー インターフェイスで Azure Government への接続を構成することはできません。  

### <a name="updating-visual-studio-for-azure-government"></a>Visual Studio を Azure Government 用に更新する
Visual Studio の Azure Government への接続を有効にするには、レジストリを更新する必要があります。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGov.reg** という名前のテキスト ファイルを作成します。
3. 次のテキストをコピーして **VisualStudioForAzureGov.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 保存してからファイルをダブルクリックして実行します。  ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動して、[Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md) の使用を開始します。

> [!NOTE]
> このレジストリ キーが設定されて初めて、Azure Government サブスクリプションにアクセスできるようになります。  Visual Studio は Azure パブリックではなく Azure Government に接続しているため、以前構成したサブスクリプションは表示されますが動作しません。  変更を元に戻す手順については、次のセクションを参照してください。
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Visual Studio の Azure Government への接続を元に戻す
Visual Studio の Azure パブリックへの接続を有効にするには、Azure Government への接続を有効にしたレジストリ設定を削除する必要があります。

1. Visual Studio を閉じます。
2. **VisualStudioForAzureGov_Remove.reg** という名前のテキスト ファイルを作成します。
3. 次のテキストをコピーして **VisualStudioForAzureGov_Remove.reg** に貼り付けます。
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 保存してからファイルをダブルクリックして実行します。  ファイルをレジストリにマージするよう求めるメッセージが表示されます。
5. Visual Studio を起動します。

> [!NOTE]
> このレジストリ キーが元に戻ると、Azure Government サブスクリプションは表示されてもアクセスできなくなり、  安全に削除できます。
> 
> 

## <a name="next-steps"></a>次のステップ
詳しい情報については、次のページを参照してください。

* [GitHub の PowerShell ドキュメント](https://github.com/Azure/azure-powershell)
* [リソース管理に接続するための詳細な手順](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [MSDN の Azure PowerShell ドキュメント](https://msdn.microsoft.com/library/mt619274.aspx)

補足情報と最新情報については、[Microsoft Azure Government ブログ](https://blogs.msdn.microsoft.com/azuregov/)を講読してください。




<!--HONumber=Nov16_HO3-->


