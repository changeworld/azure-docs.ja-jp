---
title: NFS 3.0 プロトコル を使用して Linux に Azure Blob storage をマウントする (プレビュー) | Microsoft Docs
description: NFS 3.0 プロトコルを使用して、オンプレミスで実行される Linux ベースの Azure 仮想マシン (VM) または Linux システムから BLOB ストレージにコンテナーをマウントする方法について説明します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: d3907967572b22e7a70316080b08a4368a9805ce
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372911"
---
# <a name="mount-blob-storage-on-linux-using-the-network-file-system-nfs-30-protocol-preview"></a>ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用して Linux で Blob Storage をマウントする (プレビュー)

NFS 3.0 プロトコルを使用して、オンプレミスで実行される Linux ベースの Azure 仮想マシン (VM) または Linux システムから BLOB ストレージにコンテナーをマウントすることができます。 この記事では、ステップ バイ ステップ ガイダンスを提供しています。 BLOB ストレージでの NFS 3.0 プロトコルのサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)」を参照してください。

> [!NOTE]
> Azure BLOB Storage での NFS 3.0 プロトコルのサポートはパブリック プレビュー段階であり、次のリージョンで利用できます。米国東部、米国中部、およびカナダ中部。

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>手順 1:NFS 3.0 プロトコル機能をサブスクリプションに登録する

1. PowerShell コマンド ウィンドウを開きます。 

2. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

3. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

4. 次のコマンドを使用して、`AllowNFSV3` 機能を登録します。

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. 次のコマンドを使用して、`PremiumHns` 機能も登録します。

   ```powershell
   Register-AzProviderFeature -FeatureName PremiumHns -ProviderNamespace Microsoft.Storage  
   ```

6. 次のコマンドを使用して、リソース プロバイダーを登録します。
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>手順 2:機能が登録されたことを確認する 

登録の承認には、最大 1 時間かかります。 登録が完了したことを確認するには、次のコマンドを使用します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName PremiumHns  
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>手順 3:Azure 仮想ネットワーク (VNet) を作成する

ストレージ アカウントは VNet 内に含まれている必要があります。 VNet を使用すると、クライアントをストレージ アカウントに安全に接続できます。 VNet とその作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。

> [!NOTE]
> 同じ VNet 内のクライアントは、アカウントにコンテナーをマウントできます。 オンプレミス ネットワークで実行されているクライアントからコンテナーをマウントすることもできますが、そのためにはまずオンプレミス ネットワークを VNet に接続する必要があります。 詳細については「[サポートされているネットワーク接続](network-file-system-protocol-support.md#supported-network-connections)」を参照してください。

## <a name="step-4-configure-network-security"></a>手順 4:ネットワーク セキュリティの構成

アカウント内のデータをセキュリティで保護する唯一の方法は、VNet とその他のネットワーク セキュリティ設定を使用することです。 アカウント キーの承認、Azure Active Directory (AD) セキュリティ、アクセス制御リスト (ACL) などのデータをセキュリティで保護するために使用されるその他のツールは、NFS 3.0 プロトコルのサポートが有効にされているアカウントではまだサポートされません。 

アカウントのデータをセキュリティで保護するには、次の推奨事項を参照してください。[BLOB ストレージのセキュリティに関する推奨事項](security-recommendations.md#networking)。

## <a name="step-5-create-and-configure-a-storage-account"></a>手順 5:ストレージ アカウントの作成と構成

NFS 3.0 を使用してコンテナーをマウントするには、その機能をサブスクリプションに登録した**後に**ストレージ アカウントを作成する必要があります。 機能を登録する前に存在していたアカウントを有効にすることはできません。 

この機能のプレビュー リリースでは、NFS 3.0 プロトコルは [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) アカウントでのみサポートされています。

アカウントを構成するときに、次の値を選択します。

|設定 | 値|
|----|---|
|場所|次のいずれかのリージョン:米国東部、米国中部、およびカナダ中部 |
|パフォーマンス|Premium|
|アカウントの種類|BlockBlobStorage|
|レプリケーション|ローカル冗長ストレージ (LRS)|
|接続方法|パブリック エンドポイント (選択されたネットワーク) またはプライベート エンドポイント|
|安全な転送が必須|無効|
|階層型名前空間|Enabled|
|NFS V3|Enabled|

他のすべての設定については、既定値をそのまま使用できます。 

## <a name="step-6-create-a-container"></a>手順 6:コンテナーを作成する

次のいずれかのツールまたは SDK を使用して、ストレージ アカウントにコンテナーを作成します。

|ツール|SDK|
|---|---|
|[Azure 記憶域エクスプローラー](data-lake-storage-explorer.md#create-a-container)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
|[Azure Portal](https://portal.azure.com)|[REST](https://docs.microsoft.com/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>手順 7:コンテナーをマウントする

1. Linux システムで、ディレクトリを作成します。

   ```
   mkdir -p /mnt/test
   ```

2. 次のコマンドを使用して、コンテナーをマウントします。

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - このコマンドに表示される `<storage-account-name>` プレースホルダーをストレージ アカウントの名前に置き換えます。  

   - `<container-name>` プレースホルダーは、実際のコンテナーの名前に置き換えます。

## <a name="resolve-common-issues"></a>一般的な問題を解決

|問題またはエラー | 解決方法|
|---|---|
|`Access denied by server while mounting`|サポートされているサブネット内でクライアントが実行されていることを確認します。 [サポートされているネットワークの場所](network-file-system-protocol-support.md#supported-network-connections)のページを参照してください。|
|`No such file or directory`| マウントするコンテナーが、その機能が登録されていることを確認した後に作成されたことを確認します。 「[手順 2: 機能が登録されていることを確認する](#step-2-verify-that-the-feature-is-registered)」を参照してください。また、mount コマンドとそのパラメーターを直接ターミナルに入力してください。 別のアプリケーションからこのコマンドの一部をコピーしてターミナルに貼り付けると、貼り付けた情報の非表示の文字が原因でこのエラーが発生することがあります。|

## <a name="see-also"></a>関連項目

[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)







