---
title: Azure Files での SMB ファイル共有
description: サーバー メッセージ ブロック (SMB) プロトコルを使用して Azure Files でホストされているファイル共有について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 15a3064ab02a9c83a489219210be32ee99134e47
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723350"
---
# <a name="smb-file-shares-in-azure-files"></a>Azure Files での SMB ファイル共有
Azure Files には、Azure ファイル共有のマウント用に、[サーバー メッセージ ブロック (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) プロトコルと[ネットワーク ファイル システム (NFS)](https://en.wikipedia.org/wiki/Network_File_System) プロトコル (プレビュー) の 2 つの業界標準プロトコルが用意されています。 Azure Files を使用すると、ワークロードに最適なファイル システム プロトコルを選択できます。 Azure ファイル共有では、SMB と NFS の両方のプロトコルを使用した個々の Azure ファイル共有へのアクセスはサポートされませんが、同じストレージ アカウント内に SMB と NFS のファイル共有を作成することはできます。 すべてのファイル共有に対し、Azure Files により、ストレージのニーズに合わせたスケールアップが可能で、数千ものクライアントによって同時にアクセスできる、エンタープライズ レベルのファイル共有が提供されます。

この記事では、SMB Azure ファイル共有について説明します。 NFS の Azure ファイル共有の詳細については、「[Azure Files での NFS ファイル共有](files-nfs-protocol.md)」を参照してください。

## <a name="common-scenarios"></a>一般的なシナリオ
SMB ファイル共有は、エンドユーザー ファイル共有や、データベースやアプリケーションをバックアップするファイル共有など、さまざまなアプリケーションに使用されます。 SMB ファイル共有は、多くの場合、次のシナリオで使用されます。

- エンドユーザー ファイル共有 (チーム共有、ホーム ディレクトリなど)。
- Windows ベースのアプリケーション (SQL Server データベースや Win32 または .NET ローカル ファイル システム API 用に作成された基幹業務アプリケーションなど) 向けのバックアップ用ストレージ。 
- 新しいアプリケーションとサービスの開発 (特に、そのアプリケーションまたはサービスにランダム IO と階層ストレージの要件がある場合)。

## <a name="features"></a>機能
Azure Files では、SMB ファイル共有の運用環境へのデプロイに必要な SMB と Azure の主な機能がサポートされています。

- AD ドメイン参加と随意アクセス制御リスト (DACL)。
- Azure Backup と統合されたサーバーレス バックアップ。
- Azure のプライベート エンドポイントを使用したネットワークの分離。
- SMB マルチチャネルを使用した高いネットワーク スループット (Premium ファイル共有のみ)。
- AES-256-GCM、AES-128-GCM、および AES-128-CCM を含む SMB チャネルの暗号化。
- VSS 統合共有スナップショットを使用した以前のバージョンのサポート。
- 誤って削除されないようにするための、Azure ファイル共有での自動論理的な削除。
- 必要に応じて、インターネットでも安全に使用できる SMB 3.0 以降を使用した、インターネットにアクセス可能なファイル共有。

SMB ファイル共有は、オンプレミスに直接マウントすることも、[Azure File Sync を使用してオンプレミスでキャッシュする](../file-sync/file-sync-introduction.md)こともできます。  

## <a name="security"></a>セキュリティ
Azure Files に格納されるすべてのデータは、Azure Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 Storage Service Encryption は Windows の BitLocker と同様に機能し、データはファイル システム レベルで暗号化されます。 データは Azure ファイル共有のファイル システムで暗号化されるため、ディスクにエンコードされた場合、Azure ファイル共有を読み書きするために、基になるクライアント上のキーにアクセスできる必要はありません。 保存時の暗号化は、SMB と NFS の両方のプロトコルに適用されます。

既定では、すべての Azure ストレージ アカウントで転送中の暗号化が有効になっています。 つまり、SMB 経由でファイル共有をマウントする (または FileREST プロトコル経由でアクセスする) と、Azure Files では、暗号化または HTTPS を使用する SMB 3.x で作成された接続のみが許可されます。 SMB チャネル暗号化を備えた SMB 3.x をサポートしていないクライアントは、転送中の暗号化が有効になっている場合は Azure ファイル共有をマウントできません。 

Windows 10 バージョン 21H1 で Azure Files を使用すると、SMB 3.1.1 で業界最高水準の AES-256-GCM がサポートされます。 SMB 3.1.1 では、AES-128-GCM もサポートされており、SMB 3.0 では AES-128-CCM がサポートされます。 パフォーマンス上の理由から Windows 10 バージョン 21H1 では、AES-128-GCM が既定でネゴシエートされます。

Azure ストレージ アカウントでの転送中の暗号化を無効にすることができます。 暗号化が無効になっている場合、Azure Files では、SMB 2.1 および暗号化なしの SMB 3.x も許可されます。 転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のあるレガシ アプリケーションをサポートするためです。 Azure Files では、Azure ファイル共有と同じ Azure リージョン内の SMB 2.1 接続のみが許可されます。Azure ファイル共有の Azure リージョンの外部 (オンプレミスまたは異なる Azure リージョン内など) の SMB 2.1 クライアントは、ファイル共有にアクセスできません。

## <a name="smb-protocol-settings"></a>SMB プロトコル設定
Azure Files には、SMB プロトコル向けの複数の設定が用意されています。

- **SMB マルチチャネル**: SMB マルチチャネルを有効または無効にします (Premium ファイル共有のみ)。 SMB マルチチャネルを有効にする方法については、「[FileStorage ストレージ アカウントで SMB マルチチャネルを有効にする](storage-files-enable-smb-multichannel.md)」を参照してください。 SMB マルチチャネルの既定値は無効です。
- **SMB バージョン**: 許可される SMB バージョンを指定します。 サポートされているプロトコルのバージョンは、SMB 3.1.1、SMB 3.0、および SMB 2.1 です。 既定では、すべての SMB バージョンが許可されていますが、SMB 2.1 では転送中の暗号化がサポートされていないため、[require secure transit]\(セキュリティで保護された転送が必要\) が有効になっている場合、SMB 2.1 は許可されません。
- **認証方法**: 許可される SMB 認証方法を指定します。 サポートされている認証方法は、NTLMv2 と Kerberos です。 既定では、すべての認証方法が許可されています。 NTLMv2 を削除すると、ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることができなくなります。
- **Kerberos チケットの暗号化**: 許可される暗号化アルゴリズムを指定します。 サポートされている暗号化アルゴリズムは、RC4-HMAC および AES-256 です。
- **SMB チャネル暗号化**: 許可される SMB チャネル暗号化アルゴリズムを指定します。 サポートされている暗号化アルゴリズムは、AES-256-GCM、AES-128-GCM、および AES-128-CCM です。

SMB プロトコルの設定は、Azure PowerShell モジュールを使用して切り替えることができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
SMB プロトコルの設定は、PowerShell または CLI を使用して表示および変更できます。 目的のタブを選択して、SMB プロトコル設定を取得および設定する手順を確認してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
SMB プロトコル設定を取得または設定するには、ストレージ アカウントへの参照を指定する必要があります。これを行うには、リソース グループとストレージ アカウント名を直接指定するか、`Get-AzStorageAccount` コマンドレットから取得したストレージ アカウント オブジェクトを指定します。 次の例では、後者の方法を使用して、SMB プロトコル設定を取得および設定します。

これらの PowerShell コマンドを実行する前に、`<resource-group>` と `<storage-account>` を実際の環境に適した値に置き換えることを忘れないでください。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

SMB プロトコル設定を取得するには、`Get-AzStorageFileServiceProperty` コマンドレットを使用します。 SMB プロトコル設定を変更したことがない場合、コマンドレットによって返される値は null になります。 Null の戻り値は、"既定の設定が有効になっている" と解釈する必要があります。 これをわかりやすくするために、次の PowerShell コマンドは、null 値を人間が判読できる既定値に置き換えます。 

```PowerShell
# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
$smbMultichannelEnabled = $false
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $smbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }, 
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

組織のセキュリティ、パフォーマンス、および互換性の要件に応じて、SMB プロトコルの設定を変更することをお勧めします。 次の PowerShell コマンドを実行すると、SMB ファイル共有が最も安全なオプションのみに制限されます。

> [!Important]  
> SMB Azure ファイル共有を最も安全なオプションのみに制限すると、一部のクライアントが要件を満たしていない場合に接続できなくなる可能性があります。 たとえば、AES-256-GCM は、SMB チャネル暗号化のオプションとして Windows 10 バージョン 21H1 から導入されました。 これは、AES-256-GCM をサポートしていない古いクライアントは接続できないことを意味します。

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
SMB プロトコル設定を取得するには、`az storage account file-service-properties show` コマンドを使用します。 SMB プロトコル設定を変更したことがない場合、このコマンドによって返される値は null になります。 Null の戻り値は、"既定の設定が有効になっている" と解釈する必要があります。 これをわかりやすくするために、次の Bash コマンドは、null 値を人間が判読できる既定値に置き換えます。 

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# Values to be replaced
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled,"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

組織のセキュリティ、パフォーマンス、および互換性の要件に応じて、SMB プロトコルの設定を変更することをお勧めします。 次の Azure CLI コマンドを実行すると、SMB ファイル共有が最も安全なオプションのみに制限されます。

> [!Important]  
> SMB Azure ファイル共有を最も安全なオプションのみに制限すると、一部のクライアントが要件を満たしていない場合に接続できなくなる可能性があります。 たとえば、AES-256-GCM は、SMB チャネル暗号化のオプションとして Windows 10 バージョン 21H1 から導入されました。 これは、AES-256-GCM をサポートしていない古いクライアントは接続できないことを意味します。

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>制限事項
Azure Files の SMB ファイル共有では、SMB プロトコルおよび NTFS ファイル システムでサポートされている機能のサブセットがサポートされています。 ほとんどのユース ケースとアプリケーションにはこれらの機能は必要ありませんが、一部のアプリケーションは、サポートされていない機能に依存している場合、Azure Files で正常に動作しないことがあります。 次の機能はサポートされていません。

- [SMB ダイレクト](/windows-server/storage/file-server/smb-direct)  
- SMB ディレクトリ リース
- [SMB ファイル共有の VSS](/archive/blogs/clausjor/vss-for-smb-file-shares) (これにより、VSS プロバイダーは、スナップショットが作成される前にデータを SMB ファイル共有にフラッシュできます)
- 代替データ ストリーム
- 拡張属性
- オブジェクト ID
- [ハード リンク](/windows/win32/fileio/hard-links-and-junctions)
- [ソフト リンク](/windows/win32/fileio/creating-symbolic-links)  
- [再解析ポイント](/windows/win32/fileio/reparse-points)  
- [スパース ファイル](/windows/win32/fileio/sparse-files)
- [短いファイル名 (8.3 の別名)](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [圧縮](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>リージョン別の提供状況
SMB Azure ファイル共有は、すべてのパブリックとソブリンのリージョンを含む、すべての Azure リージョンで利用できます。 Premium SMB ファイル共有は、[リージョンのサブセット](https://azure.microsoft.com/global-infrastructure/services/?products=storage)で利用できます。

## <a name="next-steps"></a>次のステップ
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [Azure ファイル共有を作成する](storage-how-to-create-file-share.md)
- 希望のオペレーティング システムに SMB ファイル共有をマウントします。
    - [Windows での SMB ファイル共有のマウント](storage-how-to-use-files-windows.md)
    - [Linux での SMB ファイル共有のマウント](storage-how-to-use-files-linux.md)
    - [macOS での SMB ファイル共有のマウント](storage-how-to-use-files-mac.md)