---
title: PowerShell for Azure Stack のインストール | Microsoft Docs
description: PowerShell for Azure Stack をインストールする方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/21/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: fe64011991732c7493d8efd06516efc664b674a4
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752777"
---
# <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack をインストールする

*適用先:Azure Stack 統合システムと Azure Stack Development Kit*

クラウドで動作させるには、Azure Stack と互換のある PowerShell モジュールをインストールする必要があります。 互換性は、*API プロファイル*と呼ばれる機能によって有効になります。

API のプロファイルは、Azure と Azure Stack の間のバージョンの違いを管理するための方法を提供します。 API バージョンのプロファイルは、特定の API バージョンを持つ一連の Azure Resource Manager PowerShell モジュールです。 各クラウド プラットフォームでは、一連の API バージョンのプロファイルがサポートされています。 たとえば、Azure Stack では **2018-03-01-hybrid** などの特定の日付のプロファイル バージョンがサポートされます。 プロファイルをインストールすると、指定されたプロファイルに対応する Azure Resource Manager PowerShell モジュールがインストールされます。

Azure Stack と互換のある PowerShell モジュールのインストールは、インターネットに接続されたシナリオ、部分的に接続されたシナリオ、接続が切断されたシナリオのいずれでも可能です。 この記事では、各シナリオで PowerShell for Azure Stack をインストールするための詳しい手順を説明します。

## <a name="1-verify-your-prerequisites"></a>1.前提条件を確認する

Azure Stack と PowerShell を開始する前に、次の前提条件が必要です。

- **PowerShell Version 5.0** 使用しているバージョンを確認するには、**$PSVersionTable.PSVersion** を実行して、**メジャー** バージョンを比較します。 PowerShell 5.0 を使用していない場合は、[Installing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) (Windows PowerShell のインストール) に従ってください。

  > [!Note]
  > PowerShell 5.0 には、Windows コンピューターが必要です。

- **PowerShell の実行 (管理者特権でのコマンド プロンプト)**。
  PowerShell を管理特権で実行する必要があります。

- **PowerShell ギャラリーへのアクセス** [PowerShell ギャラリー](https://www.powershellgallery.com)にアクセスする必要があります。 ギャラリーとは、PowerShell コンテンツのための中央のリポジトリです。 **PowerShellGet** モジュールには、PowerShell ギャラリーやその他のプライベート リポジトリから PowerShell アーティファクト (モジュール、DSC リソース、ロール機能、スクリプトなど) を検出、インストール、更新、発行するためのコマンドレットが含まれています。 切断されたシナリオで PowerShell を使用している場合は、インターネットに接続しているマシンからリソースを取得し、切断されたマシンがアクセスできる場所に格納する必要があります。


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.PowerShell ギャラリーにアクセスできるか検証する

PSGallery がリポジトリとして登録されているかどうかを検証します。

> [!Note]
> この手順では、インターネットへのアクセスが必要です。

管理者特権の PowerShell プロンプトを開き、次のコマンドレットを実行します。

```PowerShell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

PSGallery リポジトリが登録されていない場合は、管理者特権の PowerShell セッションを開き、次のコマンドを実行します。

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>手順 3.既存のバージョンの Azure Stack PowerShell モジュールのアンインストール

必要なバージョンをインストールする前に、必ず以前にインストールした Azure Stack AzureRM PowerShell モジュールをアンインストールしてください。 アンインストールには、次の 2 つの方法の 1 つを使用できます。

1. 既存の AzureRM PowerShell モジュールをアンインストールするには、アクティブな PowerShell セッションをすべて終了し、次のコマンドレットを実行します。

    ```PowerShell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```
    「モジュールが既に使用されています」などのエラーが発生した場合には、そのモジュールを使用している PowerShell セッションを終了してから、上のスクリプトをもう一度実行してください。

2. `C:\Program Files\WindowsPowerShell\Modules` フォルダーおよび `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` フォルダーから、`Azure` または `Azs.` で始まるすべてのフォルダーを削除します。 これらのフォルダーを削除すると、既存の PowerShell モジュールがすべて削除されます。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.接続済みの場合: インターネット接続を使用して PowerShell for Azure Stack をインストールする

Azure Stack では、Azure Stack バージョン 1808 の **2018-03-01-hybrid** API バージョン プロファイルが必要です。 このプロファイルは、**AzureRM.Bootstrapper** モジュールをインストールすると使用できるようになります。 AzureRM モジュールだけでなく、Azure Stack 固有の PowerShell モジュールもインストールする必要があります。 必要な API バージョン プロファイルおよび Azure Stack PowerShell モジュールは、実行している Azure Stack のバージョンによって異なります。

次の PowerShell スクリプトを実行して、これらのモジュールを開発用ワークステーションにインストールします。

- Azure Stack 1811 以降: 

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

    「接続済みの場合」のセクションで触れた追加のストレージ機能を利用する場合は、次のパッケージもダウンロードしてインストールしてください。

    ```PowerShell
    # Install the Azure.Storage module version 4.5.0
    Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

    # Install the AzureRm.Storage module version 5.0.4
    Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

    # Remove incompatible storage module installed by AzureRM.Storage
    Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

    # Load the modules explicitly specifying the versions
    Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
    Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
    ```

> [!Note]
> Azure PowerShell を **2017-03-09-profile** から **2018-03-01-hybrid** にアップグレードするには、「[移行ガイド](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md)」を参照してください。


- Azure Stack 1808 以降。

    ```PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

- Azure Stack 1807 以前。

  ```PowerShell
  Install-Module -Name AzureRm.BootStrapper
  Use-AzureRmProfile -Profile 2017-03-09-profile -Force
  Install-Module -Name AzureStack -RequiredVersion 1.4.0
  ```

次のコマンドを実行してインストールを確認します。

```PowerShell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

インストールに成功した場合、出力に AzureRM および AzureStack モジュールが表示されます。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.切断状態の場合: インターネット接続なしで PowerShell をインストールする

接続が切断されたシナリオでは、まずインターネット接続が確立されたコンピューターに PowerShell モジュールをダウンロードしてから、モジュールを Azure Stack Development Kit に転送してインストールします。

インターネット接続が確立されたコンピューターにサインインし、Azure Stack のバージョンに応じて次のスクリプトを使用して、Azure Resource Manager および AzureStack パッケージをダウンロードします。

  - Azure Stack 1811 以降: 

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

    「接続済みの場合」のセクションで触れた追加のストレージ機能を利用する場合は、次のパッケージもダウンロードしてインストールしてください。

    ```PowerShell
    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
    ```

  - Azure Stack 1808 以降。

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

  - Azure Stack 1807 以前。

    > [!Note]
    1.2.11 バージョンからアップグレードするには、[移行ガイド](https://aka.ms/azspowershellmigration)を参照してください。

    ```PowerShell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ```

2. ダウンロードしたパッケージを USB デバイスにコピーします。

3. ワークステーションにサインインし、パッケージを USB デバイスからワークステーション上の場所にコピーします。

4. ここで、その場所を既定のレポジトリとして登録し、そのレポジトリから AzureRM および AzureStack モジュールをインストールします。

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.プロキシ サーバーを使用するように PowerShell を構成する

インターネットへのアクセスにプロキシ サーバーを必要とするシナリオでは、最初に既存のプロキシ サーバーを使用するように PowerShell を構成する必要があります。

1. 管理者特権の PowerShell プロンプトを開きます。
2. 次のコマンドを実行します。

   ```PowerShell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>次の手順

 - [GitHub からの Azure Stack ツールのダウンロード](azure-stack-powershell-download.md)
 - [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)
 - [Azure Stack オペレーターの PowerShell 環境の構成](azure-stack-powershell-configure-admin.md)
 - [Azure Stack での API バージョン プロファイルの管理](user/azure-stack-version-profiles.md)