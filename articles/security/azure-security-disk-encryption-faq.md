---
title: Azure Disk Encryption についてよくあるご質問 | Microsoft Docs
description: この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption についてよく寄せられる質問への回答を示します。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 98b8883a5ab0096102ab7daf90b5b2791a6f7e41
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389577"
---
# <a name="azure-disk-encryption-faq"></a>Azure Disk Encryption に関する FAQ

この記事では、Windows および Linux IaaS VM の Azure Disk Encryption に関してよく寄せられる質問 (FAQ) への回答を示します。 このサービスの詳細については、「[Windows および Linux IaaS VM の Azure Disk Encryption](azure-security-disk-encryption-overview.md)」をご覧ください。

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Azure Disk Encryption はどこで一般公開 (GA) されていますか。

Windows および Linux IaaS VM の Azure Disk Encryption は、すべての Azure パブリック リージョンで一般公開されています。

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Azure Disk Encryption では、どのようなユーザー エクスペリエンスを利用できますか。

Azure Disk Encryption GA は、Azure Resource Manager テンプレート、Azure PowerShell、および Azure CLI をサポートしています。 複数のユーザー エクスペリエンスにより、柔軟性が得られます。 IaaS VM のディスク暗号化を有効にするオプションは 3 つあります。 Azure Disk Encryption で利用可能なユーザー エクスペリエンスとステップ バイ ステップ ガイダンスの詳細については、[Windows で Azure Disk Encryption を有効にする](azure-security-disk-encryption-windows.md)方法に関するページと [Linux で Azure Disk Encryption を有効にする](azure-security-disk-encryption-linux.md)方法に関するページを参照してください。

## <a name="how-much-does-azure-disk-encryption-cost"></a>Azure Disk Encryption の価格はどれくらいですか。

Azure Disk Encryption を使用して VM ディスクを暗号化するための料金は発生しませんが、Azure Key Vault の使用に関連する料金が発生します。 Azure Key Vault のコストの詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」ページを参照してください。


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Azure Disk Encryption は、どのレベルの仮想マシンをサポートしていますか。

Azure Disk Encryption は、[A、D、DS、G、GS、および F](https://azure.microsoft.com/pricing/details/virtual-machines/) シリーズの IaaS VM を含む Standard レベルの VM で使用できます。 Premium Storage がある VM でも利用できます。 Basic レベルの VM では使用できません。

## <a name="bkmk_LinuxOSSupport"></a>Azure Disk Encryption は、どの Linux ディストリビューションをサポートしていますか。

Azure Disk Encryption は、以下の Linux サーバーのディストリビューションおよびバージョンでサポートされます。

| Linux ディストリビューション | Version | 暗号化がサポートされているボリュームの種類|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS とデータ ディスク |
| Ubuntu | 14.04.5-DAILY-LTS | OS とデータ ディスク |
| RHEL | 7.5 | データ ディスク* |
| RHEL | 7.4 | データ ディスク* |
| RHEL | 7.3 | データ ディスク* |
| RHEL | 7.2 | データ ディスク* |
| RHEL | 6.8 | データ ディスク* |
| RHEL | 6.7 | データ ディスク* |
| CentOS | 7.4 | OS とデータ ディスク |
| CentOS | 7.3 | OS とデータ ディスク |
| CentOS | 7.2n | OS とデータ ディスク |
| CentOS | 6.8 | OS とデータ ディスク |
| CentOS | 7.1 | データ ディスク |
| CentOS | 7.0 | データ ディスク |
| CentOS | 6.7 | データ ディスク |
| CentOS | 6.6 | データ ディスク |
| CentOS | 6.5 | データ ディスク |
| openSUSE | 13.2 | データ ディスク |
| SLES | 12 SP1 | データ ディスク |
| SLES | 優先順位: 12-SP1 | データ ディスク |
| SLES | HPC 12 | データ ディスク |
| SLES | 優先順位: 11-SP4 | データ ディスク |
| SLES | 11 SP4 | データ ディスク |


*__ADE は、RHEL のデータ ディスクでサポートされます。現在の ADE 実装は OS ディスクでも動作しますが、現時点で共同ではサポートされていません。ソリューションの共同サポートに向けて、Microsoft と Red Hat の双方が取り組んでいるところです。差し当たり、[Linux での Azure Disk Encryption](azure-security-disk-encryption-linux.md) に関する記事を参照してください。__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Azure Disk Encryption の使用を開始するにはどうすればよいですか。

最初に、[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)をお読みください。

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Azure Disk Encryption でブート ボリュームとデータ ボリュームの両方を暗号化できますか。

はい。Windows および Linux IaaS VM のブート ボリュームとデータ ボリュームを暗号化できます。 Windows VM の場合は、データを暗号化する前に、OS ボリュームを暗号化する必要があります。 Linux VM の場合は、先に OS ボリュームを暗号化しなくても、データを暗号化できます。 Linux の OS ボリュームを暗号化した後で Linux IaaS VM の OS ボリュームの暗号化を無効にすることはできません。

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Azure Disk Encryption では、Bring Your Own Key (BYOK) 機能を使用できますか。

はい。独自のキー暗号化キーを指定できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Azure によって作成されたキー暗号化キーを使用できますか。

はい。Azure Key Vault を使用して、Azure Disk Encryption で使用するキー暗号化キーを生成できます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されます。 キー暗号化キーの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護できますか。

Azure Disk Encryption では、オンプレミスのキー管理サービスまたは HSM を使用して暗号化キーを保護することはできません。 暗号化キーを保護するために使用できるのは、Azure Key Vault サービスのみです。 キー暗号化キーのサポート シナリオの詳細については、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Azure Disk Encryption を構成するための前提条件は何ですか。

Azure Disk Encryption の前提条件があります。 Azure Active Directory アプリケーションを作成するか、新しいキー コンテナーを作成するか、既存のキー コンテナーをディスク暗号化アクセス用に設定してシークレットとキーの暗号化と保護を実行できるようにするには、「[Azure Disk Encryption prerequisites (Azure Disk Encryption の前提条件)](azure-security-disk-encryption-prerequisites.md)」を参照してください。 キー暗号化キーのサポート シナリオの詳細については、[Azure Disk Encryption の概要](azure-security-disk-encryption-overview.md)に関するページを参照してください。

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Azure Disk Encryption を構成するための PowerShell の使用方法に関する詳細情報はどこで入手できますか。

基本的な Azure Disk Encryption タスクの実行方法や、より高度なシナリオに関するいくつかの優れた記事を用意しています。 基本的なタスクについては、「[Explore Azure Disk Encryption with Azure PowerShell – Part 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)」(Azure PowerShell を使用する Azure Disk Encryption の探索 - パート 1) を参照してください。 より高度なシナリオについては、「[Explore Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)」(Azure PowerShell を使用する Azure Disk Encryption の探索 – パート 2) を参照してください。

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Azure Disk Encryption は、どのバージョンの Azure PowerShell をサポートしていますか。

Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用してください。 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードしてください。 Azure Disk Encryption は、Azure SDK Version 1.1.0 では*サポートされていません*。

> [!NOTE]
> Linux 用 Azure Disk Encryption プレビューの拡張機能は非推奨となっています。 詳細については、「[Deprecating Azure disk encryption preview extension for Linux IaaS VMs](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)」(Linux IaaS VM 用 Azure Disk Encryption プレビューの拡張機能の非推奨化) を参照してください。

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>カスタム Linux イメージに対して Azure Disk Encryption を適用できますか。

カスタム Linux イメージに対して Azure Disk Encryption を適用することはできません。 前述したサポート対象のディストリビューションのギャラリー Linux イメージのみがサポートされます。 カスタム Linux イメージは現時点ではサポートされていません。

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>yum update を使用する Linux Red Hat VM に更新プログラムを適用できますか。

はい。Red Hat Linux VM に対して更新プログラムまたは修正プログラムを実行できます。 詳細については、「[Applying updates to a encrypted Azure IaaS Red Hat VM using Yum Update](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)」(yum update を使用して暗号化された Azure IaaS Red Hat VM に更新プログラムを適用する) を参照してください。

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Linux で推奨される Azure Disk Encryption ワークフローは何ですか。

Linux で最適な結果を得るには、次のワークフローが推奨されます。
* 目的の OS ディストリビューションとバージョンに対応する、変更されていないストック ギャラリー イメージを使用します。
* 暗号化するマウント済みのドライブをバックアップします。  これにより、暗号化が完了する前に VM が再起動された場合などの障害が発生した場合でも復旧できます。
* 暗号化 (VM の特性や、インストールされているデータ ディスクのサイズによっては数時間から数日かかることがあります)
* 必要に応じて、イメージをカスタマイズし、イメージにソフトウェアを追加します。

このワークフローが可能でない場合は、DM-Crypt を使用したディスク全体の暗号化に代わる方法として、プラットフォームのストレージ アカウント レイヤーで [Storage Service Encryption](../storage/common/storage-service-encryption.md) (SSE) を使用できます。

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>ディスク "Bek ボリューム" または "/mnt/azure_bek_disk" とは何ですか。

Windows の "Bek ボリューム" または Linux の "/mnt/azure_bek_disk" は、暗号化された Azure IaaS VM の暗号化キーを安全に格納するローカルのデータ ボリュームです。
> [!NOTE]
> このディスクのコンテンツの削除や編集はしないでください。 IaaS VM 上のすべての暗号化操作に暗号化キーが必要なため、ディスクのマウントを解除しないでください。

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>質問したり、フィードバックを提供したりするにはどこに移動すればよいですか。

[Azure Disk Encryption フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption)で質問したり、フィードバックを提供したりできます。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Disk Encryption に関して最もよく寄せられるご質問について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure Virtual Machine を暗号化する](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [保存時の Azure データの暗号化](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
