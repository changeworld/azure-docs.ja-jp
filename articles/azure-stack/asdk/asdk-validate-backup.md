---
title: ASDK を使用した Azure Stack のバックアップの検証 |Microsoft Docs
description: ASDK を使用して、Azure Stack 統合システムのバックアップを検証する方法。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 181f37fb72584e18cc963ba1ffde070379a1b0c6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961461"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>ASDK を使用してAzure Stack のバックアップを検証する
Azure Stack をデプロイし、オファー、プラン、クォータ、およびサブスクリプションなどのユーザー リソースのプロビジョニング後に、[Azure Stack インフラストラクチャのバックアップを有効にする](../azure-stack-backup-enable-backup-console.md)必要があります。 スケジュール設定とインフラストラクチャの定期的なバックアップの実行は、突発的的なハードウェアの故障またはサービスのエラーがある場合に、インフラストラクチャ管理のデータが失われないこようにします。

> [!TIP]
> お勧めする[、オンデマンド バックアップを実行](../azure-stack-backup-back-up-azure-stack.md)使用可能な最新のインフラストラクチャ データのコピーがあることを確認するには、この手順を開始する前にします。 バックアップが正常に完了した後、必ずバックアップの ID をキャプチャしてください。 この ID は、クラウドの復旧中に必要になります。 

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



### <a name="cloud-recovery-prerequisites"></a>クラウドの復旧の前提条件
ASDK のクラウドの復旧デプロイを開始する前に、次の情報があることを確認します:

|前提条件|説明|
|-----|-----|
|バックアップ共有パス。|Azure Stack インフラストラクチャの情報を復旧するために使用する最新の Azure Stack のバックアップの UNC ファイル共有パス。 クラウドの復旧デプロイのプロセス中に、このローカル共有が作成されます。|
|バックアップ暗号化キー。|Azure Stack 管理ポータルを使用して実行するインフラストラクチャのバックアップのスケジュール設定に使用された暗号化キー。|
|復元するバックアップの ID。|クラウドの復旧中に復元するバックアップを識別する、「xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx」という英数字形式のバックアップ ID。|
|時刻サーバーの IP。|132.163.97.2 など、有効な時刻サーバーの IP は、Azure Stack のデプロイに必要です。|
|外部の 証明書のパスワード。|Azure Stack で使用される外部の証明書のパスワード。 CA のバックアップには、このパスワードを使用して復元する必要がある外部の証明書が含まれています。|
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

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>クラウド復旧モードで ASDK をデプロイする
**InstallAzureStackPOC.ps1**スクリプトを使用して、クラウドの復旧を開始します。 

> [!IMPORTANT]
> ASDK のインストールでは、ネットワーク用に 1 つだけの NIC (ネットワーク インターフェイス カード) がサポートされています。 NIC が複数ある場合は、デプロイ スクリプトを実行する前に、1 つのみが有効になっている (他はすべて無効になっている) ことを確認します。

環境内の次の PowerShell コマンドを変更し、クラウド復旧モードで実行して ASDK をデプロイします:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>インフラストラクチャ データ をバックアップから復元する
クラウド の復旧デプロイ後、**Restore-AzureStack**を使用して復元を完了する必要があります。 

Azure Stack オペレーターとしてログインした後、[Azure Stack PowerShell のインストール](asdk-post-deploy.md#install-azure-stack-powershell)を行い、その後バックアップ ID を`Name`パラメーターに置き換え、次のコマンドを実行します:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
このコマンドレットを呼び出してクラウドを復旧した ASDK のバックアップ データの検証を開始した後、60 分間待ちます。

## <a name="next-steps"></a>次の手順
[Azure Stack の登録](asdk-register.md)

