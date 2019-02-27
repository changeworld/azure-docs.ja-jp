---
title: ASDK を使用した Azure Stack のバックアップの検証 |Microsoft Docs
description: ASDK を使用して、Azure Stack 統合システムのバックアップを検証する方法。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 31c5d068c8fcd0b6edea7cff63098131d848a14e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416380"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>ASDK を使用してAzure Stack のバックアップを検証する
Azure Stack をデプロイし、オファー、プラン、クォータ、およびサブスクリプションなどのユーザー リソースのプロビジョニング後に、[Azure Stack インフラストラクチャのバックアップを有効にする](../azure-stack-backup-enable-backup-console.md)必要があります。 スケジュール設定とインフラストラクチャの定期的なバックアップの実行は、突発的的なハードウェアの故障またはサービスのエラーがある場合に、インフラストラクチャ管理のデータが失われないこようにします。

> [!TIP]
> 使用可能な最新のインフラストラクチャ データのコピーを確実に保持するには、この手順を開始する前に、[、オンデマンド バックアップを実行](../azure-stack-backup-back-up-azure-stack.md)することをお勧めします。 バックアップが正常に完了した後、必ずバックアップの ID をキャプチャしてください。 この ID は、クラウドの復旧中に必要になります。 

Azure Stack インフラストラクチャ バックアップには、Azure Stack の再デプロイ中に復元できる、クラウドに関する重要なデータが含まれます。 ASDK を使用して、運用しているクラウドに影響を与えずにこれらのバックアップを検証します。 

ASDK によるバックアップの検証は、次のシナリオでサポートされています:

|シナリオ|目的|
|-----|-----|
|統合ソリューションからインフラストラクチャのバックアップを検証します。|バックアップ データが有効である一次的な検証です。|
|エンド ツー エンドの復旧ワークフローについて説明します。|ASDK を使用して、バックアップ全体を検証およびエクスペリエンスを検証します。|
|     |     |

次のシナリオは、ASDK でバックアップを検証する時にサポートされて**いません**:

|シナリオ|目的|
|-----|-----|
|ASDK ビルドでバックアップを作成および復元します。|ASDK の以前のバージョンから新しいバージョンにバックアップ データを復元します。|
|     |     |


## <a name="cloud-recovery-deployment"></a>クラウドの復旧デプロイ
ASDK のクラウドの復旧デプロイを実行することによって、統合システムのデプロイからインフラストラクチャのバックアップを検証できます。 この種類のデプロイでは、ホスト コンピューターで ASDK をインストールした後、特定のサービス データがバックアップから復元します。

### <a name="prereqs"></a>クラウドの復旧の前提条件
ASDK のクラウドの復旧デプロイを開始する前に、次の情報があることを確認します:

**UI インストーラーの要件**

"*現在の UI インストーラーでは、暗号化キーのみがサポートされています*"

|前提条件|説明|
|-----|-----|
|バックアップ共有パス|Azure Stack インフラストラクチャの情報を復旧するために使用する最新の Azure Stack のバックアップの UNC ファイル共有パス。 クラウドの復旧デプロイのプロセス中に、このローカル共有が作成されます。|
|復元するバックアップの ID|クラウドの復旧中に復元するバックアップを識別する、「xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx」という英数字形式のバックアップ ID。|
|時刻サーバーの IP|132.163.97.2 など、有効な時刻サーバーの IP は、Azure Stack のデプロイに必要です。|
|外部の 証明書のパスワード|Azure Stack で使用される外部の証明書のパスワード。 CA のバックアップには、このパスワードを使用して復元する必要がある外部の証明書が含まれています。|
|バックアップ暗号化キー|Azure Stack バージョン 1901 以降にアップグレードし、バックアップの設定がまだ暗号化キーを使用して構成されている場合に必要。 暗号化キーは 1901 以降では非推奨です。 インストーラーでは、少なくとも 3 リリースに対する下位互換性モードで暗号化キーがサポートされます。 証明書を使用するようにバックアップの設定を更新した後、次の表で必要な情報を参照してください。|

|     |     | 

**PowerShell インストーラーの要件**

"*現在の PowerShell インストーラーでは、暗号化キーまたは解読証明書がサポートされています*"

|前提条件|説明|
|-----|-----|
|バックアップ共有パス|Azure Stack インフラストラクチャの情報を復旧するために使用する最新の Azure Stack のバックアップの UNC ファイル共有パス。 クラウドの復旧デプロイのプロセス中に、このローカル共有が作成されます。|
|復元するバックアップの ID|クラウドの復旧中に復元するバックアップを識別する、「xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx」という英数字形式のバックアップ ID。|
|時刻サーバーの IP|132.163.97.2 など、有効な時刻サーバーの IP は、Azure Stack のデプロイに必要です。|
|外部の 証明書のパスワード|Azure Stack で使用される外部の証明書のパスワード。 CA のバックアップには、このパスワードを使用して復元する必要がある外部の証明書が含まれています。|
|解読証明書のパスワード|省略可能。 バックアップが証明書を使用して暗号化されている場合にのみ必要です。 パスワードは、バックアップ データの解読に必要な秘密キーが含まれている自己署名証明書 (.pfx) に対するものです。|
|バックアップ暗号化キー|省略可能。 Azure Stack バージョン 1901 以降にアップグレードし、バックアップの設定がまだ暗号化キーを使用して構成されている場合に必要。 インストーラーでは、少なくとも 3 リリースに対する下位互換性モードで暗号化キーがサポートされます。 証明書を使用するようにバックアップの設定を更新した後、解読証明書のパスワードを指定する必要があります。|
|     |     | 

## <a name="prepare-the-host-computer"></a>ホスト コンピューターを準備する 
通常 ASDK のデプロイのように、インストール用にASDK ホスト システムの環境を準備する必要があります。 開発キットのホスト コンピューターが準備されると、CloudBuilder.vhdx 仮想マシンのハード ドライブから起動して ASDK のデプロイが開始します。

ASDK ホスト コンピューターで、バックアップされた Azure Stack の同じバージョンに対応する新しい cloudbuilder.vhdx をダウンロードして、指示に従って[ASDK ホスト コンピューターの準備](asdk-prepare-host.md)をします。

Cloudbuilder.vhdx からホスト サーバーを再起動した後は、ファイル共有を作成し、バックアップ データのコピーする必要があります。 ファイル共有はセットアップを実行しているアカウントにアクセスできる必要があります。これらの PowerShell コマンドの例での管理者: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

次に、最新の Azure Stack のバックアップ ファイルを新しく作成した共有にコピーします。 共有内のフォルダーの構造は次のようにする必要があります: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`。

最後に、解読証明書 (.pfx) を証明書ディレクトリ `C:\CloudDeployment\Setup\Certificates\` にコピーし、ファイルの名前を `BackupDecryptionCert.pfx` に変更します。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>クラウド復旧モードで ASDK をデプロイする

> [!IMPORTANT]
> 1. 現在のインストーラー UI では、暗号化キーのみがサポートされています。 暗号化キーを引き続き使用するシステムからのバックアップのみを検証できます。 バックアップが統合システムまたは証明書を使用する ASDK で暗号化されている場合は、PowerShell インストーラー (**InstallAzureStackPOC.ps1**) を使用する必要があります。 
> 2. PowerShell インストーラー (**InstallAzureStackPOC.ps1**) では、暗号化キーまたは証明書がサポートされています。
> 3. ASDK のインストールでは、ネットワーク用に 1 つだけの NIC (ネットワーク インターフェイス カード) がサポートされています。 NIC が複数ある場合は、デプロイ スクリプトを実行する前に、1 つのみが有効になっている (他はすべて無効になっている) ことを確認します。

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>インストーラー UI を使用して ASDK を回復モードでデプロイする
このセクションの手順は、**asdk-installer.ps1** PowerShell スクリプトをダウンロードして実行することによって提供されるグラフィカル ユーザー インターフェイス (GUI) を使って ASDK をデプロイする方法を示しています。

> [!NOTE]
> Azure Stack Development Kit のインストーラー ユーザー インターフェイスは、WCF および PowerShell に基づくオープン ソースのスクリプトです。

> [!IMPORTANT]
> 現在のインストーラー UI では、暗号化キーのみがサポートされています。

1. ホスト コンピューターが CloudBuilder.vhdx イメージで正常に起動した後、ASDK インストールの[開発キットのホスト コンピューターを準備](asdk-prepare-host.md)するときに指定した管理者資格情報を使ってサインインします。 これは、開発キット ホストのローカル管理者の資格情報と同じである必要があります。
2. 管理者特権の PowerShell コンソールを開き、**&lt;ドライブ文字>\AzureStack_Installer\asdk-installer.ps1** PowerShell スクリプトを実行します。 このスクリプトは現在、CloudBuilder.vhdx イメージの C:\ とは別のドライブに存在する可能性があります。 **[回復]** をクリックします。

    ![ASDK インストーラー スクリプト](media/asdk-validate-backup/1.PNG) 

3. ID プロバイダーと資格情報のページで、Azure AD のディレクトリ情報 (省略可能) と、ASDK ホスト コンピューターのローカル管理者パスワードを入力します。 **[次へ]** をクリックします。

    ![ID と資格情報のページ](media/asdk-validate-backup/2.PNG) 

4. ASDK ホスト コンピューターによって使用されるネットワーク アダプターを選択し、**[次へ]** をクリックします。 ASDK のインストール中、他のすべてのネットワーク インターフェイスは無効化されます。 

    ![ネットワーク アダプター インターフェイス](media/asdk-validate-backup/3.PNG) 

5. [ネットワーク構成] ページで、タイム サーバーと DNS フォワーダーの有効な IP アドレスを指定します。 **[次へ]** をクリックします。

    ![ネットワーク構成ページ](media/asdk-validate-backup/4.PNG) 

6. ネットワーク インターフェイス カードのプロパティが検証されたら、**[次へ]** をクリックします。 

    ![ネットワーク カード設定の検証](media/asdk-validate-backup/5.PNG) 

7. [バックアップ設定] ページで、前述の[前提条件セクション](#prereqs)で説明した必須情報を指定し、共有へのアクセスに使用するユーザー名とパスワードを指定します。 **[次へ]** をクリックします。 

   ![バックアップ設定ページ](media/asdk-validate-backup/6.PNG) 

8. [概要] ページで、ASDK のデプロイに使用するデプロイ スクリプトを確認します。 **[デプロイ]** をクリックしてデプロイを開始します。 

    ![概要ページ](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>PowerShell を使用して ASDK を回復モードでデプロイする

環境内の次の PowerShell コマンドを変更し、クラウド復旧モードで実行して ASDK をデプロイします:

**InstallAzureStackPOC.ps1 スクリプトを使用して、暗号化キーでクラウドの復旧を開始します。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**InstallAzureStackPOC.ps1 スクリプトを使用して、解読証明書でクラウドの復旧を開始します。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>クラウドの復旧を完了する 
クラウド の復旧デプロイ後、**Restore-AzureStack**を使用して復元を完了する必要があります。 

Azure Stack オペレーターとしてログインした後、[Azure Stack PowerShell をインストール](asdk-post-deploy.md#install-azure-stack-powershell)し、次のコマンドを実行して、バックアップからの復元時に使用される証明書とパスワードを指定します。

**証明書ファイルによる回復モード**

> [!NOTE] 
> セキュリティ上の理由から、Azure Stack のデプロイでは解読証明書は保存されません。 解読証明書とそれに関連するパスワードを、もう一度指定する必要があります。

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**暗号化キーによる回復モード**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

このコマンドレットを呼び出してクラウドを復旧した ASDK のバックアップ データの検証を開始した後、60 分間待ちます。

## <a name="next-steps"></a>次の手順
[Azure Stack の登録](asdk-register.md)

