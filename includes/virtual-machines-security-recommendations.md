---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164082"
---
この記事には、Azure Virtual Machines のセキュリティに関する推奨事項が含まれています。 これらの推奨事項を実装することにより、お客様は共同責任モデルに記載されたセキュリティに関する義務を果たすことができ、お客様の Web アプリ ソリューションの全体的なセキュリティが向上します。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みについて詳しくは、「[クラウド コンピューティングについての責任共有](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)」をご覧ください。

この記事に記載されているいくつかの推奨事項は、Azure Security Center によって自動的に監視できます。 Azure Security Center は、Azure のリソースを保護するための防御の最前線です。 これは Azure リソースのセキュリティの状態を定期的に分析して、潜在的なセキュリティ脆弱性を特定します。 その後、それらに対処する方法の推奨事項を提供します。

- Azure Security Center の推奨事項の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項](../articles/security-center/security-center-recommendations.md)」をご覧ください。
- Azure Security Center の詳細については「[Azure Security Center とは](../articles/security-center/security-center-intro.md)」を参照してください。

## <a name="recommendations"></a>Recommendations

| Category | 推奨 | 説明 | Security Center |
|-|-|----|--|
| 全般 | カスタム VM イメージを作成するときに最新の更新プログラムを使用する | イメージを作成する前に、オペレーティングシステムと、イメージの一部となるすべてのアプリケーションについて、最新の更新プログラムがすべてインストールされていることを確認してください。  | - |
| 全般 | VM を最新の状態に保つ | Azure Automation の [Update Management](../articles/automation/automation-update-management.md) ソリューションを使用すると、Azure 内の Windows コンピューターと Linux コンピューターに対してオペレーティング システムの更新プログラムを管理できます。 | [はい](../articles/security-center/security-center-apply-system-updates.md) |
| 全般 | 仮想マシンをバックアップする | [Azure Backup](../articles/backup/backup-overview.md) は、アプリケーション データを最小限の運用コストで保護するのに役立ちます。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれる危険があります。 Azure Backup により、Windows と Linux で実行されている仮想マシンが保護されます。 | - |
| 全般 | 復元性と可用性を高めるために複数の VM を使用する | 高可用性を必要とするアプリケーションが VM で実行される場合は、複数の VM または[可用性セット](../articles/virtual-machines/windows/manage-availability.md)が必要になります | - |
| 全般 | 事業継続とディザスター リカバリー (BCDR) 戦略を採用する | Azure Site Recovery では、事業継続性のニーズに対応するように設計されたさまざまなオプションを選択できます。 さまざまなレプリケーションおよびフェールオーバーのシナリオをサポートしします。 詳しくは、「[Site Recovery について](../articles/site-recovery/site-recovery-overview.md)」をご覧ください。 | - |
| ID 管理とアクセス管理 | VM 認証の一元化 | [Azure AD 認証](../articles/active-directory/develop/authentication-scenarios.md)を使用して、Windows と Linux の仮想マシンの認証を一元化することができます。 | - |
| データのセキュリティ | オペレーティング システム ディスクを暗号化する | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) を使用して、Windows と Linux の IaaS 仮想マシンのディスクを暗号化できます。 ディスクを暗号化すると、必要なキーがないと内容を読み取れなくなります。 ディスクを暗号化することで、ディスクをコピーした場合に発生する可能性のある不正アクセスから保存済みのデータを保護します。| [はい](../articles/security-center/security-center-apply-disk-encryption.md) |
| データのセキュリティ | データ ディスクを暗号化する | [Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) を使用して、Windows と Linux の IaaS 仮想マシンのディスクを暗号化できます。 ディスクを暗号化すると、必要なキーがないと内容を読み取れなくなります。 ディスクを暗号化することで、ディスクをコピーした場合に発生する可能性のある不正アクセスから保存済みのデータを保護します。| -  |
| データのセキュリティ | インストールされているソフトウェアを制限する | インストールされているソフトウェアを、ソリューションを正常に実装するために必要なものに限定することで、ソリューションの攻撃対象領域を減らすことができます。 | - |
| データのセキュリティ | ウイルス対策およびマルウェア対策を使用する | Azure では、Microsoft、Symantec、Trend Micro、Kaspersky などのセキュリティ ベンダーのマルウェア対策ソフトウェアを使用できます。 このソフトウェアは、悪意のあるファイル、アドウェア、他の脅威から仮想マシンを保護するのに役立ちます。 アプリケーションのワークロードに基づいて、基本的な既定のセキュリティまたは高度なカスタム構成で、Microsoft マルウェア対策保護をデプロイできます。 Azure の Microsoft マルウェア対策の詳細については、「[Azure Cloud Services および Virtual Machines 向け Microsoft マルウェア対策](../articles/security/azure-security-antimalware.md)」を参照してください | - |
| データのセキュリティ | キーとシークレットを安全に保存する | セキュリティで保護された一元管理オプションをアプリケーション所有者に提供することにより、シークレットとキーの管理を簡素化することで、偶発的なセキュリティ侵害や漏洩のリスクを軽減できます。 Azure Key Vault では、FIPS 140-2 レベル 2 に認定されたハードウェア セキュリティ モジュール (HSM) にキーを安全に保存できます。 FIPs 140.2 レベル 3 を使用してキーとシークレットを格納する必要がある場合は、[Azure Dedicated HSM](../articles/dedicated-hsm/overview.md) を使用できます。 | - |
| ネットワーク | 管理ポートへのアクセスを制限する | 攻撃者は、パブリック クラウドの IP 範囲をスキャンして開いている管理ポートを探し、よく使用されるパスワードや修正プログラムが適用されていない既知の脆弱性などの "苦労しないで済む" 攻撃を試みます [Just-In-Time (JIT) 仮想マシン (VM) アクセス](../articles/security-center/security-center-just-in-time.md)を使用すると、Azure VM への受信トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。 | - |
| ネットワーク | ネットワーク アクセスを制限する | ネットワーク セキュリティ グループを使用すると、ネットワーク アクセスを制限し、公開するエンドポイントの数を制御できます。 詳細については、「[ネットワーク セキュリティ グループを作成、変更、削除](../articles/virtual-network/manage-network-security-group.md)」をご覧ください。 | - |
| 監視 | 仮想マシンを監視する | [Azure Monitor for VMs](../articles/azure-monitor/insights/vminsights-overview.md) を使用して、ご利用の Azure 仮想マシンおよび仮想マシン スケール セットを監視することができます。 VM のパフォーマンスの問題はサービス中断に発展する場合があり、そうなれば可用性というセキュリティの原則を損ないます。 | - |

## <a name="next-steps"></a>次の手順

アプリケーション プロバイダーに追加のセキュリティ要件があるかどうかを確認する。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](../articles/security/fundamentals/abstract-develop-secure-apps.md)を参照してください。