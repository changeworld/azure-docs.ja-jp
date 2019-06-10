---
title: FAQ - CloudSimple による VMware ソリューション
description: CloudSimple による Azure VMware ソリューションのよく寄せられる質問
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358053"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple による VMware ソリューションについてよく寄せられる質問

CloudSimple による VMware ソリューションについてよく寄せられる質問と回答は、サービスとその使用方法を理解するために役立ちます。  質問と回答は次の各カテゴリに分類されます。

* CloudSimple サービス
* 接続
* ネットワーク
* セキュリティ
* Compute
* Storage
* VMware
* Azure の統合
  
## <a name="cloudsimple-service"></a>CloudSimple サービス

**CloudSimple による VMware ソリューションとは?**

**CloudSimple による VMware ソリューション**は、わずか数分で VMware ワークロードを変換し、Azure 上のプライベートな専用クラウドに拡張します。 プロビジョニング、インフラストラクチャの管理、そしてオンプレミスと Azure の間でのワークロードの調整を任せることができます。 アプリはオンプレミスと Azure でまったく同じように実行されるため、アプリの複雑な再設計をしなくても、クラウドの弾力性とサービスのベネフィットを受けられます。 CloudSimple は、オンデマンド プロビジョニング、成長に応じた課金、容量最適化を提供するクラウド使用モデルによって、総保有コストを削減します。  機能、利点、シナリオについては、「[CloudSimple による Azure VMware ソリューションとは](cloudsimple-vmware-solutions-overview.md)」を参照してください。

**CloudSimple プライベート クラウドとは?**

Azure の場所にある Microsoft Azure インフラストラクチャ (ハードウェアとデータセンターの占有領域) にデプロイされた、高性能なコンピューティング、ストレージ、およびネットワーク環境で構成されるプライベートの専用クラウドをプロビジョニングします。  プライベート クラウドは、ネイティブ VMware プラットフォームを "サービスとして" 提供します。 VMware の用語では、各プライベート クラウドには vCenter Server のインスタンスが 1 つだけ含まれます。 vCenter Server は、1 つ以上の vSphere クラスターに含まれる複数の ESXi ノードを、対応する仮想 SAN (vSAN) ストレージと共に管理します。 CloudSimple サービスは、Azure サブスクリプションに複数のプライベート クラウドを含めることができます。  プライベート クラウドの詳細については、[プライベート クラウドの概要](cloudsimple-private-cloud.md)に関する記事を参照してください。

**CloudSimple サービスが使用可能な場所は?**

CloudSimple は米国東部および米国西部リージョンで利用できます。

**CloudSimple のサブスクリプションを有効にする方法は?**

[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) から Microsoft アカウントの担当者に連絡して、CloudSimple サービスのサブスクリプションを有効にすることができます。 CloudSimple サービスを有効にするサブスクリプション ID を電子メールに記入してください。  

**CloudSimple ポータルにアクセスする方法は?**

Azure portal から CloudSimple ポータルにアクセスします。  CloudSimple ポータルへのアクセスの詳細については、[CloudSimple による VMware ソリューションのポータルに Azure portal からアクセスする](https://docs.azure.cloudsimple.com/access-cloudsimple-portal)方法に関する記事を参照してください。 

**プライベート クラウドの容量を増やす方法は?**

Azure portal からノードを購入し、CloudSimple ポータルからプライベート クラウドを拡張します。  既存の vSphere クラスターにノードを追加するか、新しい vSphere クラスターを作成することによってプライベート クラウドを拡張できます。  手順については、[CloudSimple プライベート クラウドの拡張](https://docs.azure.cloudsimple.com/expand-private-cloud)に関する記事を参照してください。

**メンテナンス中、プライベート クラウドはどうなりますか?**

CloudSimple では、予定メンテナンスに先立って期日を定期的に通知します。  プライベート クラウドの可用性を確保するために、メンテナンスは中断を伴わない方法で行われます。  メンテナンスは次のいずれかの種類になります。

1. **CloudSimple インフラストラクチャ:** CloudSimple インフラストラクチャは、高可用性となるように設計されています。  メンテナンス中は、いかなる影響も発生させずに冗長コンポーネントを 1 つずつ更新することによって、プライベート クラウドの接続性と可用性が保証されます。  プライベート クラウドの vCenter、すべての仮想マシン、プライベート クラウドからのインターネット接続、オンプレミスまたは Azure への接続にアクセスできます。
2. **CloudSimple ポータル:** メンテナンス中、CloudSimple ポータルの一部の機能がアクセス不能または無効になる場合があります。  メンテナンス通知には、ポータルでできることの詳細が記載されます。

## <a name="connectivity"></a>接続

**CloudSimple リージョン ネットワークへの接続オプションにはどのようなものがありますか?**

CloudSimple では、CloudSimple リージョン ネットワークに接続するための 3 種類の接続オプションを提供しています。  3 つすべてを併用できます。

1. オンプレミスのデータセンターから CloudSimple リージョン ネットワークへの ExpressRoute 接続 - 高速で低待機時間の、セキュリティで保護されたプライベート接続であり、Global Reach を使用して、オンプレミスの ExpressRoute 回線と CloudSimple ExpressRoute 回線をブリッジします。 接続のセットアップについては、[ExpressRoute を使用してオンプレミスから CloudSimple に接続する](https://docs.azure.cloudsimple.com/on-premises-connection)方法に関する記事を参照してください。
2. Azure 仮想ネットワークから CloudSimple リージョン ネットワークへの ExpressRoute 接続 - 高速で低待機時間の、セキュリティで保護されたプライベート接続であり、仮想ネットワーク ゲートウェイを使用して、Azure 上の仮想ネットワークと CloudSimple ExpressRoute 回線をブリッジします。  接続のセットアップについては、[ExpressRoute を使用して CloudSimple プライベート クラウド環境を Azure 仮想ネットワークに接続する](https://docs.azure.cloudsimple.com/azure-expressroute-connection)方法に関する記事を参照してください。
3. オンプレミスのデータセンターから CloudSimple リージョン ネットワークへのサイト間 VPN 接続 - オンプレミスの VPN デバイスから CloudSimple プライベート クラウド リージョンへの、セキュリティで保護された仮想プライベート ネットワーク。  VPN 接続のセットアップについては、[オンプレミス ネットワークと CloudSimple プライベート クラウド間の VPN 接続のセットアップ]に関する記事を参照してください。

**プライベート クラウドに接続する方法は?**

CloudSimple ポータルでは、プライベート クラウドの詳細を表示できます。 プライベート クラウドに対応する vCenter に接続するには、サイト対サイト、ポイント対サイト、または ExpressRoute を使用してネットワーク接続が確立されていることを確認します。 次に、Azure portal から CloudSimple ポータルを起動し、ホーム ページまたはプライベート クラウドの詳細ページで *[vSphere Client を起動する]* をクリックします。

**ExpressRoute 回線の利点は?**

Azure ExpressRoute 回線は、高速で低待機時間の、セキュリティで保護された接続を提供します。  CloudSimple では、リージョンごとに、また顧客ごとに専用の ExpressRoute 回線が提供されます。  この回線を使用して、セキュリティで保護された接続をオンプレミスまたは Azure サブスクリプションから確立できます。

**CloudSimple とのネットワーク接続のコストはどのくらいですか。CloudSimple への、また CloudSimpleから Azure へのエグレス料金はありますか? リージョンをまたぐ場合は?**

ネットワーク エグレスに対する料金はありません。  仮想ネットワークからの、またはオンプレミスの ExpressRoute 回線からのエグレス トラフィックには Azure の標準料金が適用されます。

## <a name="networking"></a>ネットワーク

**プライベート クラウドのためにどのようなネットワーク機能が利用できますか?**

VLAN (とそのサブネット)、ファイアウォール テーブルをプロビジョニングし、パブリック IP アドレスを割り当てて、プライベート クラウドで稼働している仮想マシンにマップすることができます。  詳細については、[VLAN とサブネットの概要](cloudsimple-vlans-subnets.md)、[ファイアウォール テーブルの概要](cloudsimple-firewall-tables.md)、および[パブリック IP アドレスの概要](cloudsimple-public-ip-address.md)に関する記事を参照してください。

**プライベート クラウド内のアプリケーションに対して、異なるサブネットをセットアップする方法は?**

CloudSimple ポータルから、プライベート クラウド上に VLAN を作成できます。  VLAN を作成したら、その VLAN を使用してプライベート クラウド vCenter 上に分散ポート グループを作成し、その分散ポート グループに接続された仮想マシンを作成することができます。  VLAN/サブネットのファイアウォール テーブルを有効にして、ネットワーク トラフィックをセキュリティで保護するためのファイアウォール規則を定義できます。

**プライベート クラウドに対してどのようなファイアウォール設定が利用できますか?**

南北および東西のトラフィックのための規則を構成できます。  規則はファイアウォール テーブルで定義されます。  ファイアウォール テーブルは、プライベート クラウド上の VLAN に添付できます。  セットアップ手順については、[プライベート クラウド用のファイアウォール テーブルおよび規則のセットアップ](https://docs.azure.cloudsimple.com/firewall)に関する記事を参照してください。

**プライベート クラウド環境内の VM にパブリック IP アドレスを割り当てることはできますか?**

CloudSimple ポータルでは、新しいパブリック IP アドレスを割り当てて、それを仮想マシンまたはアプライアンスのプライベート IP アドレスに関連付けることが簡単にできます。  ポータルで、新しいファイアウォール規則を作成または既存のファイアウォール規則を適用して、特定のポートや特定の IP アドレスの集合からのトラフィックを許可することもできます。 セットアップ手順については、[プライベート クラウド環境のパブリック IP アドレスの割り当て](https://docs.azure.cloudsimple.com/public-ips)に関する記事を参照してください。

## <a name="security"></a>セキュリティ

**CloudSimple にはどのようなセキュリティ オプションがありますか?**

CloudSimple プライベート クラウドは、プライベート クラウド環境をセキュリティで保護するために、次のセキュリティ機能を提供します。

1. **保存データの暗号化**:プライベート クラウド内の vSAN ストレージ上にある保存データを暗号化できます。 vSAN は、Azure vNet またはオンプレミス環境にデプロイできる外部のキー管理サーバーをサポートします。  詳細については、[CloudSimple プライベート クラウドでの vSAN 暗号化の構成](https://docs.azure.cloudsimple.com/vsan-encryption)に関する記事を参照してください。
2. **ネットワークのセキュリティ**:ファイアウォール規則を使用して、プライベート クラウドからの、またインターネット、オンプレミス、プライベート クラウドのサブネット内からプライベート クラウドへのネットワーク トラフィック フローを制御します。
3. **セキュリティで保護されたプライベート接続**:オンプレミス ネットワークと Azure サブスクリプション間のセキュリティで保護されたプライベート接続。

## <a name="compute"></a>Compute

**どのような種類のホストが使用可能ですか?**

CloudSimple は 2 種類のホストを提供します。

* **CS28 ノード:** CPU:2x 2.2 GHz、合計 28 コア、48 HT。  RAM: 256 GB。  ストレージ:1600 GB NVMe キャッシュ、5760 GB データ (オール フラッシュ)。 ネットワーク: 2x25Gbe NIC
* **CS36 ノード:** CPU 2x 2.3 GHz、合計 36 コア、72 HT。  RAM: 512 GB。  ストレージ:3200 GB NVMe キャッシュ、11,520 GB データ (オール フラッシュ)。  ネットワーク: 2x25Gbe NIC

**ハードウェア障害はどのように処理されますか?**

すべての CloudSimple インフラストラクチャは、CloudSimple プラットフォームと、当社のサービス運用チームによって継続的に監視されています。  ハードウェア障害が検出された場合、新しいノードがプライベート クラウドに追加され、障害が発生したノードは削除されることで、プライベート クラウドの高可用性が確保されます。

## <a name="storage"></a>Storage

**プライベート クラウドではどのような種類のストレージがサポートされますか?**

CloudSimple では、すべてのプライベート クラウドで**オール フラッシュ VMware vSAN ストレージ**が提供されます。  個々の vSphere は、その独自の vSAN データ ストアを使用して作成されます。  詳細については、[プライベート クラウド VMware コンポーネント - vSAN ストレージ](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage)に関する記事を参照してください。

**データの暗号化はサポートされていますか?**
はい。  プライベート クラウド上に vSAN ストレージをセットアップし、オンプレミスまたは Azure にデプロイされるキー管理サーバー (KMS) を使用して、vSAN に保存されているデータを暗号化することができます

**障害が発生したディスクはどのように処理されますか?**

CloudSimple の監視機能は、プライベート クラウドのすべてのハードウェア コンポーネントを継続的に監視します。  ディスク障害が検出された、または (ヒューリスティックに基づいて) ディスクが障害発生と判定された場合、新しいノードが自動的にプライベート クラウドに追加されます。  障害が発生したか、または障害が発生する可能性が高いディスクがあるノードは、プライベート クラウドから削除されます。

## <a name="vmware"></a>VMware

**大規模なアップロードを実行したり、アプリケーションやデータをオンプレミスから移行したりする方法は?**

CloudSimple はネイティブの VMware vSphere ソリューションを提供します。  一括データ移行に使用されるツールはすべて、CloudSimple プライベート クラウドで使用できます。  使用できるオプションには、次のものがあります。

1. データの一括移行用の VMware HCX。
2. Storage vMotion を使用して、オンプレミスから CloudSimple にデータをコールド移行します。

**インストールできる VMware ツールはありますか?**

CloudSimple はネイティブの VMware vSphere ソリューションを提供します。  オンプレミスの vSphere 環境を管理するために使用されるツールはすべて CloudSimple で使用できます。  CloudSimple では、VMware ツールのインストールに関して、ライセンス持ち込み (BYOL) モデルをサポートしています。

**更新とアップグレードはどのように管理されますか?**

CloudSimple は、プライベート クラウドのすべてのインフラストラクチャ コンポーネントを、シームレスで中断のない方法で管理および更新します。  VMware またはインフラストラクチャ ベンダーによってリリースされた更新プログラムまたはセキュリティ パッチはすべて、CloudSimple による認定を受け次第、更新のためにスケジュールされます。

プライベート クラウドにインストールされているアプリケーションのアップグレードまたは更新を CloudSimple が実行することはありません。

## <a name="azure-integration"></a>Azure の統合

**どのような Azure サービスがサポートされていますか?**

CloudSimple は、Azure 上のサブスクリプションへの Azure ExpressRoute 接続を提供します。  サブスクリプションで実行されているすべてのサービスは、プライベート クラウドへのネットワーク接続を備え、プライベート クラウドに接続できます。  次に例を示します。

1. CloudSimple vCenter の ID ソースとしての **Azure Active Directory**
2. プライベート クラウドのバックアップ、イメージ、その他データを保存するための **Azure Storage**
3. **ハイブリッド アプリケーション** - パブリック クラウドとプライベート クラウドにまたがるアプリケーション アーキテクチャを作成できます。  たとえば、CloudSimple プライベート クラウド上のアプリケーション サーバーやデータベース サーバーにアクセスする Web サーバーを Azure に作成できます。
4. **Azure Monitor** と **Azure Security Center** - VMware 上で実行されているワークロードは、ログ記録、パフォーマンス メトリック、セキュリティ管理にこれらを使用できます。

**VMware テナントを Azure にマップする方法は?**

CloudSimple は、プライベート クラウド上の VMware VM を Azure portal から管理するための独自機能を提供します。  (必要なリソース制約を構成した) vCenter リソース プールは、グローバル管理者によってサブスクリプションにマップできます。  

**Azure ではどのようなライセンス特典を受けられますか?**

CloudSimple では、Azure Hybrid Usage Benefit (ハイブリッド利用特典) を利用してライセンス コストを最大 90% 節約できるため、Microsoft ライセンスへの投資が無駄にならず、他のクラウドに比べて TCO を削減できます。 さらに、Windows Server 2008 および Microsoft SQL Server 2008 用の拡張セキュリティ更新プログラムを入手できます。  クラウドへのライセンス持ち込み (BYOL) により、Veeam、Zerto などの一般的なアプリのコストを抑えることができます。  