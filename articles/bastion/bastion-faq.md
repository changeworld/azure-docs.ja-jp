---
title: Azure Bastion に関する FAQ | Microsoft Docs
description: Azure Bastion に関してよく寄せられる質問について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: cherylmc
ms.openlocfilehash: 74a1093f8a084360669c5b436fa56d6ea7aa33d8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494579"
---
# <a name="azure-bastion-faq"></a>Azure Bastion に関する FAQ

## <a name="faqs"></a>FAQ

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Azure Bastion 経由で接続するうえで、自分の仮想マシンにパブリック IP は必要ですか?

いいえ。 Azure Bastion を使用して VM に接続するときは、接続先となる Azure 仮想マシンのパブリック IP は必要ありません。 Bastion サービスは、ご使用の仮想ネットワーク内で、お客様の仮想マシンのプライベート IP 経由でお客様の仮想マシンへの RDP または SSH セッションや接続を開きます。

### <a name="is-ipv6-supported"></a>IPv6 はサポートされていますか?

現時点では、IPv6 はサポートされていません。 Azure Bastion は、IPv4 のみをサポートしています。 したがって、Bastion リソースには IPv4 パブリック IP アドレスのみ割り当てることができ、Bastion を使用して IPv4 ターゲット VM に接続できます。 また、Bastion を使用してデュアル スタック ターゲット VM に接続できますが、IPv4 トラフィックは Azure Bastion 経由でのみ送受信できます。 

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Azure プライベート DNS ゾーンで Azure Bastion を使用することはできますか?

ターゲット リソースに正常に接続するには、Azure Bastion に特定の内部エンドポイントとの通信が確立されている必要があります。 そのため、選択したゾーン名がそれらの内部エンドポイントの名前と重複しない限り、Azure プライベート DNS ゾーンで Azure Bastion を使用 "*できます*"。 Azure Bastion リソースをデプロイする前に、名前に次が入っているプライベート DNS ゾーンに、ホストの仮想ネットワークがリンクされていないことを確認してください。
* core.windows.net
* azure.com

プライベート エンドポイントが統合された Azure プライベート DNS ゾーンを使用している場合、複数の Azure サービスの[推奨される DNS ゾーン名](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration)が上に表示されている名前と重複することに注意してください。 Azure Bastion の使用は、これらの設定ではサポート "*されていません*"。

また、Azure Bastion は各国のクラウド内の Azure プライベート DNS ゾーンでもサポートされていません。

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RDP または SSH クライアントは必要ですか?

いいえ。 ご使用の Azure portal 内でお客様の Azure 仮想マシンに RDP または SSH アクセスするために RDP または SSH クライアントは必要ありません。 [Azure portal](https://portal.azure.com) を使用して、ブラウザー内で仮想マシンに直接 RDP または SSH アクセスすることができます。

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Azure 仮想マシン内で実行するエージェントは必要ですか?

いいえ。 ご使用のブラウザーやお客様の Azure 仮想マシンにエージェントやソフトウェアをインストールする必要はありません。 Bastion サービスはエージェントレスのため、RDP や SSH 用の追加のソフトウェアを必要としません。

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP セッションではどのような機能がサポートされていますか?

現時点では、テキストのコピーと貼り付けのみがサポートされています。 ファイルのコピーなどの機能はサポートされていません。 [Azure Bastion フィードバック ページ](https://feedback.azure.com/d365community/forum/8ae9bf04-8326-ec11-b6e6-000d3a4f0789?c=c109f019-8326-ec11-b6e6-000d3a4f0789)で、新機能に関するフィードバックをぜひお寄せください。

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Bastion によるセキュリティ強化は、AADJ VM 拡張機能に参加している VM で動作しますか?

この機能は、Azure AD ユーザーを使用して AADJ VM 拡張機能に参加しているマシンでは動作しません。 詳細については、[Windows Azure VM と Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements) に関するページをご覧ください。

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>どのブラウザーがサポートされていますか?

ブラウザーは HTML 5 をサポートしている必要があります。 Windows では Microsoft Edge ブラウザーまたは Google Chrome をご使用ください。 Apple Mac では、Google Chrome ブラウザーをご使用ください。 Microsoft Edge Chromium も Windows と Mac の両方でサポートされます。

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>料金はどのようになっていますか?

詳細については、 [価格に関するページ](https://aka.ms/BastionHostPricing)を参照してください。

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>顧客データは Azure Bastion によってどこに格納されますか?

Azure Bastion によって、顧客データがデプロイされているリージョン外に移動または格納されることはありません。

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>仮想マシンにアクセスするには、なんらかのロールが必要ですか?

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール。
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
* Azure Bastion リソースに対する閲覧者ロール。
* ターゲット仮想マシンの Virtual Network での閲覧者ロール (Bastion がピアリングされた仮想ネットワーク内にある場合)。

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion には、Azure でホストされる VM を管理する目的で RDS CAL が必要ですか?

いいえ、管理の目的にのみ使用する場合、Azure Bastion による Windows Server VM へのアクセスに [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) は必要ありません。

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion リモート セッション中はどのキーボード レイアウトがサポートされますか?

現在、VM 内では次のキーボード レイアウトが、Azure Bastion でサポートされています。
* en-us-qwerty
* en-gb-qwerty
* de-ch-qwertz
* de-de-qwertz
* fr-be-azerty
* fr-fr-azerty
* fr-ch-qwertz
* hu-hu-qwertz
* it-it-qwerty
* ja-jp-qwerty
* pt-br-qwerty
* es-es-qwerty
* es-latam-qwerty
* sv-se-qwerty
* tr-tr-qwerty

ターゲット言語の適切なキー マッピングを確立するには、ローカル コンピューターの言語、またはターゲット VM 内の言語を英語 (米国) に設定する必要があります。 つまり、ターゲット VM 言語がターゲット言語に設定されている場合、またはその逆の場合、ローカル コンピューターの言語は英語 (米国) に設定する必要があります。 コンピューター設定で、マシンに英語 (米国) 言語を追加できます。

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>Azure Bastion では、ターゲット VM のタイムゾーンの構成やタイムゾーンのリダイレクトはサポートされていますか?

現在、Azure Bastion ではタイムゾーンのリダイレクトはサポートされておらず、タイムゾーンは構成できません。

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion サブネットでユーザー定義ルーティング (UDR) はサポートされますか?

いいえ。 Azure Bastion サブネットで UDR はサポートされません。

Azure Bastion と Azure Firewall/ネットワーク仮想アプライアンス (NVA) の両方が同じ仮想ネットワークに存在するシナリオでは、Azure Bastion と VM との間の通信はプライベートであるため、Azure Bastion サブネットから Azure Firewall にトラフィックを強制する必要がありません。 詳細については、[Azure Firewall と Bastion の内側の VM にアクセスする方法](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)に関するページを参照してください。

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a> Basic SKU から Standard SKU にアップグレードできますか?

はい。 手順については、「[SKU のアップグレード](upgrade-sku.md)」を参照してください。 SKU の詳細については、[構成設定](configuration-settings.md#skus)に関する記事を参照してください。

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a> Standard SKU から Basic SKU にダウングレードできますか?

いいえ。 Standard SKU から Basic SKU へのダウングレードはサポートされていません。 SKU の詳細については、[構成設定](configuration-settings.md#skus)に関する記事を参照してください。

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a> 私が使用している Azure Bastion サブネットに複数の Azure リソースをデプロイできますか?

いいえ。 Azure Bastion サブネット (*AzureBastionSubnet*) は、Azure Bastion リソースのデプロイのためにのみ予約されています。

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Bastion セッションの開始前に "セッションの有効期限が切れています" というエラー メッセージを受け取るのはなぜですか?

セッションは Azure portal からのみ開始される必要があります。 もう一度 Azure portal にサインインして、自分のセッションを開始してください。 別のブラウザー セッションまたはタブから直接 URL に移動する場合に、このエラーが発生します。 これは、自分のセッションがより安全で、セッションが Azure portal からのみアクセスできることを確保するのに役立ちます。

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>デプロイ エラーはどのように処理しますか?

エラー メッセージを確認し、必要に応じて [Azure portal 内でサポート リクエストを送信](../azure-portal/supportability/how-to-create-azure-support-request.md)してください。 [Azure サブスクリプションの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)によって、デプロイ エラーが発生する可能性があります。 特に、サブスクリプションごとに許可されるパブリック IP アドレスの数に対する制限が発生する可能性があります。これは、Azure Bastion のデプロイが失敗する原因となります。

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>ディザスター リカバリー プランに対し、どのようにして Azure Bastion を組み込めばよいでしょうか?

Azure Bastion は、VNet またはピアリングされた VNet 内にデプロイされて、Azure リージョンに関連付けられます。 Azure Bastion は、お客様がディザスター リカバリー (DR) サイトの VNet にデプロイすることになります。 万一 Azure リージョンで障害が発生した場合は、その DR リージョンに対して VM のフェールオーバー操作を実行してください。 その後、DR リージョンにデプロイした Azure Bastion ホストを使用し、そこにデプロイされている VM に接続します。

## <a name="vnet-peering"></a><a name="peering"></a>VNet ピアリング

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>ピアリングされた仮想ネットワークで複数の Bastion ホストをデプロイすることはできますか?

はい。 既定では、ユーザーには、VM が存在している同じ仮想ネットワークにデプロイされている Bastion ホストが表示されます。 ただし、 **[接続]** メニューには、ピアリングされたネットワークで検出された複数の Bastion ホストが表示されます。 仮想ネットワークにデプロイされている VM への接続に使用する Bastion ホストを選択できます。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>ピアリングされた VNet が別のサブスクリプションにデプロイされている場合、Bastion 経由の接続は機能しますか?

はい、Bastion 経由の接続は、1 つのテナントの異なるサブスクリプション間でピアリングされた Vnet に対して引き続き機能します。 2 つの異なるテナントにまたがるサブスクリプションはサポートされていません。 **[接続]** ドロップダウン メニューにある Bastion を表示するには、 **[サブスクリプション] > [グローバル サブスクリプション]** で、アクセス権を持つサブスクリプションを選択する必要があります。

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="グローバル サブスクリプション フィルター。" lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="does-bastion-support-connectivity-to-azure-virtual-desktop"></a>Bastion は Azure Virtual Desktop への接続をサポートしていますか?
いいえ。Azure Virtual Desktop への Bastion 接続はサポートされていません。

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>ピアリングされた VNet へのアクセス権を持っているにも関わらず、デプロイされている VM が表示されません。

ユーザーが VM とピアリングされた VNet の両方への **読み取り** アクセスを持っていることを確認します。 さらに、ユーザーが次のリソースへの **読み取り** アクセスを持っていることを IAM で確認します。

* 仮想マシンに対する閲覧者ロール。
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
* Azure Bastion リソースに対する閲覧者ロール。
* Virtual Network の閲覧者ロール (ピアリングされた仮想ネットワークがない場合は不要)。

|アクセス許可|説明|アクセス許可の種類|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |踏み台ホストを取得します|アクション|
|Microsoft.Network/virtualNetworks/BastionHosts/action |仮想ネットワーク内の踏み台ホストの参照を取得します。|アクション|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|仮想ネットワーク内の踏み台ホストの参照を取得します。|アクション|
|Microsoft.Network/networkInterfaces/read|ネットワーク インターフェイスの定義を取得します。|アクション|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|ネットワーク インターフェイスの IP 構成定義を取得します。|アクション|
|Microsoft.Network/virtualNetworks/read|仮想ネットワークの定義を取得します。|アクション|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|仮想ネットワーク サブネットのすべての仮想マシンへの参照を取得します。|アクション|
|Microsoft.Network/virtualNetworks/virtualMachines/read|仮想ネットワークのすべての仮想マシンへの参照を取得します。|アクション|

### <a name="what-is-the-maximum-screen-resolution-supported-via-bastion"></a>Bastion でサポートされる最大画面解像度は何ですか?  
現在、サポートされている最大解像度は 1920x1080 (1080p) です。 
