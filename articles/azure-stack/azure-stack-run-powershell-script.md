---
title: "Azure Stack Development Kit のデプロイ | Microsoft Docs"
description: "Azure Stack Development Kit を準備し、PowerShell スクリプトを実行してそれをデプロイする方法について説明します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Azure Stack Development Kit のデプロイ

*適用対象: Azure Stack 開発キット*

[Azure Stack Development Kit](azure-stack-poc.md) をデプロイするには、次の手順を完了する必要があります。

1. [デプロイ パッケージをダウンロード](https://azure.microsoft.com/overview/azure-stack/try/?v=try)して、Cloudbuilder.vhdx を取得します。
2. 開発キットをインストールするコンピューター (開発キットのホスト) を構成するために、cloudbuilder.vhdx を準備します。 この手順の後に、開発キットのホストは、Cloudbuilder.vhdx から起動します。
3. 開発キットのホストに開発キットをデプロイします。

> [!NOTE]
> インターネット接続されていない Azure Stack 環境を使用する場合でも、最適な結果を得るには、接続中にデプロイすることが最良です。 そうすることにより、開発キット インストールに含まれている Windows Server 2016 の評価バージョンがデプロイ時にアクティブ化されます。

## <a name="download-and-extract-the-development-kit"></a>開発キットのダウンロードと抽出
1. ダウンロードを開始する前に、コンピューターが次の前提条件を満たしていることを確認します。

  - コンピューターには、オペレーティング システム ディスクに加えて、4 つの独立した同一の論理ハード ドライブ上に、少なくとも 60 GB の空きディスク領域が必要です。
  - [.NET framework 4.6 (またはそれ以降のバージョン)](https://aka.ms/r6mkiy) がインストールされています。

2. [利用開始のページに移動](https://azure.microsoft.com/overview/azure-stack/try/?v=try)し、Azure Stack Development Kit のダウンロード、詳細の入力を行い、**[送信]** をクリックします。
3. [Azure Stack Development Kit 用のデプロイ チェッカー](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409)をダウンロードして実行します。 このスタンドアロン スクリプトでは、Azure Stack Development Kit のセットアップで実行される前提条件チェックを実行します。 Azure Stack Development Kit のより大きなパッケージをダウンロードする前に、ハードウェアとソフトウェアの要件を満たしているかどうかを確認できます。
4. **[Download the software]\(ソフトウェアをダウンロード\)** の **[Azure Stack Development Kit]** をクリックします。

  > [!NOTE]
  > ASDK ダウンロード (AzureStackDevelopmentKit.exe) は、それ自体が約 10 GB です。 Windows Server 2016 評価版の ISO ファイルもダウンロードすると、ダウンロード サイズは約 17 GB に増加します。 この ISO ファイルを使用して、ASDK のインストールの完了後に、Azure Stack Marketplace に Windows Server 2016 の仮想マシン イメージを作成して追加できます。 この Windows Server 2016 評価版イメージは、ASDK でのみ使用でき、ASDK のライセンス条項の対象であることに注意してください。

5. ダウンロードが完了したら、**[実行]** をクリックして ASDK 自己展開ツール (AzureStackDevelopmentKit.exe) を起動します。
6. 自己展開ツール ウィザードの **[License Agreement]\(使用許諾契約書\)** ページで表示される使用許諾契約書を読んで同意してから、**[次へ]** をクリックします。
7. 自己展開ツール ウィザードの **[Important Notice]\(重要なお知らせ\)** ページに表示されるプライバシーに関する声明を確認し、**[次へ]** をクリックします。
8. 自己展開ツール ウィザードの **[Select Destination Location]\(インストール先の選択\)** ページで、Azure Stack セットアップ ファイルが展開される場所を選択し、**[次へ]** をクリックします。 既定の場所は、"*現在のフォルダー*"\Azure Stack Development Kit です。 
9. 自己展開ツール ウィザードの **[Ready to Extract]\(展開の準備完了\)** ページで展開先の場所の概要を確認し、**[Extract]\(展開\)** をクリックして、CloudBuilder.vhdx (約 25 GB) と ThirdPartyLicenses.rtf ファイルを展開します。 この処理には、完了までに時間がかかる場合があります。
10. CloudBuilder.vhdx ファイルを ASDK ホスト コンピューターの C:\ ドライブのルート (C:\CloudBuilder.vhdx) にコピーまたは移動します。

> [!NOTE]
> ファイルの展開後は、ハード ディスクの空き領域を増やすために、.EXE および .BIN ファイルを削除してかまいません。 または、ASDK を再デプロイする必要がある場合にファイルを再度ダウンロードしなくて済むように、これらのファイルをバックアップすることもできます。

## <a name="deploy-the-asdk-using-a-guided-experience"></a>ガイド付きのエクスペリエンスを使用した ASDK のデプロイ
ASDK は、asdk-installer.ps1 PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使用してデプロイすることができます。

> [!NOTE]
> Azure Stack Development Kit のインストーラー ユーザー インターフェイスは、WCF および PowerShell に基づくオープン ソースのスクリプトです。

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>ガイド付きのユーザー エクスペリエンスを使用して開発キットのホストを準備する
ASDK をホスト コンピューターにインストールする前に、ASDK 環境を準備する必要があります。
1. ASDK ホスト コンピューターに、ローカル管理者としてサインインします。
2. CloudBuilder.vhdx ファイルが C:\ ドライブのルート (C:\CloudBuilder.vhdx) に移動してあることを確認します。
3. 次のスクリプトを実行して、開発キットのインストーラー ファイル (asdk-installer.ps1) を [Azure Stack の GitHub ツール リポジトリ](https://github.com/Azure/AzureStack-Tools)から開発キットのホスト コンピューターの **C:\AzureStack_Installer** フォルダーにダウンロードします。

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 管理者特権で PowerShell コンソールから **C:\AzureStack_Installer\asdk-installer.ps1** スクリプトを開始し、**[Prepare Environment]\(環境の準備\)** をクリックします。
5. インストーラーの **[Select Cloudbuilder vhdx]\(Cloudbuilder vhdx の選択\)** ページで、前の手順でダウンロードおよび展開した **cloudbuilder.vhdx** ファイルを探して選択します。 このページでは、開発キットのホスト コンピューターに他のドライバーを追加する必要がある場合、オプションで **[ドライバーの追加]** チェック ボックスをオンにすることもできます。  
6. **[オプション設定]** ページで、開発キットのホスト コンピューターのローカル管理者アカウントを指定します。 

  > [!IMPORTANT]
  > これらの資格情報を指定しない場合は、開発キットのセットアップの一環としてコンピューターを再起動した後、ホストに直接または KVM でアクセスする必要があります。

7. これらのオプション設定を、**[オプション設定]** ページで指定することもできます。
    - **[コンピューター名]**: このオプションでは、開発キットのホスト名が設定されます。 名前は FQDN の要件に準拠している必要があり、長さは 15 文字以下にする必要があります。 既定は、Windows によって生成されたランダムなコンピューター名です。
    - **[タイム ゾーン]**: 開発キットのホストのタイム ゾーンが設定されます。 既定は、(UTC-8:00) 太平洋標準時 (米国およびカナダ) です。
    - **[Static IP configuration]\(静的な IP 構成\)**: デプロイが静的 IP アドレスを使用するよう設定されます。 それ以外の場合、インストーラーが cloudbuilder.vhx から再起動されるときに、ネットワーク インターフェイスが DHCP と構成されます。
11. **[次へ]** をクリックします。
12. 前の手順で静的 IP を構成した場合、次を実行する必要があります。
    - ネットワーク アダプターを選択します。 **[次へ]** をクリックする前に、アダプターに接続できることを確認します。
    - **[IP アドレス]**、**[ゲートウェイ]**、および **[DNS]** 値が正しいことを確認し、**[次へ]** をクリックします。
13. **[次へ]** をクリックして、準備プロセスを開始します。
14. 準備が **[完了]** と示されたら、**[次へ]** をクリックします。
15. **[今すぐ再起動する]** をクリックして cloudbuilder.vhdx から起動し、デプロイ処理を続行します。


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>ガイド付きのエクスペリエンスを使用して開発キットをデプロイする
ASDK ホスト コンピューターの準備ができたら、次の手順で ASDK を CloudBuilder.vhdx イメージにデプロイできます。 
1. ホスト コンピューターが CloudBuilder.vhdx イメージで正常に起動した後、前の手順で指定した管理者資格情報を使用してログインします。 
2. 管理者特権で PowerShell コンソールを開き、**\AzureStack_Installer\asdk-installer.ps1** スクリプト (現時点では CloudBuilder.vhdx イメージの別のドライブにある可能性があります) を実行します。 **[インストール]**をクリックします。
3. **[タイプ]** ドロップダウン ボックスで、**[Azure クラウド]** または **[AD FS]** を選択します。
    - **Azure クラウド**: Azure Active Directory (Azure AD) を ID プロバイダーとして構成します。 このオプションを使用するには、インターネット接続、Azure AD ディレクトリ テナントのフル ネーム (*domainname*.onmicrosoft.com 形式) または Azure AD 確認済みドメイン名、および指定したディレクトリのグローバル管理者資格情報が必要です。 
    - **AD FS**: 既定のスタンプ ディレクトリ サービスが ID プロバイダーとして使用されます。 サインインに使用する既定のアカウントは azurestackadmin@azurestack.local であり、使用するパスワードは、セットアップの一部として指定したものです。
4. **[Local administrator password]\(ローカル管理者のパスワード\)** の **[パスワード]** ボックスに、(現在の構成済みのローカル管理者パスワードと同じ) ローカル管理者パスワードを入力し、**[次へ]** をクリックします。
5. 開発キットに使用するネットワーク アダプターを選択して、**[次へ]** をクリックします。
6. BGPNAT01 仮想マシン用に DHCP または静的なネットワーク構成を選択します。
    - **[DHCP]** (既定): 仮想マシンが DHCP サーバーから IP ネットワークの構成を取得します。
    - **[静的]**: DHCP が Azure Stack に、インターネットにアクセスするための有効な IP アドレスを割り当てることができない場合にのみこのオプションは使用されます。 静的 IP アドレスは、CIDR 形式のサブネット マスク長を使用して指定する必要があります (例: 10.0.0.5/24)。
7. 任意で次の値を設定します。
    - **[VLAN ID]**: VLAN ID を設定します。 このオプションは、ホストと AzS-BGPNAT01 が物理ネットワーク (およびインターネット) にアクセスするために VLAN ID を構成する必要がある場合にのみ使用します。 
    - **[DNS Forwarder]\(DNS フォワーダ\)**: DNS サーバーは Azure Stack のデプロイの一部として作成されます。 ソリューション内のコンピューターにスタンプ外の名前解決を許可するには、既存のインフラストラクチャの DNS サーバーを提供します。 スタンプ内の DNS サーバーが、このサーバーに不明な名前解決の要求を送信します。
    - **[タイム サーバー]**: この必須フィールドでは、開発キットによって使用されるタイム サーバーを設定します。 このパラメーターは、有効なタイム サーバーの IP アドレスとして指定する必要があります。 サーバー名はサポートされていません。
  
  > [!TIP]
  > タイム サーバーの IP アドレスを検索するには、[pool.ntp.org](http:\\pool.ntp.org) にアクセスするか、time.windows.com に ping を実行します。 
  
8. **[次へ]** をクリックします。 
9. **[Verifying network interface card properties]\(ネットワーク インターフェイス カードのプロパティを確認しています\)** ページに、進行状況バーが表示されます。 
    - **[An update cannot be downloaded]\(更新プログラムをダウンロードできませんでした\)** と書かれている場合、ページの指示に従います。
    - **[完了]** と書かれている場合、**[次へ]** をクリックします。
10. **[概要] ページで** [デプロイ] **をクリックします。** ここで、開発キットのインストールに使用される PowerShell のセットアップ コマンドをコピーすることもできます。
11. Azure AD デプロイを使用している場合、セットアップを開始して数分後に、Azure AD のグローバル管理者アカウントの資格情報を入力するように求めるメッセージが表示されます。
12. デプロイには場合によって数時間かかります。その間システムは自動的に 1 回再起動されます。 デプロイが成功した場合、PowerShell コンソールには、**[COMPLETE: Action ‘Deployment’]\(完了: アクション ‘デプロイ’\)** と表示されます。 デプロイが失敗した場合は、最初から[再デプロイ](azure-stack-redeploy.md)し直すか、同じ管理者特権の PowerShell ウィンドウで次の PowerShell コマンドを使用して、前回の成功した手順からデプロイを再開することができます。

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > デプロイの進行状況を監視する場合は、セットアップ中に開発キットのホストが再起動されるときに、azurestack\AzureStackAdmin としてサインインします。 コンピューターがドメインに参加した後に、ローカル管理者としてサインインした場合、デプロイの進行状況は表示されません。 デプロイは再実行せず、代わりに azurestack\AzureStackAdmin としてサインインし、それが実行中であることを確認します。
   

## <a name="deploy-the-asdk-using-powershell"></a>PowerShell を使用した ASDK のデプロイ
前の手順では、ガイド付きのユーザー エクスペリエンスを使用して ASDK をデプロイする手順を説明しました。 別の方法として、このセクションの手順に従って PowerShell で ASDK を開発キットのホストにデプロイすることもできます。

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>CloudBuilder.vhdx から起動するように ASDK ホスト コンピューターを構成する
これらのコマンドでは、ダウンロードして展開した Azure Stack 仮想ハード ディスク (CloudBuilder.vhdx) から起動するように ASDK ホスト コンピューターを構成します。 以下の手順の完了後、ASDK ホスト コンピューターを再起動します。

1. 管理者としてコマンド プロンプトを開きます。
2. `bcdedit /copy {current} /d "Azure Stack"` を実行します。
3. 返された CLSID 値を、必要な {} を含めてコピー (Ctrl + C) します。 この値は {CLSID} と呼ばれ、後の手順で貼り付け (Ctrl + V または右クリック) を行う必要があります。
4. `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` を実行します。 
5. `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` を実行します。 
6. `bcdedit /set {CLSID} detecthal on` を実行します。 
7. `bcdedit /default {CLSID}` を実行します。
8. 起動設定を確認するには、`bcdedit` を実行します。 
9. CloudBuilder.vhdx ファイルが C:\ ドライブのルート (C:\CloudBuilder.vhdx) に移動してあることを確認し、開発キットのホスト コンピューターを再起動します。 これで、ASDK ホストが再起動されるときに、CloudBuilder.vhdx VM からの起動が既定になっているはずです。 

### <a name="prepare-the-development-kit-host-using-powershell"></a>PowerShell を使用して開発キットのホストを準備する 
開発キットのホスト コンピューターが CloudBuilder.vhdx イメージで正常に起動したら、管理者特権の PowerShell コンソールを開いてこのセクションのコマンドを実行し、ASDK を開発キットのホストにデプロイすることができます。

> [!IMPORTANT] 
> ASDK のインストールでは、ネットワーク用に 1 つだけの NIC (ネットワーク インターフェイス カード) がサポートされています。 NIC が複数ある場合は、デプロイ スクリプトを実行する前に、1 つのみが有効になっている (他はすべて無効になっている) ことを確認します。

Azure Stack と Azure AD または AD FS を、ID プロバイダーとしてデプロイできます。 どちらを使用しても、Azure Stack、リソース プロバイダー、およびその他のアプリケーションは同様に動作します。 Azure Stack の AD FS でサポートされている内容の詳細については、[主要な機能と概念](.\azure-stack-key-features.md)に関する記事を参照してください。

> [!TIP]
> セットアップ パラメーター (InstallAzureStackPOC.ps1 のオプションのパラメーターと以下の例を参照) を指定しない場合、必須パラメーターの入力を求めるメッセージが表示されます。

### <a name="deploy-azure-stack-using-azure-ad"></a>Azure AD を使用して Azure Stack をデプロイする 
**Azure AD を ID プロバイダーとして使用**して Azure Stack をデプロイするには、インターネットに直接または透過プロキシ経由で接続する必要があります。 Azure AD を使用して開発キットをデプロイするには、次の PowerShell コマンドを実行します。

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

デプロイ処理には数時間かかる場合があります。その間に、システムは自動的に 1 回再起動されます。 デプロイが成功した場合、PowerShell コンソールには、**[COMPLETE: Action ‘Deployment’]\(完了: アクション ‘デプロイ’\)** と表示されます。 デプロイが失敗した場合は、-rerun パラメーターを使用してスクリプトを再実行してみることができます。 または、最初から [ASDK を再デプロイ](.\azure-stack-redeploy.md)することもできます。
> [!IMPORTANT]
> ASDK ホストの再起動後にデプロイの進行状況を監視する場合は、AzureStack\AzureStackAdmin としてサインインする必要があります。 ホスト コンピューターが再起動され、azurestack.local ドメインに参加した後、ローカル管理者としてサインインすると、デプロイの進行状況は表示されません。 デプロイを再実行せず、代わりに azurestack としてサインインし、デプロイが実行中であることを確認してください。
>

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

自分の環境で DHCP を有効にして**いない**場合は、上のいずれかのオプションに以下の追加パラメーターを含める必要があります (使用例を示しています)。 

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

## <a name="activate-the-administrator-and-tenant-portals"></a>管理者およびテナント ポータルのアクティブ化
Azure AD を使用するデプロイの後は、Azure Stack の管理者とテナント ポータルの両方をアクティブにする必要があります。 アクティブにすることによって、ディレクトリの全ユーザーに関して適切なアクセス許可 (同意のページに一覧が表示されます) を Azure Stack ポータルと Azure Resource Manager に付与することに同意したことになります。

- 管理者ポータルについては、https://adminportal.local.azurestack.external/guest/signup に移動し、情報を読んで、**[同意する]** をクリックします。 同意後は、ディレクトリ テナント管理者ではないサービス管理者を追加することができます。

- テナント ポータルについては、https://portal.local.azurestack.external/guest/signup に移動し、情報を読んで、**[同意する]** をクリックします。 同意後は、ディレクトリのユーザーがテナント ポータルにサインインすることができます。 

> [!NOTE] 
> ポータルがアクティブにされていない場合は、ディレクトリ管理者だけがポータルにサインインし、使用することができます。 他のユーザーがサインインすると、管理者が他のユーザーにアクセス許可を付与していないことを示すエラーが表示されます。 Azure Stack の登録先ディレクトリに管理者がネイティブに属していない場合は、Azure Stack ディレクトリをアクティベーション URL に追加する必要があります。 たとえば、Azure Stack が fabrikam.onmicrosoft.com に登録されていて、管理者ユーザーが admin@contoso.com である場合は、https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com に移動して、ポータルをアクティブにします。 

## <a name="reset-the-password-expiration-policy"></a>パスワードの有効期限ポリシーのリセット 
開発キットのホストのパスワードが評価期間の終了前に期限切れにならないようにするには、ASDK のデプロイ後に次の手順を実行します。

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Powershell からパスワードの有効期限ポリシーを変更するには:
管理者特権の PowerShell コンソールで、次のコマンドを実行します。

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>パスワードの有効期限ポリシーを手動で変更するには:
1. 開発キットのホストで、**[グループ ポリシーの管理]** を開き、**[グループ ポリシーの管理]**、**[フォレスト: azurestack.local]**、**[ドメイン]**、**[azurestack.local]** に移動します。
2. **[既定のドメイン ポリシー]** を右クリックし、**[編集]** をクリックします。
3. グループ ポリシー管理エディターで、**[コンピューターの構成]**、**[ポリシー]**、**[Windows の設定]**、**[セキュリティの設定]**、**[アカウント ポリシー]**、**[パスワード ポリシー]** の順に移動します。
4. 右側のウィンドウの **[パスワードの有効期間]** をダブルクリックします。
5. **[Maximum password age Properties]\(パスワードの有効期間プロパティ\)** ダイアログ ボックスで、**[パスワードの有効期限]** の値を 180 に変更し、**[OK]** をクリックします。


## <a name="next-steps"></a>次のステップ

[Azure Stack への接続](azure-stack-connect-azure-stack.md)

[Azure Stack 環境用の PowerShell のセットアップ](azure-stack-powershell-configure-quickstart.md)

[Azure のサブスクリプションを使用した Azure Stack の登録](azure-stack-register.md)



