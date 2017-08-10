---
title: "Azure Disk Encryption についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption に関してよく寄せられる質問への回答を示します。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2a81a88750ae40cd75e5d394f97c8310416546e4
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Azure Disk Encryption についてよくあるご質問 (FAQ)

この FAQ では、Windows および Linux IaaS VM の Azure Disk Encryption に関する質問に回答します。このサービスの詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)」を参照してください。

## <a name="general-questions"></a>一般的な質問
Q. Azure Disk Encryption が一般公開されているのはどのリージョンですか。
A: Windows および Linux IaaS VM の Azure Disk Encryption は、すべての Azure パブリック リージョンで一般公開されており、利用可能です。

Q: Azure Disk Encryption ではどのようなユーザー エクスペリエンスを利用できますか。
A: Azure Disk Encryption の一般公開では、Azure Resource Manager のテンプレート、Azure PowerShell、Azure CLI をサポートしています。 これにより、IaaS VM のディスク暗号化を有効にするオプションが 3 種類になり、柔軟性が向上しています。 ユーザー エクスペリエンスや段階的なガイダンスの詳細については、Azure Disk Encryption のデプロイ シナリオおよびエクスペリエンスで確認できます。

Q: Azure Disk Encryption のコストはどのくらいかかりますか。
A: Azure Disk Encryption による VM ディスクの暗号化の料金は発生しません。

Q: Azure Disk Encryption はどのような仮想マシン レベルで使用できますか。
A: Azure Disk Encryption は、Premium Storage を使用する VM と、[A、D、DS、G、GS、F](https://azure.microsoft.com/pricing/details/virtual-machines/) などの IaaS VM シリーズを含む Standard レベルの VM のみで使用可能です。 Basic レベルの VM では使用できません。

Q: Azure Disk Encryption では、どのような Linux ディストリビューションがサポートされていますか。
A: Azure Disk Encryption は、以下の Linux サーバーのディストリビューションおよびバージョンでサポートされます。
| Linux ディストリビューション | バージョン | 暗号化がサポートされているボリューム タイプ|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS とデータ ディスク |
| Ubuntu | 14.04.5-DAILY-LTS | OS とデータ ディスク |
| RHEL | 7.3 | OS とデータ ディスク |
| RHEL | 7.2 | OS とデータ ディスク |
| RHEL | 6.8 | OS とデータ ディスク |
| RHEL | 6.7 | データ ディスク |
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

Q: Azure Disk Encryption の使用を開始するにはどうすればよいですか。
A: 使用を開始する方法については、[こちら](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)の Azure Disk Encryption のホワイト ペーパーを参照してください。

Q: Azure Disk Encryption でブート ボリュームとデータ ボリュームの両方を暗号化できますか。
A: はい、Windows および Linux IaaS VM のブート ボリュームとデータ ボリュームを暗号化できます。 Windows VM の場合は、データを暗号化する前に、最初に OS ボリュームを暗号化する必要があります。 Linux VM の場合は、最初に OS ボリュームを暗号化しなくても、データを暗号化できます。 Linux の OS ボリュームを暗号化した後は、Linux IaaS VM の OS ボリュームの暗号化を無効にすることはできません

Q: Azure Disk Encryption では、"Bring Your Own Key" (BYOK) の機能は有効になりますか。
A: はい、独自のキー暗号化キーを指定することができます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されています。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

Q: Azure によって作成されたキー暗号化キーを使用できますか。
A: はい、Azure Key Vault を使用して、Azure Disk Encryption で使用するキー暗号化キーを生成することができます。 これらのキーは、Azure Disk Encryption のキー ストアである Azure Key Vault で保護されています。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

Q: オンプレミスのキー管理サービス/HSM を使用して暗号化キーを保護することはできますか。
A: オンプレミスのキー管理サービス/HSM を使用して、Azure Disk Encryption で暗号化キーを保護することはできません。 暗号化キーを保護するために使用できるのは、Azure Key Vault サービスのみです。 キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption のデプロイ シナリオとエクスペリエンスを参照してください。

Q: Azure Disk Encryption を構成するための前提条件とは何ですか。
Azure Disk Encryption の前提条件となる PowerShell スクリプトがあります。このスクリプトは、AAD アプリケーションを作成し、新しい Key Vault を作成するか既存の Key Vault を設定して、ディスク暗号化アクセスで暗号化を有効にしてシークレットおよびキーを保護します。  キー暗号化キーのサポート シナリオの詳細については、Azure Disk Encryption の前提条件とデプロイ シナリオおよびエクスペリエンスを参照してください。

Q: PowerShell を使用して Azure Disk Encryption を構成する方法の詳細情報はどこで入手できますか。
A: Azure Disk Encryption の基本タスクを実行する方法についての優れた記事や、より高度なシナリオを提供しています。 基本的なタスクについては、「[Azure PowerShell を使用する Azure Disk Encryption の探索 - パート 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)」を参照してください。 より高度なシナリオについては、「[Azure PowerShell を使用する Azure Disk Encryption の探索 – パート 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)」を参照してください。

Q: Azure Disk Encryption でサポートされているのは、どのバージョンの Azure PowerShell ですか。
A: Azure PowerShell SDK の最新バージョンを使用して、Azure Disk Encryption を構成します。 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードしてください。 Azure Disk Encryption は、Azure SDK Version 1.1.0 ではサポートされていません。

> [!NOTE]
> Linux 用 Azure Disk Encryption プレビューの拡張機能は推奨されていません。 詳細については、[こちら](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)のドキュメントを参照してください。

Q: カスタム Linux イメージで Azure Disk Encryption を適用できますか。
A: カスタム Linux イメージで Azure Disk Encryption を適用することはできません。 上に示したサポート対象のディストリビューションのギャラリー Linux イメージのみがサポートされています。 カスタム Linux イメージは現在サポートされていません。

Q: Yum の更新を使用して Linux Red Hat VM に更新プログラムを適用できますか。
A: はい、[こちら](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)に記載されているガイダンスに従って、Red Hat Linux VM に更新プログラムや修正プログラムを適用することができます。

Q: 質問やフィードバックはどこに投稿すればよいですか。A: ご質問やフィードバックは、[こちら](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)の Azure Disk Encryption フォーラムに投稿してください。

## <a name="see-also"></a>関連項目
このドキュメントでは、Azure Disk Encryption に関して最もよく寄せられるご質問について説明しました。このサービスと機能の詳細については、以下を参照してください。

- [Azure Security Center で Disk Encryption を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 仮想マシンの暗号化](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [保存時の Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

