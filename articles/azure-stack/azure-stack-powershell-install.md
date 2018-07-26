---
title: PowerShell for Azure Stack のインストール | Microsoft Docs
description: PowerShell for Azure Stack をインストールする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 09d5842f349917be0e5d94d919b0e9630347284b
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035481"
---
# <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack をインストールする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack を使用するには、Azure Stack と互換性のある Azure PowerShell モジュールが必要です。 このガイドでは、PowerShell for Azure Stack のインストールに必要な手順について説明します。 次の手順は、インターネットに接続された環境に適用されます。 インターネットから切断された環境については、このページの一番下までスクロールしてください。

この記事では、PowerShell for Azure Stack をインストールする手順について詳しく説明しています。

> [!Note]  
> 次の手順では、PowerShell 5.0 以降が必要です。 バージョンを確認するには、$PSVersionTable.PSVersion を実行して、**メジャー** バージョンを比較します。 PowerShell 5.0 を使用していない場合は、この[リンク](https://docs.microsoft.com/en-us/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)から PowerShell 5.0 にアップグレードしてください。

Azure Stack 用の PowerShell コマンドは、PowerShell ギャラリーを介してインストールされます。 PSGallery がリポジトリとして登録されているかどうかを検証するには、管理者特権の PowerShell セッションを開き、次のコマンドを実行します。

```PowerShell
#requires -Version 5
#requires -RunAsAdministrator
#requires -Module PowerShellGet

Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop 

Get-PSRepository -Name "PSGallery"
```

PSGallery リポジトリが登録されていない場合は、管理者特権の PowerShell セッションを開き、次のコマンドを実行します。

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> この手順では、インターネットへのアクセスが必要です。 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>既存のバージョンの Azure Stack PowerShell モジュールのアンインストール

必要なバージョンをインストールする前に、必ず以前にインストールした Azure Stack AzureRM PowerShell モジュールをアンインストールしてください。 アンインストールには、次の 2 つの方法の 1 つを使用できます。

 - 既存の AzureRM PowerShell モジュールをアンインストールするには、アクティブな PowerShell セッションをすべて終了し、次のコマンドを実行します。

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - `C:\Program Files\WindowsPowerShell\Modules` および `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` フォルダーから、「Azure」で始まるすべてのフォルダーを削除します。 これらのフォルダーを削除すると、既存の PowerShell モジュールがすべて削除されます。

以下のセクションでは、PowerShell for Azure Stack のインストールに必要な手順について説明します。 PowerShell は、接続されているシナリオ、部分的に接続されているシナリオ、または接続が切断されたシナリオで運用される Azure Stack にインストールできます。

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>接続されているシナリオでの Azure Stack PowerShell モジュールのインストール (インターネット接続を使用)

Azure Stack と互換性のある AzureRM モジュールは、API バージョン プロファイルを使用してインストールされます。 Azure Stack には **2017-03-09-profile** API バージョン プロファイルが必要です。このプロファイルは、AzureRM.Bootstrapper モジュールをインストールすることで利用できます。 API バージョン プロファイル、およびそれらのプロファイルによって提供されるコマンドレットについては、 「[manage API version profiles](user/azure-stack-version-profiles.md)」(API バージョン プロファイルの管理) を参照してください。 AzureRM モジュールだけでなく、Azure Stack 固有の PowerShell モジュールもインストールする必要があります。 次の PowerShell スクリプトを実行して、これらのモジュールを開発用ワークステーションにインストールします。

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

インストールを確認するには、次のコマンドを実行します。

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

インストールに成功した場合、出力に AzureRM および AzureStack モジュールが表示されます。

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>接続が切断されたシナリオまたは部分的に接続されているシナリオでの Azure Stack PowerShell モジュールのインストール (制限されたインターネット接続を使用)

接続が切断されたシナリオでは、まずインターネット接続が確立されたコンピューターに PowerShell モジュールをダウンロードしてから、モジュールを Azure Stack Development Kit に転送してインストールします。

> [!IMPORTANT]  
> Azure Stack 1.3.0 リリースの PowerShell モジュールには、重大な変更が多数追加されています。 1.2.11 バージョンからアップグレードするには、[移行ガイド](https://aka.ms/azspowershellmigration)を参照してください。

1. インターネット接続が確立されたコンピューターにサインインし、次のスクリプトを使用して、AzureRM および AzureStack パッケージをローカル コンピューターにダウンロードします。

   ```PowerShell 
  #requires -Version 5
  #requires -RunAsAdministrator
  #requires -Module PowerShellGet
  #requires -Module PackageManagement
  
  Import-Module -Name PowerShellGet -ErrorAction Stop
  Import-Module -Name PackageManagement -ErrorAction Stop

   $Path = "<Path that is used to save the packages>"

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11

   Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > 更新プログラム 1804 以降が適用された Azure Stack を実行していない場合は、**requiredversion** パラメーター値を `1.2.11` に変更します。 

2. ダウンロードしたパッケージを USB デバイスにコピーします。

3. ワークステーションにサインインし、パッケージを USB デバイスからワークステーション上の場所にコピーします。

4. ここで、その場所を既定のレポジトリとして登録し、そのレポジトリから AzureRM および AzureStack モジュールをインストールする必要があります。

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>プロキシ サーバーを使用するように PowerShell を構成する

インターネットへのアクセスにプロキシ サーバーを必要とするシナリオでは、最初に PowerShell を既存のプロキシ サーバーを使用するように構成する必要があります。

1. 管理者特権の PowerShell プロンプトを開きます。
2. 次のコマンドを実行します。

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>次の手順

 - [GitHub からの Azure Stack ツールのダウンロード](azure-stack-powershell-download.md)
 - [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)  
 - [Azure Stack オペレーターの PowerShell 環境の構成](azure-stack-powershell-configure-admin.md) 
 - [Azure Stack での API バージョン プロファイルの管理](user/azure-stack-version-profiles.md)  
