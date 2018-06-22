---
title: Azure Stack をデプロイする - PowerShell | Microsoft Docs
description: この記事では、PowerShell を使用してコマンド ラインから ASDK をインストールします。
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
ms.custom: ''
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f0d7daa479f6e6ea345e010962488c1ecad5b7e2
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849959"
---
# <a name="deploy-the-asdk-from-the-command-line"></a>ASDK をコマンド ラインからデプロイする
ASDK は、Azure Stack の機能やサービスを評価したり実演したりするためにデプロイできるテスト/開発環境です。 この環境を準備するには、環境ハードウェアを用意し、いくつかのスクリプトを実行する必要があります (これには数時間かかります)。 その後、管理者ポータルとユーザー ポータルにサインインし、Azure Stack の使用を開始することができます。

## <a name="prerequisites"></a>前提条件 
開発キットのホスト コンピューターを準備します。 ハードウェア、ソフトウェア、およびネットワークを計画します。 開発キットをホストするコンピューター (開発キット ホスト) は、ハードウェア、ソフトウェア、ネットワークの要件を満たす必要があります。 Azure Active Directory (Azure AD) と Active Directory フェデレーション サービス (AD FS) のどちらを使用するかを選択します。 インストール プロセスが滞りなく進行するように、デプロイの開始前に以上の前提条件を必ず満たしてください。 

ASDK をデプロイする前に、計画している開発キット ホスト コンピューターのハードウェア、オペレーティング システム、アカウント、およびネットワーク構成が、ASDK をインストールするための最小要件を満たしていることを確認してください。

**[ASDK デプロイ計画に関する考慮事項を確認する](asdk-deploy-considerations.md)**

> [!TIP]
> オペレーティング システムをインストールした後、ハードウェアがすべての要件を満たしていることを確認するには、[Azure Stack デプロイ要件チェック ツール](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)を使用できます。

## <a name="download-and-extract-the-deployment-package"></a>デプロイ パッケージをダウンロードし、展開する
開発キット ホスト コンピューターが ASDK の基本的なインストール要件を満たしていることを確認したら、次の手順は ASDK デプロイ パッケージをダウンロードして展開することです。 デプロイ パッケージには、Cloudbuilder.vhdx ファイルが含まれています。このファイルは、起動可能なオペレーティング システムと Azure Stack インストール ファイルを含む仮想ハード ドライブです。

開発キット ホストや別のコンピューターに、デプロイ パッケージをダウンロードできます。 展開されたデプロイ ファイルは 60 GB の空きディスク容量を占めます。そのため、別のコンピューターを利用することで、開発キット ホストのハードウェア要件を緩和できます。

**[Azure Stack Development Kit (ASDK) をダウンロードして展開する](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>開発キットのホスト コンピューターを準備する
ASDK をホスト コンピューターにインストールする前に、環境を準備し、VHD から起動するようにシステムを構成する必要があります。 この手順の後、開発キット ホストは Cloudbuilder.vhdx (起動可能なオペレーティング システムと Azure Stack インストール ファイルを含む仮想ハード ドライブ) から起動します。

PowerShell を使用して、CloudBuilder.vhdx から起動するように ASDK ホスト コンピューターを構成します。 これらのコマンドは、ダウンロードして展開した Azure Stack 仮想ハード ディスク (CloudBuilder.vhdx) から起動するように ASDK ホスト コンピューターを構成します。 以下の手順の完了後、ASDK ホスト コンピューターを再起動します。

CloudBuilder.vhdx から起動するように ASDK ホスト コンピューターを構成するには、次の手順に従います。

  1. 管理者としてコマンド プロンプトを開きます。
  2. `bcdedit /copy {current} /d "Azure Stack"` を実行します。
  3. 返された CLSID 値を、必要な {} を含めてコピー (Ctrl + C) します。 この値は {CLSID} と呼ばれ、後の手順で貼り付け (Ctrl + V または右クリック) を行う必要があります。
  4. `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` を実行します。 
  5. `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` を実行します。 
  6. `bcdedit /set {CLSID} detecthal on` を実行します。 
  7. `bcdedit /default {CLSID}` を実行します。
  8. 起動設定を確認するには、`bcdedit` を実行します。 
  9. CloudBuilder.vhdx ファイルが C:\ ドライブのルート (C:\CloudBuilder.vhdx) に移動してあることを確認し、開発キットのホスト コンピューターを再起動します。 ASDK ホスト コンピューターが再起動されるときに、ASDK のデプロイを開始するには、CloudBuilder.vhdx 仮想マシンのハード ドライブから起動する必要があります。 

> [!IMPORTANT]
> 再起動する前に、開発キット ホスト コンピューターへの直接の物理的アクセスまたは KVM アクセスがあることを確認してください。 VM が初めて起動するときに、Windows Server セットアップを完了するように求めるメッセージが表示されます。 開発キット ホスト コンピューターにログインするときに使用したのと同じ管理者資格情報を入力します。 

### <a name="prepare-the-development-kit-host-using-powershell"></a>PowerShell を使用して開発キットのホストを準備する 
開発キット ホスト コンピューターが CloudBuilder.vhdx イメージから正常に起動したら、開発キット ホスト コンピューターにログインするときに使用した (およびホスト コンピューターが VHD から起動したときに Windows Server セットアップを完了するために指定した) のと同じローカル管理者資格情報でログインします。 

> [!NOTE]
> 必要に応じて、ASDK のインストール "*前*" に [Azure Stack のテレメトリ設定](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)を構成することもできます。

管理者特権の PowerShell コンソールを開いてこのセクションのコマンドを実行し、ASDK を開発キットのホストにデプロイします。

> [!IMPORTANT] 
> ASDK のインストールでは、ネットワーク用に 1 つだけの NIC (ネットワーク インターフェイス カード) がサポートされています。 NIC が複数ある場合は、デプロイ スクリプトを実行する前に、1 つのみが有効になっている (他はすべて無効になっている) ことを確認します。

Azure Stack と、ID プロバイダーとして Azure AD または Windows Server AD FS をデプロイできます。 どちらを使用しても、Azure Stack、リソース プロバイダー、およびその他のアプリケーションは同様に動作します。

> [!TIP]
> セットアップ パラメーター (InstallAzureStackPOC.ps1 のオプションのパラメーターと以下の例を参照) を指定しない場合、必須パラメーターの入力を求めるメッセージが表示されます。

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD を使用して Azure Stack をデプロイする 
**Azure AD を ID プロバイダーとして使用**して Azure Stack をデプロイするには、インターネットに直接または透過プロキシ経由で接続する必要があります。 

Azure AD を使用して開発キットをデプロイするには、次の PowerShell コマンドを実行します。

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

ASDK のインストールを開始して数分後に、Azure AD 資格情報の入力を求めるメッセージが表示されます。 Azure AD テナントのグローバル管理者の資格情報を指定する必要があります。 

### <a name="deploy-azure-stack-using-ad-fs"></a>AD FS を使用して Azure Stack をデプロイする 
**AD FS を ID プロバイダーとして使用**して開発キットをデプロイするには、次の PowerShell コマンドを実行します (-UseADFS パラメーターを追加するだけです)。 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

AD FS デプロイでは、既定のスタンプ ディレクトリ サービスが ID プロバイダーとして使用されます。 サインインに使用する既定のアカウントは azurestackadmin@azurestack.local で、パスワードは PowerShell のセットアップ コマンドの一部として指定したものに設定されます。

デプロイ処理には数時間かかる場合があります。その間に、システムは自動的に 1 回再起動されます。 デプロイが成功した場合、PowerShell コンソールには、**[COMPLETE: Action ‘Deployment’]\(完了: アクション ‘デプロイ’\)** と表示されます。 デプロイが失敗した場合は、-rerun パラメーターを使用してスクリプトを再実行してみることができます。 または、最初から [ASDK を再デプロイ](asdk-redeploy.md)することもできます。

> [!IMPORTANT]
> ASDK ホストの再起動後にデプロイの進行状況を監視する場合は、AzureStack\AzureStackAdmin としてサインインする必要があります。 ホスト コンピューターが再起動され、azurestack.local ドメインに参加した後、ローカル管理者としてサインインすると、デプロイの進行状況は表示されません。 デプロイを再実行せず、代わりに azurestack としてサインインし、デプロイが実行中であることを確認してください。


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD のデプロイ スクリプトの例
Azure AD のデプロイ全体をスクリプト化することができます。 オプションのパラメーターを含む、コメント付きの例をいくつか示します。

Azure AD ID が **1 つ**の Azure AD ディレクトリのみに関連付けられている場合:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Azure AD ID が **2 つ以上**の Azure AD ディレクトリに関連付けられている場合:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

自分の環境で DHCP を有効にしていない場合は、上のいずれかのオプションに以下の追加パラメーターを含める必要があります (使用例を示しています)。 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 の省略可能なパラメーター
|パラメーター|必須/省略可能|説明|
|-----|-----|-----|
|AdminPassword|必須|開発キットのデプロイの一環として作成されたすべての仮想マシンのローカル管理者アカウントと他のすべてのユーザー アカウントを設定します。 このパスワードは、ホスト上の現在のローカル管理者パスワードと一致する必要があります。|
|InfraAzureDirectoryTenantName|必須|テナント ディレクトリを設定します。 このパラメーターを使用して、AAD アカウントに複数のディレクトリを管理するアクセス許可がある特定のディレクトリを指定します。 AAD ディレクトリ テナントのフル ネーム (.onmicrosoft.com 形式) または Azure AD 確認済みカスタム ドメイン名。|
|TimeServer|必須|このパラメーターを使用して、特定のタイム サーバーを指定します。 このパラメーターは、有効なタイム サーバーの IP アドレスとして指定する必要があります。 サーバー名はサポートされていません。|
|InfraAzureDirectoryTenantAdminCredential|省略可能|Azure Active Directory のユーザー名とパスワードを設定します。 これらの Azure 資格情報は、組織 ID である必要があります。|
|InfraAzureEnvironment|省略可能|この Azure Stack デプロイを登録する Azure 環境を選択します。 オプションには、パブリック Azure、Azure China、Azure US Government などがあります。|
|DNSForwarder|省略可能|DNS サーバーが Azure Stack のデプロイの一部として作成されます。 ソリューション内のコンピューターにスタンプ外の名前解決を許可するには、既存のインフラストラクチャの DNS サーバーを提供します。 スタンプ内の DNS サーバーが、このサーバーに不明な名前解決の要求を送信します。|
|NatIPv4Address|DHCP NAT のサポートには必須|MAS-BGPNAT01 の静的 IP アドレスを設定します。 このパラメーターは、DHCP がインターネットにアクセスするための有効な IP アドレスを割り当てることができない場合にのみ使用します。|
|NatIPv4Subnet|DHCP NAT のサポートには必須|NAT 経由の DHCP サポートのために使用される IP サブネット プレフィックスです。 このパラメーターは、DHCP がインターネットにアクセスするための有効な IP アドレスを割り当てることができない場合にのみ使用します。|
|PublicVlanId|省略可能|VLAN ID を設定します。 このパラメーターは、ホストと MAS-BGPNAT01 が物理ネットワーク (およびインターネット) にアクセスするために VLAN ID を構成する必要がある場合にのみ使用します。 例: .\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|Rerun|省略可能|このフラグを使用して、デプロイを再実行します。 以前のすべての入力が使用されます。 以前に指定したデータの再入力は、サポートされていません。いくつかの一意の値が生成され、デプロイに使用されるためです。|


## <a name="perform-post-deployment-configurations"></a>デプロイ後の構成を実行する
ASDK をインストールした後、推奨されるインストール後の確認と構成変更がいくつかあります。 test-AzureStack コマンドレットを使用してインストールが正常に行われたことを検証し、Azure Stack PowerShell および GitHub ツールをインストールすることができます。 

評価期間が終了する前に開発キット ホストのパスワードが期限切れにならないように、パスワードの有効期限のポリシーをリセットする必要もあります。

> [!NOTE]
> 必要に応じて、ASDK のインストール "*後*" に [Azure Stack のテレメトリ設定](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)を構成することもできます。

**[ASDK デプロイ後のタスク](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Azure に登録する
Azure Stack に [Azure マーケットプレース項目をダウンロード](asdk-marketplace-item.md)できるように、Azure Stack を Azure に登録する必要があります。

**[Azure Stack を Azure に登録する](asdk-register.md)**

## <a name="next-steps"></a>次の手順
お疲れさまでした。 これらの手順を完了すると、[管理者](https://adminportal.local.azurestack.external)ポータルと[ユーザー](https://portal.local.azurestack.external) ポータルの両方を開発キット環境で利用できるようになります。 

[ASDK インストール後の構成タスク](asdk-post-deploy.md)

