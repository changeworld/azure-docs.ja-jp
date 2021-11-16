---
title: Azure Files での SMB ファイル共有
description: サーバー メッセージ ブロック (SMB) プロトコルを使用して Azure Files でホストされているファイル共有について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 23146fecfcb27e8f468a693804fff5e559866b38
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522025"
---
# <a name="smb-file-shares-in-azure-files"></a>Azure Files での SMB ファイル共有
Azure Files には、Azure ファイル共有のマウント用に、[サーバー メッセージ ブロック (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) プロトコルと[ネットワーク ファイル システム (NFS)](https://en.wikipedia.org/wiki/Network_File_System) プロトコルの 2 つの業界標準プロトコルが用意されています。 Azure Files を使用すると、ワークロードに最適なファイル システム プロトコルを選択できます。 Azure ファイル共有では、SMB と NFS の両方のプロトコルを使用した個々の Azure ファイル共有へのアクセスはサポートされませんが、同じストレージ アカウント内に SMB と NFS のファイル共有を作成することはできます。 すべてのファイル共有に対し、Azure Files により、ストレージのニーズに合わせたスケールアップが可能で、数千ものクライアントによって同時にアクセスできる、エンタープライズ レベルのファイル共有が提供されます。

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

Windows Server 2022 または Windows 11 と共に使用する場合、Azure Files では、SMB 3.1.1 を使用した AES-256-GCM がサポートされます。 SMB 3.1.1 では、AES-128-GCM もサポートされており、SMB 3.0 では AES-128-CCM がサポートされます。 パフォーマンス上の理由から Windows 10 バージョン 21H1 では、AES-128-GCM が既定でネゴシエートされます。

Azure ストレージ アカウントでの転送中の暗号化を無効にすることができます。 暗号化が無効になっている場合、Azure Files では、SMB 2.1 および暗号化なしの SMB 3.x も許可されます。 転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のあるレガシ アプリケーションをサポートするためです。 Azure Files では、Azure ファイル共有と同じ Azure リージョン内の SMB 2.1 接続のみが許可されます。Azure ファイル共有の Azure リージョンの外部 (オンプレミスまたは異なる Azure リージョン内など) の SMB 2.1 クライアントは、ファイル共有にアクセスできません。

## <a name="smb-protocol-settings"></a>SMB プロトコル設定
Azure Files には、SMB プロトコルの動作、パフォーマンス、およびセキュリティに影響する複数の設定があります。 これらは、ストレージ アカウント内のすべての Azure ファイル共有に対して構成されます。

### <a name="smb-multichannel"></a>SMB マルチチャネル
SMB マルチチャネルでは、SMB 3.x クライアントが SMB ファイル共有に複数のネットワーク接続を確立できます。 Azure Files では、プレミアム ファイル共有で SMB マルチチャネルがサポートされています (FileStorage ストレージ アカウントの種類のファイル共有)。 Azure Files で SMB マルチチャネルを有効にしても追加料金は発生しません。 SMB マルチチャネルは既定で無効になっています。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
SMB マルチチャネルのステータスを表示するには、Premium ファイル共有を含むストレージ アカウントに移動し、ストレージ アカウントの目次にある **[データ ストレージ]** 見出しの **[ファイル共有]** を選択します。 SMB マルチチャネルのステータスは、 **[ファイル共有の設定]** セクションで確認できます。

![ストレージ アカウントの [ファイル共有] セクションで SMB マルチチャネル設定が強調表示されているスクリーンショット](./media/files-smb-protocol/1-smb-multichannel-enable.png)

SMB マルチチャネルを有効または無効にするには、現在のステータスを選択します (ステータスに応じて **[有効]** または **[無効]** )。 表示されるダイアログでは、SMB マルチチャネルを有効または無効に切り替えることができます。 目的の状態を選択し、 **[保存]** を選択します。

:::image type="content" source="media/files-smb-protocol/2-smb-multichannel-enable.png" alt-text="SMB マルチチャネル機能を有効または無効にするダイアログのスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
SMB マルチチャネルのステータスを取得するには、`Get-AzStorageFileServiceProperty` コマンドレットを使用します。 これらの PowerShell コマンドを実行する前に、`<resource-group>` と `<storage-account>` を実際の環境に適した値に置き換えることを忘れないでください。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$defaultSmbMultichannelEnabled = $false

# Get the current value for SMB Multichannel
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $defaultSmbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }
```

SMB マルチチャネルを有効または無効にするには、`Update-AzStorageFileServiceProperty` コマンドレットを使用します。

```PowerShell
Update-AzStorageFileServiceProperty `
    -StorageAccount $storageAccount `
    -EnableSmbMultichannel $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
SMB マルチチャネルのステータスを取得するには、`az storage account file-service-properties show` コマンドを使用します。 これらの Bash コマンドを実行する前に、`<resource-group>` と `<storage-account>` を実際の環境に適した値に置き換えることを忘れないでください。

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 

## Search strings
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"

# Replacement values for null parameters. 
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"

# Print returned settings
echo $protocolSettings
```

SMB マルチチャネルを有効または無効にするには、`az storage account file-service-properties update` コマンドを使用します。

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-smb-multichannel "true"
```
---

### <a name="smb-security-settings"></a>SMB セキュリティ設定
Azure Files は、組織の要件に応じて、SMB プロトコルの互換性またはセキュリティを強化する設定を公開します。 既定では、Azure Files は最大限の互換性を持つように構成されているため、これらの設定を制限すると、一部のクライアントが接続できなくなる可能性があることに注意してください。

Azure Files では、以下の設定が公開されています。

- **SMB バージョン**: 許可される SMB バージョンを指定します。 サポートされているプロトコルのバージョンは、SMB 3.1.1、SMB 3.0、および SMB 2.1 です。 既定では、すべての SMB バージョンが許可されていますが、SMB 2.1 では転送中の暗号化がサポートされていないため、[require secure transit]\(セキュリティで保護された転送が必要\) が有効になっている場合、SMB 2.1 は許可されません。
- **認証方法**: 許可される SMB 認証方法を指定します。 サポートされている認証方法は、NTLMv2 と Kerberos です。 既定では、すべての認証方法が許可されています。 NTLMv2 を削除すると、ストレージ アカウント キーを使用して Azure ファイル共有をマウントすることができなくなります。
- **Kerberos チケットの暗号化**: 許可される暗号化アルゴリズムを指定します。 サポートされている暗号化アルゴリズムは、RC4-HMAC および AES-256 です。
- **SMB チャネル暗号化**: 許可される SMB チャネル暗号化アルゴリズムを指定します。 サポートされている暗号化アルゴリズムは、AES-256-GCM、AES-128-GCM、および AES-128-CCM です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
SMB セキュリティの設定は、PowerShell または CLI を使用して表示および変更できます。 目的のタブを選択して、SMB セキュリティ設定を取得および設定する手順を確認してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
SMB プロトコル設定を取得するには、`Get-AzStorageFileServiceProperty` コマンドレットを使用します。 これらの PowerShell コマンドを実行する前に、`<resource-group>` と `<storage-account>` を実際の環境に適した値に置き換えることを忘れないでください。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

# Gets the current values of the SMB security settings
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
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
> SMB Azure ファイル共有を最も安全なオプションのみに制限すると、一部のクライアントが要件を満たしていない場合に接続できなくなる可能性があります。 たとえば、AES-256-GCM は、SMB チャネル暗号化のオプションとして Windows Server 2022 と Windows 11 から導入されました。 これは、AES-256-GCM をサポートしていない古いクライアントは接続できないことを意味します。

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
SMB セキュリティ設定のステータスを取得するには、`az storage account file-service-properties show` コマンドを使用します。 これらの Bash コマンドを実行する前に、`<resource-group>` と `<storage-account>` を実際の環境に適した値に置き換えることを忘れないでください。

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values.

# Values to be replaced
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{"
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
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

組織のセキュリティ、パフォーマンス、および互換性の要件に応じて、SMB プロトコルの設定を変更することをお勧めします。 次の Azure CLI コマンドを実行すると、SMB ファイル共有が最も安全なオプションのみに制限されます。

> [!Important]  
> SMB Azure ファイル共有を最も安全なオプションのみに制限すると、一部のクライアントが要件を満たしていない場合に接続できなくなる可能性があります。 たとえば、AES-256-GCM は、SMB チャネル暗号化のオプションとして Windows Server 2022 と Windows 11 から導入されました。 これは、AES-256-GCM をサポートしていない古いクライアントは接続できないことを意味します。

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