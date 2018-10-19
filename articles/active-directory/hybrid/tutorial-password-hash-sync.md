---
title: 'チュートリアル: パスワード ハッシュの同期 (PHS) を使用して単一 AD フォレストを Azure に統合する | Microsoft Docs'
description: パスワード ハッシュの同期を使用してハイブリッド ID 環境をセットアップする方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 81c88c90ff24cb5e0ab143237fb08678067ea993
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46305541"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>チュートリアル: パスワード ハッシュの同期 (PHS) を使用して単一 AD フォレストを統合する

![Create](media/tutorial-password-hash-sync/diagram.png)

以下のチュートリアルでは、パスワード ハッシュの同期を使用してハイブリッド ID 環境を作成する手順を説明します。この環境を使用して、ハイブリッド ID の動作をテストしたり、ハイブリッド ID について理解を深めたりできます。

## <a name="prerequisites"></a>前提条件
このチュートリアルを実行するために必要な前提条件を次に示します。
- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) がインストールされているコンピューター。  [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) または [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) コンピューターにインストールすることをお勧めします。
- 仮想マシンがインターネットと通信できるようにするための[外部ネットワーク アダプター](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network)。
- [Azure サブスクリプション](https://azure.microsoft.com/free)
- Windows Server 2016 のコピー

> [!NOTE]
> このチュートリアルでは、チュートリアル環境を最短時間で作成できるように PowerShell スクリプトを使用します。  各スクリプトでは、スクリプトの先頭で宣言された変数を使用します。  これらの変数は環境に合わせて変更できます。
>
>使用されるスクリプトでは、Azure AD Connect をインストールする前に、一般的な Active Directory 環境を作成します。  これらはすべてのチュートリアルに関連しています。
>
> このチュートリアルで使用する PowerShell スクリプトのコピーは、GitHub の[こちら](https://github.com/billmath/tutorial-phs)で入手できます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
ハイブリッド ID 環境を稼働させるには、まず、オンプレミスの Active Directory サーバーとして使用する仮想マシンを作成する必要があります。  以下の手順を実行します。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>オペレーティング システムの展開を完了する
仮想マシンの作成を完了するには、オペレーティング システムのインストールを完了する必要があります。

1. Hyper-V マネージャーで仮想マシンをダブルクリックします。
2. [スタート] ボタンをクリックします。
3.  "Press any key to boot from CD or DVD"\(CD または DVD から起動するには、いずれかのキーを押してください\) というメッセージが表示されます。 キーを押して続行します。
4. Windows Server の起動画面で言語を選択し、**[次へ]** をクリックします。
5. **[今すぐインストール]** をクリックします。
6. ライセンス キーを入力し、**[次へ]** をクリックします。
7. [I accept the license terms]\(ライセンス条項に同意する\) をオンにし、**[次へ]** をクリックします。
8. **[Custom: Install Windows Only (Advanced)]\(カスタム: Windows のみをインストールする (詳細設定)\)** を選択します。
9. **[次へ]** をクリックします
10. インストールが完了したら、仮想マシンを再起動してサインインし、Windows Update を実行して VM が最新の状態であることを確認します。  最新の更新プログラムをインストールします。

## <a name="install-active-directory-prerequisites"></a>Active Directory の前提条件をインストールする
仮想マシンを稼働させたら、Active Directory をインストールする前にいくつかの作業を行う必要があります。  具体的には、仮想マシンの名前を変更し、静的 IP アドレスと DNS 情報を設定して、リモート サーバー管理ツールをインストールします。   以下の手順を実行します。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD 環境を作成する
VM を作成し、名前の変更と静的 IP アドレスの設定を行いました。これで、Active Directory Domain Services をインストールして構成できます。  以下の手順を実行します。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD ユーザーを作成する
Active Directory 環境が作成されたので、テスト アカウントが必要です。  このアカウントはオンプレミスの AD 環境で作成され、Azure AD に同期されます。  以下の手順を実行します。

1. PowerShell ISE を管理者として起動します。
2. 次のスクリプトを実行します。

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Azure AD テナントを作成する
次に、ユーザーをクラウドに同期することができるように、Azure AD テナントを作成する必要があります。  新しい Azure AD テナントを作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) を参照し、Azure サブスクリプションがあるアカウントでサインインします。
2. **プラス (+) アイコン**を選択し、**Azure Active Directory** を検索します。
3. 検索結果で **[Azure Active Directory]** を選択します。
4. **作成**を選択します。</br>
![作成](media/tutorial-password-hash-sync/create1.png)</br>
5. **組織の名前**と**初期ドメイン名**を指定します。 **[作成]** を選択します。 これにより、ディレクトリが作成されます。
6. これが完了したら、**こちら**のリンクをクリックしてディレクトリを管理します。

## <a name="create-a-global-administrator-in-azure-ad"></a>Azure AD でグローバル管理者を作成する
Azure AD テナントが作成されたので、グローバル管理者アカウントを作成します。  このアカウントは、Azure AD Connect のインストール時に Azure AD コネクタ アカウントを作成するために使用されます。  Azure AD コネクタ アカウントは、Azure AD への情報の書き込みに使用します。   グローバル管理者アカウントを作成するには、次手順を実行します。

1.  **[管理]** にある **[Users]** を選択します。</br>
![作成](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  **[すべてのユーザー]** を選択し、**[+ 新しいユーザー]** を選択します。
3.  このユーザーの名前とユーザー名を入力します。 これがテナントのグローバル管理者になります。 また、**[ディレクトリ ロール]** を **[グローバル管理者]** に変更します。 一時パスワードを表示することもできます。 完了したら、**[作成]** を選択します。</br>
![作成](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. これが完了したら、新しい Web ブラウザーを開き、新しいグローバル管理者アカウントと一時パスワードを使用して myapps.microsoft.com にサインインします。
5. グローバル管理者のパスワードを覚えやすいものに変更します。

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect をダウンロードしてインストールする
次に、Azure AD Connect をダウンロードしてインストールします。  Azure AD Connect をインストールしたら、高速インストールを実行します。  以下の手順を実行します。

1. [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) をダウンロードします。
2. **AzureADConnect.msi**を検索し、ダブルクリックします。
3. [ようこそ] 画面で、ライセンス条項に同意するチェック ボックスをオンにし、 **[続行]** をクリックします。  
4. [簡単設定] 画面で、 **[簡単設定を使う]** をクリックします。</br>  
![作成](media/tutorial-password-hash-sync/express1.png)</br>
5. [Azure AD に接続] 画面で、Azure AD のグローバル管理者のユーザー名とパスワードを入力します。 **[次へ]** をクリックします。  
6. [AD DS に接続] 画面で、エンタープライズ管理者アカウントのユーザー名とパスワードを入力します。 **[次へ]** をクリックします。  
7. [構成の準備完了] 画面で、 **[インストール]** をクリックします。
8. インストールが完了したら、 **[終了]** をクリックします。
9. インストールの完了後、Synchronization Service Manager または同期規則エディターを使用する前に、サインアウトしてもう一度サインインします。


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>ユーザーが作成され、同期が実行されていることを確認する
オンプレミスのディレクトリに存在していたユーザーが同期され、現在は Azure AD テナントに存在することを確認します。  これが完了するまでに数時間かかる場合があることに注意してください。  ユーザーが同期されていることを確認するには、次の手順を実行します。


1. [Azure portal](https://portal.azure.com) を参照し、Azure サブスクリプションがあるアカウントでサインインします。
2. 左側の **[Azure Active Directory]** を選択します。
3. **[管理]** にある **[Users]** を選択します。
4. テナントに新しいユーザーが表示されていることを確認します。</br>
![同期](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>いずれかのユーザーでサインインをテストする

1.  [http://myapps.microsoft.com](http://myapps.microsoft.com) を参照します。
2. 新しいテナントに作成されたユーザー アカウントでサインインします。  user@domain.onmicrosoft.com の形式を使用してサインインする必要があります。 ユーザーがオンプレミスでのサインインに使用するものと同じパスワードを使用します。</br>
![確認](media/tutorial-password-hash-sync/verify1.png)</br>

これで、ハイブリッド ID 環境が正常にセットアップされました。この環境を使用して、Azure が提供するものをテストし、理解することができます。

## <a name="next-steps"></a>次の手順


- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [パスワード ハッシュの同期](how-to-connect-password-hash-synchronization.md)|
