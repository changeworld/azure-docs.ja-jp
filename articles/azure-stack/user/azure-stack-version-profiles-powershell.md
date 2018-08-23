---
title: Azure Stack での PowerShell による API バージョンのプロファイルの使用 | Microsoft Docs
description: Azure Stack での PowerShell による API バージョンのプロファイルの使用について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 8da02641cc54f9308f8e0bbb8d2b28da9a930aa2
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946515"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Azure Stack での PowerShell による API バージョンのプロファイルの使用

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

API バージョンのプロファイルは、Azure と Azure Stack の間のバージョンの違いを管理するための方法を提供します。 API バージョンのプロファイルは、特定の API バージョンを持つ一連の AzureRM PowerShell モジュールです。 各クラウド プラットフォームでは、一連の API バージョンのプロファイルがサポートされています。 たとえば、Azure Stack では **2017-03-09-profile** などの特定の日付のプロファイル バージョンがサポートされ、Azure では**最新の** API バージョンのプロファイルがサポートされています。 プロファイルをインストールすると、指定されたプロファイルに対応する AzureRM PowerShell モジュールがインストールされます。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>API バージョンのプロファイルを使用するために必要な PowerShell モジュールのインストール

PowerShell ギャラリーから入手できる **AzureRM.Bootstrapper** モジュールは、API バージョンのプロファイルを操作するために必要な PowerShell コマンドレットを提供します。 AzureRM.Bootstrapper モジュールをインストールするには、次のコマンドレットを使用します。

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>プロファイルのインストール

Azure Stack に必要な AzureRM モジュールをインストールするには、**Install-AzureRmProfile** コマンドレットを **2017-03-09-profile** という API バージョンのプロファイルと共に使用します。 Azure Stack のオペレーター モジュールは、この API のバージョン プロファイルと一緒にインストールされません。 「[PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)」の手順 3 で指定されているとおりに、個別にインストールする必要があります。

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>プロファイル内のモジュールのインストールおよびインポート

API バージョンのプロファイルに関連付けられたモジュールをインストールおよびインポートするには、**Use-AzureRmProfile** コマンドレットを使用します。 PowerShell セッションでは、API バージョンのプロファイルを 1 つしかインポートできません。 別の API バージョンのプロファイルをインポートするには、新しい PowerShell セッションを開く必要があります。 Use-AzureRMProfile コマンドレットは、次のタスクを実行します。  
1. 指定された API バージョンのプロファイルに関連付けられた PowerShell モジュールが現在のスコープにインストールされているかどうかをチェックします。  
2. まだインストールされていない場合は、それらのモジュールをダウンロードしてインストールします。   
3. それらのモジュールを現在の PowerShell セッションにインポートします。 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

選択された AzureRM モジュールを API バージョンのプロファイルからインストールおよびインポートするには、**Module** パラメーターを指定して Use-AzureRMProfile コマンドレットを実行します。

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>インストールされているプロファイルの取得

使用可能な API バージョンのプロファイルの一覧を取得するには、**Get-AzureRmProfile** コマンドレットを使用します。 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>プロファイルの更新

API バージョンのプロファイル内のモジュールを PSGallery で使用可能な最新バージョンのモジュールに更新するには、**Update-AzureRmProfile** コマンドレットを使用します。 モジュールをインポートするときの競合を回避するために、**Update-AzureRmProfile** コマンドレットは常に新しい PowerShell セッションで実行することをお勧めします。 Update-AzureRmProfile コマンドレットは、次のタスクを実行します。

1. 現在のスコープの特定の API バージョンのプロファイルに最新バージョンのモジュールがインストールされているかどうかをチェックします。  
2. まだインストールされていない場合は、インストールするよう求めるプロンプトが表示されます。  
3. 更新されたモジュールをインストールして現在の PowerShell セッションにインポートします。  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

最新の使用可能なバージョンに更新する前に、以前にインストールされたバージョンのモジュールを削除するには、Update-AzureRmProfile コマンドレットを **-RemovePreviousVersions** パラメーターと共に使用します。

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

このコマンドレットは、次のタスクを実行します。  

1. 現在のスコープの特定の API バージョンのプロファイルに最新バージョンのモジュールがインストールされているかどうかをチェックします。  
2. 現在の API バージョンのプロファイルおよび現在の PowerShell セッションから古いバージョンのモジュールを削除します。  
4. 最新バージョンをインストールするよう求めるプロンプトが表示されます。  
5. 更新されたモジュールをインストールして現在の PowerShell セッションにインポートします。  
 
## <a name="uninstall-profiles"></a>プロファイルのアンインストール

指定された API バージョンのプロファイルをアンインストールするには、**Uninstall-AzureRmProfile** コマンドレットを使用します。

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>次の手順
* [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)  
