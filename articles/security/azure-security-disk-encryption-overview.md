---
title: IaaS VM の Azure Disk Encryption の概要 | Microsoft Docs
description: この記事では、IaaS VM の Microsoft Azure Disk Encryption の概要を示します。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 0e81a48c1215e8590f90c42aee0861e6fda3db8e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392729"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>IaaS VM の Azure Disk Encryption 
Microsoft Azure では、データのプライバシーおよびデータ主権の確保に積極的に取り組んでおり、暗号化キーの暗号化、制御、管理、また、データ アクセスの制御と監査を行うための幅広い先進テクノロジを介して Azure でホストされるデータを制御できます。 この制御により、Azure の顧客はビジネス ニーズに最適なソリューションを柔軟に選択することができます。 この記事では、"Windows および Linux IaaS VM の Azure Disk Encryption" というテクノロジ ソリューションを紹介します。このソリューションは、組織のセキュリティおよびコンプライアンス コミットメントを満たすためのデータの保護に役立ちます。 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>概要
Azure Disk Encryption (ADE) は、Windows と Linux の IaaS 仮想マシン ディスクを暗号化するのに役立つ機能です。 ADE では、OS およびデータ ディスクのボリュームを暗号化するために、Windows の業界標準である [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能が使用されます。 このソリューションは、ディスクの暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されています。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

Windows および Linux IaaS VM の Azure Disk Encryption は、すべての Azure パブリック リージョンおよび AzureGov リージョンの Standard VM と Premium Storage を使用する VM で**一般に提供**されています。 Azure Disk Encryption の管理ソリューションを使用すると、次のビジネス ニーズに対応できます。

* 業界標準の暗号化テクノロジを使用して保存中の IaaS VM をセキュリティで保護し、組織のセキュリティおよびコンプライアンス要件に対処します。
* IaaS VM はお客様が管理するキーとポリシーに従ってブートします。またお客様は、Key Vault での IaaS VM の使用量を監査することができます。

> [!NOTE]
> 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。


## <a name="encryption-scenarios"></a>暗号化のシナリオ
Azure Disk Encryption ソリューションでは、次の顧客シナリオがサポートされています。

* 事前に暗号化した VHD と暗号化キーから作成された新しい Windows IaaS VM での暗号化を有効にする 
* サポートされている Azure ギャラリー イメージから作成された新しい IaaS VM での暗号化を有効にする
* Azure で実行されている既存の IaaS VM での暗号化を有効にする
* Windows IaaS VM での暗号化を無効にする
* Linux IaaS VM でのデータ ドライブの暗号化を無効にする
* マネージド ディスク VMの暗号化を有効にする
* 既存の暗号化された Premium および Premium 以外のストレージ VM の暗号化設定を更新する
* 暗号化された VM のバックアップと復元

このソリューションでは、Microsoft Azure で有効になっている場合、IaaS VM の以下のシナリオがサポートされます。

* Azure Key Vault との統合
* Standard レベルの VM: [A、D、DS、G、GS、F などの IaaS VM シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * これらの階層内の [Linux VM](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) が最小メモリ要件 (7GB) を満たしている必要があります。
* サポートされている Azure ギャラリー イメージから Windows および Linux IaaS VM、およびマネージド ディスク VM での暗号化を有効にする
* Windows IaaS VM およびマネージド ディスク VM での OS およびデータ ドライブの暗号化を無効にする
* Linux IaaS VM およびマネージド ディスク VM でのデータ ドライブの暗号化を無効にする
* Windows クライアント OS を実行している IaaS VM での暗号化を有効にする
* ボリュームのマウント パスでの暗号化を有効にする
* mdadm を使用してディスク ストライピング (RAID) で構成されている Linux VM での暗号化を有効にする
* データ ディスクの LVM を使用して Linux VM での暗号化を有効にする
* Linux VM の OS ディスクとデータ ディスクでの暗号化を有効にする 
* 記憶域スペースで構成されている Windows VM での暗号化を有効にする
* 既存の暗号化された Premium および Premium 以外のストレージ VM の暗号化設定を更新する
* KEK なしおよび KEK あり (KEK - キーの暗号化キー) の両方のシナリオについての暗号化された VM のバックアップと復元
* すべての Azure パブリック リージョンおよび AzureGov リージョンがサポートされる

このソリューションでは、以下のシナリオ、機能、およびテクノロジはサポートされていません。

* Basic レベルの IaaS VM
* Linux IaaS VM の OS ドライブの暗号化を無効にする
* OS ドライブが Linux Iaas VM で暗号化されている場合にデータ ドライブの暗号化を無効にする
* 従来の VM の作成方法を使用して作成された IaaS VM
* Linux IaaS VM ユーザーのカスタム イメージでの暗号化を有効にする機能はサポートされていません。
* オンプレミス キー管理サービスとの統合
* Azure Files (共有ファイル システム)、ネットワーク ファイル システム (NFS)、ダイナミック ボリューム、およびソフトウェアベースの RAID システムで構成されている Windows VM

## <a name="encryption-features"></a>暗号化機能
Azure IaaS VM の Azure Disk Encryption を有効にしてデプロイすると、指定された構成に応じて、以下の機能が有効になります。

* ストレージに保存中のブート ボリュームを保護するために OS ボリュームを暗号化する
* ストレージに保存中のデータ ボリュームを保護するためにデータ ボリュームを暗号化する
* Windows IaaS VM での OS およびデータ ドライブの暗号化を無効にする
* Linux IaaS VM でのデータ ドライブの暗号化を無効にする (OS ドライブが暗号化されていない場合のみ)
* Key Vault サブスクリプションの暗号化キーとシークレットを保護する
* 暗号化された IaaS VM の暗号化状態をレポートする
* IaaS 仮想マシンからディスク暗号化構成設定を削除する
* 暗号化された VM を Azure Backup サービスでバックアップおよび復元する

Windows および Linux IaaS VM の Azure Disk Encryption ソリューションには、以下のものが含まれています。

* Windows のディスク暗号化拡張機能
* Linux のディスク暗号化拡張機能
* ディスク暗号化 PowerShell コマンドレット
* ディスク暗号化 Azure コマンド ライン インターフェイス (CLI) コマンドレット
* ディスク暗号化 Azure Resource Manager テンプレート

Azure Disk Encryption ソリューションは、Windows または Linux OS を実行している IaaS VM でサポートされます。 サポートされているオペレーティング システムについて詳しくは、[前提条件](azure-security-disk-encryption-prerequisites.md)に関する記事をご覧ください。

> [!NOTE]
> Azure Disk Encryption による VM ディスクの暗号化に追加料金は発生しません。 暗号化キーの格納に使用するキー コンテナーには標準の [Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/)が適用されます。 

## <a name="encryption-workflow"></a>暗号化のワークフロー
Windows および Linux VM のディスク暗号化を有効にするには、次の手順を実行します。

1. 上記の暗号化シナリオの中から、実行する暗号化シナリオを選択します。
2. Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用してディスク暗号化を有効にすることを選択し、暗号化構成を指定します。

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい IaaS VM での暗号化を有効にします。
   * Marketplace から作成された新しい VM と、Azure で既に実行されている既存の VM については、IaaS VM での暗号化を有効にするための暗号化構成を指定します。

3. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、IaaS VM での暗号化を有効にします。

4. Key Vault に暗号化キー マテリアルを書き込むための Azure Active Directory (Azure AD) アプリケーション ID を指定します。 これにより、手順 2 で述べたシナリオについて、IaaS VM での暗号化が有効になります。

5. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。

 ![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>暗号化解除のワークフロー
IaaS VM のディスク暗号化を無効にするには、以下の手順に従います。

1. Azure で実行中の IaaS VM に対して暗号化を無効に (解除) するよう選択し、暗号化解除の構成を指定します。 Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI を使用して無効にすることができます。

 この手順によって、Windows IaaS VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。 ただし、先のセクションで述べたように、Linux で OS ディスクの暗号化を無効にすることはサポートされていません。 OS ディスクが暗号化されていない限り、暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。
2. Azure によって VM サービス モデルが更新され、IaaS VM は暗号化解除済みとしてマークされます。 VM の内容は保存時に暗号化されなくなります。

> [!NOTE]
> 暗号化を無効にしても、Key Vault や暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。
 > Linux 用の OS ディスク暗号化の無効化は、サポートされていません。 暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。
OS ドライブが暗号化されている場合、Linux のデータ ディスクの暗号化を無効にする機能はサポートされていません。

## <a name="terminology"></a>用語集
このテクノロジで使用される一般的な用語をいくつか理解するために、以下の用語表を使用してください。

| 用語集 | 定義 |
| --- | --- |
| Azure AD | Azure AD とは [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) のことです。 Azure AD アカウントは、Key Vault のシークレットの認証、格納、および取得を行うために使用されます。 |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。Key Vault は暗号化キーと機密性の高い情報を保護するために役立ちます。 詳しくは、「[Key Vault](https://azure.microsoft.com/services/key-vault/)」のドキュメントをご覧ください。 |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) は、Windows IaaS VM でディスク暗号化を有効にするために使用される業界公認の Windows ボリューム暗号化テクノロジです。 |
| BEK | BitLocker 暗号化キーは、OS ブート ボリュームとデータ ボリュームの暗号化に使用されます。 BitLocker キーは、Key Vault でシークレットとして保護されます。 |
| CLI | [Azure コマンド ライン インターフェイス](/cli/azure/install-azure-cli)に関する記事をご覧ください。|
| DM-Crypt |[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) は、Linux IaaS VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| KEK | キー暗号化キーは、シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| PS コマンドレット | [Azure PowerShell コマンドレット](/powershell/azure/overview)に関する記事をご覧ください。 |

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)
