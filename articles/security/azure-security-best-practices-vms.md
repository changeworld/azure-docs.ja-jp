---
title: "Azure 仮想マシンのセキュリティに関するベスト プラクティス | Microsoft Docs"
description: "この記事では、Azure 内に存在する仮想マシンで使われるセキュリティのベスト プラクティスのコレクションを提供します。"
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Azure 仮想マシンのセキュリティに関するベスト プラクティス

ほとんどの IaaS (サービスとしてのインフラストラクチャ) では、[仮想マシン](https://docs.microsoft.com/en-us/azure/virtual-machines/)がクラウド コンピューティングを使用している組織にとっての主要なワークロードです。 これは、組織がワークロードをクラウドに徐々に移行しようとしている[ハイブリッド シナリオ](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)において一般的です。 このような場合は、[IaaS シナリオでのセキュリティに関する一般的な考慮事項](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)に従い、Azure に存在するすべての VM にセキュリティのベスト プラクティスを適用してあることを確認する必要があります。

この記事では、Azure の VM に関するベスト プラクティスについて説明します。 このベスト プラクティスは、Azure VM に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。 

それぞれのベスト プラクティスについて、次の点を説明します。

- ベスト プラクティスの内容
- そのベスト プラクティスをお勧めする理由
- そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
- そのベスト プラクティスに代わる対処法
- そのベスト プラクティスを実践する方法

この Azure 仮想マシンのセキュリティに関するベスト プラクティスの記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

この記事で説明する Azure VM のセキュリティに関するベスト プラクティスは、次のとおりです。

- 仮想マシンの認証とアクセス制御
- 仮想マシンの可用性とネットワーク アクセス
- 暗号化の強制による Azure VM 内の保存データの保護
- 仮想マシンの更新の管理
- 仮想マシンのセキュリティ体制の管理
- 仮想マシンのパフォーマンスの監視

## <a name="virtual-machine-authentication-and-access-control"></a>仮想マシンの認証とアクセス制御

仮想マシン保護の第一歩は、承認されたユーザーのみが新しい VM をプロビジョニングできるようにすることです。 [Resource Manager ポリシー](../azure-resource-manager/resource-manager-policy.md)を使って組織内のリソースに対する規則を確立し、カスタマイズしたポリシーを作成して、[リソース グループ](../azure-resource-manager/resource-group-overview.md)などのリソースにポリシーを適用できます。 そのリソース グループに属する VM は、これらのポリシーを継承します。 これは、ニーズが異なり、異なるリソース グループに存在するリソース (VM など) を管理するときの推奨される方法ですが、[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) を使って VM へのアクセスを個別に制御することもできます。

Azure Resource Manager のポリシーと RBAC を有効にして VM へのアクセスを制御することで、VM の全体的なセキュリティが強化されます。 同じライフ サイクルを共有する密結合の VM を同じリソース グループに配置することをお勧めします。 リソース グループを使用すると、グループとしてリソースをデプロイおよび監視し、リソース グループ別に請求コストをまとめることができます。 [最低限の権限](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)アプローチを使って VM をプロビジョニングするためのユーザーへのアクセスを有効にし、ユーザーに権限を割り当てるときは以下の Azure 組み込みロールを使うように計画します。

- [仮想マシン共同作成者](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): 仮想マシンを管理することはできますが、接続している仮想ネットワークまたはストレージ アカウントは管理できません。
- [従来の仮想マシン共同作成者](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): 従来の仮想マシンを管理することはできますが、接続している仮想ネットワークまたはストレージ アカウントは管理できません。
- [セキュリティ マネージャー](../active-directory/role-based-access-built-in-roles.md#security-manager): セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます。
- [DevTest Labs ユーザー](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます。

アカウントとパスワードを管理者どうしで共有したり、複数のユーザー アカウントや複数のサービスに同じパスワードを使用したりしないでください。特に、ソーシャル メディアなど、管理以外の作業に使用するパスワードを再利用してはいけません。 理想的には、[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) テンプレートを使って、VM のプロビジョニングを保護する必要があります。 このアプローチを使うことで、デプロイの選択肢を強化し、デプロイ全体にセキュリティ設定を適用できます。

RBAC などの機能を利用してデータ アクセス制御を適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 これにより、アクセスする必要がない特定のレベルのデータにアクセスできるようになり、データのセキュリティ侵害に直接つながる恐れがあります。
 

## <a name="virtual-machine-availability-and-network-access"></a>仮想マシンの可用性とネットワーク アクセス

高可用性を必要とする重要なアプリケーションが仮想マシンで実行されている場合は、複数の VM を使うことを強くお勧めします。  可用性を高めるには、[可用性セット](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md)に少なくとも&2; つの VM を作成します。 Azure [Load Balancer](../load-balancer/load-balancer-overview.md) も、同じ可用性セットに属している負荷分散された VM を必要とします。 これらの VM にインターネットからアクセスをする必要がある場合は、[インターネットに接続するロード バランサー](../load-balancer/load-balancer-internet-overview.md)を構成する必要があります。

VM がインターネットに公開される場合は、[ネットワーク セキュリティ グループでネットワーク トラフィック フローを制御する](../virtual-network/virtual-networks-nsg.md)ことが重要です。  NSG はサブネットに適用できるため、リソースをサブネットごとにグループ化し、NSG をサブネットに適用することで、NSG の数を最小限に抑えることができます。 目的はネットワーク分離のレイヤーを作成することであり、これは Azure で[ネットワーク セキュリティ](../best-practices-network-security.md)機能を適切に構成することにより実現できます。  

また、Azure Security Center の Just In Time VM アクセス機能を使って、特定の VM にリモート アクセスできるユーザーと時間を制御することもできます。 この機能の使い方について詳しくは、以下のビデオをご覧ください。


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

インターネットに接続する VM にネットワーク アクセス制限を適用していない組織は、RDP ブルート フォース攻撃などのセキュリティ リスクに曝されます。 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>暗号化の強制による Azure VM 内の保存データの保護

現在、データ プライバシー、コンプライアンス、データ主権を確保するうえで、[保存データの暗号化](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)は欠かせません。 [Azure Disk Encryption](../security/azure-security-disk-encryption.md) を利用して、Windows および Linux の IaaS 仮想マシン (VM) のディスクを暗号化できます。 Azure Disk Encryption では、業界標準である Windows の BitLocker 機能と Linux の DM-Crypt 機能を利用して、OS およびデータ ディスクのボリュームの暗号化を提供します。

Azure Disk Encryption を利用してデータを保護し、組織のセキュリティ要件とコンプライアンス要件を達成することができます。 暗号化を使用して、不正なデータ アクセスに関連するリスクを減らすことも検討してください。 また、機密データをドライブに書き込む前にドライブを暗号化することもお勧めします。 

Azure ストレージ アカウントの保存データ ボリュームを保護するため、VM のデータ ボリュームを必ず暗号化してください。 暗号化キーとシークレットは、[Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/) を利用して保護します。 

データを暗号化しない組織は、悪意のあるユーザーや犯罪者にデータを盗まれたり、アカウントを乗っ取られて暗号化されていないデータに不正にアクセスされたりするなど、データの保全性に関する問題にさらされることが多くなります。 このようなリスクに加えて、業界の規制を遵守する必要がある企業は、適切なセキュリティ制御を使用してデータのセキュリティ強化に努めていることを証明する必要があります。

Azure Disk Encryption の詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](azure-security-disk-encryption.md)」を参照してください。


## <a name="manage-virtual-machine-updates"></a>仮想マシンの更新の管理

Windows Azure Virtual Machines はユーザーによって管理されることを想定しているため、Azure は Windows の更新プログラムをこれらの仮想マシンにプッシュしません。 これは、オンプレミスのコンピューターと同じです。 ただし、ユーザーには自動 Windows Update の設定を有効のままにしておくことをお勧めします。 もう&1; つの方法は、[Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) サーバーまたは他の適切な更新管理製品を、別の Azure 仮想マシンまたはオンプレミスにデプロイすることです。 WSUS と Windows Update はどちらも、VM を最新の状態に保ちます。 また、スキャン製品を使ってすべての IaaS 仮想マシンが最新の状態であることを確認することもお勧めします。

Azure によって提供されるストック イメージは、最新の Windows 更新プログラムが含まれるように定期的に更新されます。 ただし、デプロイ時にイメージが最新であることは保証されません。 パブリック リリースから少し (数週間以下) 遅れる可能性があります。 すべての Windows 更新プログラムを確認してインストールすることは、すべてのデプロイの第一歩です。 これは、ユーザーが提供するイメージをデプロイするとき、またはユーザーのライブラリからイメージをデプロイするときに、特に重要です。 Windows Azure ギャラリーの一部として提供されるイメージは常に、自動 Windows Update が既定で有効になります。

ソフトウェア更新ポリシーを適用していない組織は、既に修正されている既知の脆弱性を悪用する脅威にさらされる危険が大きくなります。 このようなリスクに加えて、業界の規制を遵守する必要がある企業は、適切なセキュリティ制御を使用して、クラウドに存在するワークロードのセキュリティ強化に努めていることを証明する必要があります。
重要なのは、ソフトウェア更新プログラムのベスト プラクティスについては、従来のデータセンター内と Azure IaaS 内で多数の類似点があるということです。したがって、現在のソフトウェア更新ポリシーを評価して、Azure VM を含めることをお勧めします。

## <a name="manage-virtual-machine-secure-posture"></a>仮想マシンのセキュリティ体制の管理

サイバー脅威は進化しており、VM を保護するには、脅威をすばやく検出し、アラートをトリガーして、偽陽性を減らすことができる、より高性能の監視機能が必要です。 この種のワークロードのセキュリティ体制は、更新の管理からセキュリティで保護されたネットワーク アクセス、さらにリソースへの不正アクセスを試みる脅威のアクティブな監視まで、VM のセキュリティのあらゆる面で構成されます。

[Azure Security Center](../security-center/security-center-intro.md) を使って、[Windows VM](../security-center/security-center-virtual-machine.md) と [Linux VM](../security-center/security-center-linux-virtual-machine.md) のセキュリティ体制を監視できます。 Azure Security Center の次の機能を利用して、VM を監視できます。

- 推奨される構成規則を使用したオペレーティング システム (OS) セキュリティ設定
- 不足しているシステムのセキュリティ更新プログラムと重要な更新プログラム
- エンドポイント保護の推奨事項 (マルウェア対策)
- ディスク暗号化の検証
- 脆弱性評価と修復
- 脅威の検出

Security Center は脅威を積極的に監視でき、これらの脅威はセキュリティ通知で公開されます。 関連性のある複数の脅威は、*セキュリティ インシデント*と呼ばれる&1; つのビューに集約されます。 Security Center を使って Azure に存在する VM 内の潜在的な脅威を識別する方法については、次のビデオをご覧ください。

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

VM に対してセキュリティ体制を設けていない組織は、実施されているセキュリティ管理を回避する潜在的な試行を認識できません。

## <a name="monitoring-virtual-machine-performance"></a>仮想マシンのパフォーマンスの監視

必要量より多くのリソースを消費するプロセスが VM 内にある場合、リソースの不適切な使用も問題になる可能性があります。 パフォーマンスに問題がある仮想マシンはサービス中断の原因になる場合があり、セキュリティ原則の&1; つである可用性を損ないます。 このため、対処的 (問題発生時) だけでなく、通常動作中の基準作成のためにも、VM のアクセスを監視する必要があります。

[Azure 診断ログ](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)は、仮想マシンのリソースを監視し、パフォーマンスと可用性を損なう可能性がある問題を識別するのに役立ちます。 Azure 診断の拡張機能は、Windows ベースの Azure 仮想マシンに監視および診断機能を提供します。 Azure Resource Manager [テンプレート](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)の一部として拡張機能を含めることによって、仮想マシンでこれらの機能を有効にすることができます。 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) を使ってリソースの正常性を把握することもできます。

仮想マシンのパフォーマンスを監視していない組織は、パフォーマンス パターンのある変化が正常な使用状態の一部であるのか、それとも通常より多くのリソースを消費する異常な動作の発生なのかを判断できません。 このような異常は、外部リソースからの攻撃の可能性、またはその仮想マシンで実行している侵害されたプロセスを、示している場合があります。 
