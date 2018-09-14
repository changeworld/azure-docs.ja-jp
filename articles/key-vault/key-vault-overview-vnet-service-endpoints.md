---
ms.assetid: ''
title: Azure Key Vault の VNET サービス エンドポイント | Microsoft Docs
description: Key Vault の仮想ネットワーク サービス エンドポイントの概要
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.openlocfilehash: fd3a16841e9d3c9b8bfd9b0f53c42ad1e08b3d80
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346360"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault の仮想ネットワーク サービス エンドポイント

Key Vault の仮想ネットワーク サービス エンドポイントを使用すると、指定した仮想ネットワークや、IPv4 (インターネット プロトコル バージョン 4) アドレス範囲のリストに対するアクセスを制限することができます。 発信元がこのようなソース以外から Key Vault に接続しようとすると、アクセスが拒否されます。 ユーザーが Office 365 Exchange Online、Office 365 SharePoint Online、Azure コンピューティング、Azure Resource Manager、Azure Backup などの "信頼できる Microsoft サービス" を許可するようにオプトインした場合、このようなサービスからの接続はファイアウォールを介して行われます。 もちろん、このような場合でも発信元は有効な AAD トークンを提示する必要があり、要求された操作を実行できるアクセス許可 (アクセス ポリシーとして構成) を持っている必要があります。 技術の詳細については、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。

## <a name="usage-scenarios"></a>使用シナリオ

既定で (インターネット トラフィックを含む) すべてのネットワークからのトラフィックに対するアクセスを拒否するように [Key Vault ファイアウォールと仮想ネットワーク](key-vault-network-security.md)を構成することができます。 特定の Azure 仮想ネットワークやパブリック インターネット IP アドレスの範囲からのトラフィックにアクセスを許可できるため、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

> [!NOTE]
> Key Vault ファイアウォールと仮想ネットワーク ルールは、キー コンテナーの[データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)にのみ適用されます。 Key Vault のコントロール プレーン操作 (キー コンテナーの作成、削除、変更操作、アクセス ポリシーの設定、ファイアウォールと仮想ネットワーク ルールの設定など) は、ファイアウォールや仮想ネットワーク ルールの影響を受けません。

次に例を示します。
* Key Vault を使用して暗号化キー、アプリケーションのシークレット、証明書を保存し、パブリック インターネットからキー コンテナーへのアクセスをブロックする場合。
* アプリケーションまたは指定した少数のホスト一覧のみがキー コンテナーに接続できるように、キー コンテナーへのアクセスをロックする場合。
* Azure 仮想ネットワーク (VNET) でアプリケーションを実行していて、この VNET はすべての受信および送信トラフィックに対してロックされている場合。 シークレットまたは証明書を取得する、または暗号キーを使用するには、アプリケーションはキー コンテナーに接続する必要があります。

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault のファイアウォールと仮想ネットワークを構成する

ファイアウォールと仮想ネットワークの構成には、次の手順を実行する必要があります。 これらの手順は、ファイアウォールと仮想ネットワーク ルールの設定に使用するインターフェイス (PowerShell、CLI、Azure portal) に関係なく同じです。
1. 省略可能ですが強く推奨される手順: 詳細なアクセスログを表示するように[キー コンテナーのログ記録](key-vault-logging.md)を有効にします。 これは、ファイアウォールと仮想ネットワーク ルールによってキー コンテナーにアクセスできない場合の診断に役立ちます。
2. ターゲット仮想ネットワークとサブネットの 'キー コンテナーのサービス エンドポイント' を有効にする
3. 特定の仮想ネットワーク、サブネット、および IPv4 アドレス範囲からキー コンテナーへのアクセスを制限するようにキー コンテナーのファイアウォールと仮想ネットワーク ルールを設定します。
4. すべての信頼できる Microsoft サービスからこのキー コンテナーにアクセスできるようにする必要がある場合は、'信頼できる Azure サービス' がキー コンテナーに接続できるようにするオプションを有効にする必要があります。

詳細な手順については、「[Configure Azure Key Vault Firewalls and Virtual Networks](key-vault-network-security.md)」(Azure Key Vault ファイアウォールと仮想ネットワークを構成する) を参照してください。

> [!IMPORTANT]
> ファイアウォール ルールが有効になると、発信元の要求が許可された仮想ネットワークまたは IPV4 アドレス範囲から送信された場合にのみ、すべての Key Vault [データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)の操作が実行されます。 これは、Azure portal からキー コンテナーにアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる 'Key Vault の選択機能' にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。


> [!NOTE]
> * 最大 127 個の VNET ルールと 127 個の IPv4 ルールが許可されます。 
> * 「/31」や「/32」のプレフィックス サイズを使用した小さなアドレス範囲はサポートされていません。 これらの範囲は、個々の IP アドレス ルールを使用して構成する必要があります。
> * IP ネットワーク ルールは、パブリック IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、*10.**、*172.16.**、および *192.168.** で始まるアドレスが含まれています。 
> * 現時点でサポートされているのは、IPv4 アドレスのみです。

## <a name="trusted-services"></a>信頼できるサービス
'信頼できるサービスを許可する' オプションが有効な場合にキー コンテナーへのアクセスが許可されている信頼できるサービスの一覧を次に示します。

|信頼できるサービス|使用シナリオ|
| --- | --- |
|Azure Virtual Machines 展開サービス|[ユーザー管理のキー コンテナーから VM に証明書を展開する](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Azure Resource Manager テンプレート展開サービス|[デプロイ時にセキュリティで保護された値を渡す](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Azure Disk Encryption ボリューム暗号化サービス|[Azure Disk Encryption](../security/azure-security-disk-encryption.md) を有効にするために、VM のデプロイ中に BitLocker キー (Windows VM) または DM パスフレーズ (Linux VM) とキー暗号化キーへのアクセスを許可する|
|Azure Backup|[Azure Backup](../backup/backup-introduction-to-azure-backup.md) を使用して、Azure VM のバックアップ中に関連するキーとシークレットのバックアップと復元を許可する|
|Exchange Online と SharePoint Online|[カスタマー キー](https://support.office.com/en-us/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697)を使用するサービス暗号化のためにカスタマー キーへのアクセスを許可する。|
|Azure Information Protection|[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) のテナント キーへのアクセスを許可する。|
|アプリケーション サービス|[Key Vault を介して Azure Web アプリ証明書をデプロイする](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Azure SQL Database と Data Warehouse の Transparent Data Encryption での Bring Your Own Key のサポート](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|カスタマー管理キーによる [Azure Data Lake Store 内のデータの暗号化](../data-lake-store/data-lake-store-encryption.md)|



> [!NOTE]
> 関連するキー コンテナー アクセス ポリシーは、対応するサービスがキー コンテナーにアクセスできるように設定する必要があります。

## <a name="next-steps"></a>次の手順

* [キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)
* [Azure Key Vault のファイアウォールと仮想ネットワークを構成する](key-vault-network-security.md)