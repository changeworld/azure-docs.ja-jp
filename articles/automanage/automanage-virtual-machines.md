---
title: Azure Automanage for virtual machines
description: Azure Automanage for virtual machines について説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: b93169ced916f5d16adcbd11fcc2d2217a07b643
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451181"
---
# <a name="preview-azure-automanage-for-machine-best-practices"></a>プレビュー: Azure Automanage for machine のベスト プラクティス

この記事では、Azure Automanage for machine のベスト プラクティスについて説明します。これには次のような利点があります。

- 仮想マシンをインテリジェントにオンボードして、ベスト プラクティスの Azure サービスを選択する
- Azure のベスト プラクティスに従って、各サービスを自動的に構成する
- ベスト プラクティス サービスのカスタマイズをサポートします
- ドリフトを監視し、検出されたら修正する
- 簡単なエクスペリエンス (ポイント、クリック、設定、消去) を提供する

## <a name="overview"></a>概要

Azure Automanage for machine のベスト プラクティスは、Azure で仮想マシンに役に立つ特定のサービスを検出し、オンボード方法を知り、構成方法を知る必要がなくなくなるサービスです。 これらのサービスは、Azure のベスト プラクティス サービスと見なされ、仮想マシンの信頼性、セキュリティ、管理を強化するのに役立ちます。 サービスの例としては、[Azure Update Management](../automation/update-management/overview.md) や [Azure Backup](../backup/backup-overview.md) などがあります。

ご利用のマシンを Azure Automanage にオンボードすると、各ベスト プラクティス サービスが推奨される設定に自動的に構成されます。 ただし、ベスト プラクティスのサービスと設定をカスタマイズする場合は、[[カスタム プロファイル]](#custom-profiles) オプションを使用できます。 

また、Azure Automanage を使用すると、ドリフトが自動的に監視され、検出されると修正されます。 つまり、仮想マシンまたは Arc 対応サーバーが Azure Automanage にオンボードされている場合は、マシンを監視して、ライフサイクル全体にわたってその[構成プロファイル](#configuration-profile)に準拠し続ける必要があります。 仮想マシンがそのプロファイルからドリフトつまり逸脱した場合 (たとえば、サービスがオフボードされた場合) は、修正が行われ、マシンは望ましい状態に戻されます。

VM が配置されている geography 外で、Automanage によって顧客データが格納/処理されることはありません。 東南アジア リージョンでは、東南アジア外で、Automanage によってデータが格納/処理されることはありません。

> [!NOTE]
> Automanage は Azure 仮想マシン上に加え、Azure Arc 対応のサーバー上で有効にできます。 現在、Automanage は米国政府機関向けクラウドでは利用できません。

## <a name="prerequisites"></a>前提条件

仮想マシンで Azure Automanage を有効にする前に、いくつかの前提条件を考慮する必要があります。

- サポートされている [Windows Server のバージョン](automanage-windows-server.md#supported-windows-server-versions) と [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions)
- VM はサポート対象のリージョンに存在する必要があります (下記参照)
- ユーザーは正しいアクセス許可を持っている必要があります (下記参照)
- 現時点では、サンドボックス サブスクリプションは Automanage ではサポートされていません
- 現在のところ、Automanage では Windows 10 がサポートされていません

### <a name="supported-regions"></a>サポートされているリージョン
Automanage では、次のリージョンにある VM のみがサポートされます。
* 西ヨーロッパ
* 北ヨーロッパ
* 米国中部
* 米国東部
* 米国東部 2
* 米国西部
* 米国西部 2
* カナダ中部
* 米国中西部
* 米国中南部
* 東日本
* 英国南部
* オーストラリア東部
* オーストラリア南東部
* 東南アジア

### <a name="required-rbac-permissions"></a>必要な RBAC アクセス許可
オンボードするには、サブスクリプションで初めて Automanage を有効にするかどうかに応じて、Automanage には若干異なる RBAC ロールが必要です。

サブスクリプションで Automanage を初めて有効にする場合:
* ご利用のマシンを含むサブスクリプションの **[所有者]** ロール、"_**または**_"
* ご利用のマシンを含むサブスクリプションの **[共同作成者]** および **[ユーザー アクセス管理者]** ロール

Automanage マシンが既に存在するサブスクリプション内のマシンで Automanage を有効にする場合:
* ご利用のマシンを含むリソース グループの **[共同作成者]** ロール。

Automanage サービスは、このファースト パーティ アプリケーション (Automanage API アプリケーション ID: d828acde-4b48-47f5-a6e8-52460104a052) に **共同作成者** アクセス許可を付与し、自動管理されたマシンでアクションを実行します。

> [!NOTE]
> 別のサブスクリプションのワークスペースに接続されている VM で Automanage を使用する場合は、サブスクリプションごとに上記に記載されているアクセス許可を持っている必要があります。

## <a name="participating-services"></a>対象となるサービス

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="サービスをインテリジェントにオンボードする。":::

対象となる Azure サービスの完全な一覧と、サポートされているプロファイルについては、以下をご覧ください。
- [Linux 用 Automanage](automanage-linux.md)
- [Windows Server 用 Automanage](automanage-windows-server.md)

 ベスト プラクティス構成プロファイルを使用すると、これらの参加サービスに自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure portal で Azure Automanage for VMs を有効にする

Azure portal で、既存の仮想マシンで Automanage を有効にできます。 このプロセスの簡単な手順については、[Azure Automanage for virtual machines のクイックスタート](quick-create-virtual-machines-portal.md)に関するページを参照してください。

VM に対して Automanage を初めて有効にする場合は、Azure portal で **Automanage – Azure マシンのブスト プラクティス** を検索します。 **[Enable on existing VM]\(既存の VM で有効にする\)** をクリックし、使用する[構成プロファイル](#configuration-profile)を選択し、オンボードするマシンを選択します。 **[有効化]** をクリックしたら完了です。

これらのサービスを管理するためにこのマシンとの対話が必要になるのは、VM の自動修正が試みられて失敗した場合だけです。 VM の自動修正が正常に行われた場合は、コンプライアンス状態に戻り、お客様には何も通知されません。 詳細については、[VM の状態](#status-of-vms)に関するページをご覧ください。

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Azure Policy を使用して Automanage for VMs を有効にする
組み込みの Azure Policy を使用して、大規模な VM に Automanage を有効にすることもできます。 ポリシーには DeployIfNotExists 効果があります。これは、ポリシーのスコープ内にあるすべての対象 VM が、Automanage の VM のベスト プラクティスに自動的にオンボードされることを意味します。

ポリシーへの直接リンクは[こちら](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)にあります。

詳細については、[Automanage 組み込みポリシー](virtual-machines-policy-enable.md)を有効にする方法を確認してください。 

## <a name="configuration-profile"></a>構成プロファイル

マシンに対して Automanage を有効にするときは、構成プロファイルが必要です。 構成プロファイルは、このサービスの基礎となります。 それらには、ご利用のマシンがオンボードされるサービスと、それらのサービスのある程度の構成が定義されています。

### <a name="best-practice-configuration-profiles"></a>ベスト プラクティス構成プロファイル

現在、2 つのベスト プラクティス構成プロファイルを使用できます。

- **Dev/Test** プロファイルは、Dev/Test 用のマシン向けに設計されています。
- **運用** プロファイルは、実稼働向けです。

このような違いの理由は、実行されるワークロードに基づいて特定のサービスが推奨されるためです。 たとえば、運用マシンでは、Azure Backup に自動的にオンボードされます。 一方、Dev/Test マシンの場合は通常、ビジネスへの影響が少ないため、バックアップ サービスは不要なコストになります。

### <a name="custom-profiles"></a>カスタム プロファイル

カスタム プロファイルを使用すると、マシンに適用するサービスと設定をカスタマイズできます。 これは、IT 要件がベスト プラクティスと異なる場合に最適なオプションです。 たとえば、IT 組織で別のマルウェア対策ソリューションを使用する必要があるという理由で Microsoft Antimalware ソリューションを使用しない場合は、カスタム プロファイルを作成するときに Microsoft Antimalware を切り替えるだけでできます。

> [!NOTE]
> ベスト プラクティス Dev/Test 構成プロファイルでは、VM のバックアップはまったく行われません。

> [!NOTE]
> マシンの構成プロファイルを変更する場合は、目的の構成プロファイルを使用してマシンを再び開くだけでできます。 ただし、マシンの状態が "アップグレードが必要" の場合は、最初に無効にしてから Automanage を再び有効にする必要があります。 

対象となる Azure サービスの完全な一覧と、それらによって基本設定がサポートされているかどうかについては、次をご覧ください。
- [Linux 用 Automanage](automanage-windows-server.md)
- [Windows Server 用 Automanage](automanage-windows-server.md)


## <a name="status-of-vms"></a>VM の状態

Azure portal で、自動管理されているマシンがすべて表示されている「**Automanage - Azure マシンのベスト プラクティス**」ページに移動します。 ここには、各マシンの全体的な状態が表示されます。

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="構成済み仮想マシンの一覧。":::

表示されているコンピューターごとに、[名前]、[構成プロファイル]、[状態]、[リソースの種類]、[リソース グループ]、[サブスクリプション] の詳細が表示されます。

**[状態]** 列には、次の状態が表示されます。
- *[進行中]* - VM は有効にされたばかりで、構成が行われています
- *[適合]* - VM は構成されており、ドリフトは検出されていません
- *[Not conformant]* \(不適合\) - VM にはドリフトがあり、修復できなかったか、マシンの電源がオフになっています。Automanage は、次回の実行時に VM のオンボードまたは修復を試行します
- *[アップグレードが必要]* - VM は以前のバージョンの Automanage にオンボードされており、最新バージョンに[アップグレード](automanage-upgrade.md)する必要があります

**[Not conformant]\(不適合\)** という *状態* が表示された場合は、ポータルで状態をクリックし、表示されるトラブルシューティング リンクを使用して、トラブルシューティングを行うことができます。


## <a name="disabling-automanage-for-vms"></a>Automanage for VMs を無効にする

特定の VM について Automanage を無効にすることができます。 たとえば、マシンで非常に機密性の高いセキュリティで保護されたワークロードが実行されていて、Azure によって既定で行われるより厳しくロックダウンする必要があるため、Azure のベスト プラクティスの範囲外でマシンを構成する必要があるような場合です。

Azure portal でそれを行うには、自動管理されている VM がすべて表示される「**Automanage - Azure マシンのベスト プラクティス**」ページに移動します。 Automanage から無効にする仮想マシンの横にあるチェック ボックスをオンにして、 **[Disable automanagment]\(自動管理を無効にする\)** ボタンをクリックし ます。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

**[Disable]\(無効\)** に同意する前に、結果として表示されたポップアップのメッセージを注意深く確認します。

> [!NOTE]
> VM で自動管理を無効にすると、次の動作が発生します。
>
> - VM とそれにオンボードされているサービスの構成は変更されません。
> - それらのサービスによって発生する料金は引き続き課金されます。
> - Automanage のドリフトの監視は直ちに停止します。


何よりもまず、仮想マシンは、オンボードされて構成されているどのサービスからも、オフボードされることはありません。 そのため、それらのサービスによって発生する料金は引き続き課金されます。 必要な場合は、オフボードする必要があります。 Automanage のすべての動作は直ちに停止します。 たとえば、VM でドリフトは監視されなくなります。

## <a name="automanage-and-azure-disk-encryption"></a>Automanage と Azure Disk Encryption
Automanage は、Azure Disk Encryption (ADE) が有効になっている VM と互換性があります。

運用環境を使用する場合、Azure Backup にもオンボードされます。 ADE と Azure Backup を正しく使用するために、前提条件が 1 つあります。
* ADE が有効な VM を Automanage の運用環境にオンボードする前に、[こちらのドキュメント](../backup/backup-azure-vms-encryption.md#before-you-start)の「**開始する前に**」セクションに記載されている手順を実行済みであることを確認してください。

## <a name="next-steps"></a>次の手順

この記事では、マシン用の Automanage によって、Azure サービスを理解し、それにオンボードし、そのベスト プラクティスを構成する必要がなくなる手段が提供されることを説明しました。 また、Automanage for virtual machines に対してオンボードしたマシンが、構成プロファイルから外れた場合は、準拠するように自動的に戻されます。

Azure portal で、Azure 仮想マシンまたは Arc 対応サーバーの自動管理を有効にしてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)
