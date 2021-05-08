---
title: Azure Lab Services ラボ アカウント迅速設定ガイド
description: 管理者はこのガイドに従って、学校内で使用するラボ アカウントをすばやく作成できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669391"
---
# <a name="lab-account-setup-guide"></a>ラボ アカウント設定ガイド
管理者の場合は、Azure Lab Services 環境を設定する前に、まず、ご自分の Azure サブスクリプション内で "*ラボ アカウント*" を作成する必要があります。 ラボ アカウントは 1 つまたは複数のラボに使用するコンテナーで、ほんの数分で設定できます。

このガイドには、次の 3 つのセクションが含まれます。
-  前提条件
-  ラボ アカウントの設定を計画する
-  ラボ アカウントの設定

## <a name="prerequisites"></a>前提条件
以降のセクションで、ラボ アカウントを設定する前に行う必要がある作業について説明します。


### <a name="access-your-azure-subscription"></a>ご自分の Azure サブスクリプションにアクセスする
ラボ アカウントを作成するには、学校用に既に設定されている Azure サブスクリプションにアクセスする必要があります。 学校は 1 つ以上のサブスクリプションを所有している場合があります。 サブスクリプションを使用して、ラボ アカウントなど、すべてのAzure リソースとサービスの課金とセキュリティを管理します。  Azure サブスクリプションは、通常、IT 部門によって管理されています。  詳細については、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#subscription)」の「サブスクリプション」セクションを参照してください。

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>必要な VM 数と VM サイズを見積もる
学校のラボで必要とされる[仮想マシン (VM) の数と VM サイズ](./administrator-guide.md#vm-sizing)を把握することが重要です。 

ラボとイメージを構築するためのガイダンスについては、[物理ラボから Azure Lab Services への移行](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)に関するブログ記事をご覧ください。

ラボを構成する方法に関するその他のガイダンスについては、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#lab)」の「ラボ」セクションを参照してください。

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>サブスクリプションの VM の制限とリージョンの VM の容量を理解する
ラボの VM 数と VM サイズの見積もりが完了したら、次のことを行う必要があります。

- ラボで使用する予定の VM の数と VM のサイズが、Azure サブスクリプションの容量制限の範囲内であることを確認します。
- 利用可能な VM 容量が十分あるリージョン内にラボ アカウントを作成します。

詳細については、[VM サブスクリプションの制限とリージョンの容量](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)に関するページをご覧ください。

### <a name="decide-how-many-lab-accounts-to-create"></a>作成するラボ アカウントの数を決定する

すぐに開始するには、独自のリソース グループ内に 1 つのラボ アカウントを作成します。  後で、必要に応じて、追加のラボ アカウントとリソース グループを作成できます。 たとえば、コストを明確に区別する手段として、部門ごとに 1 つのラボ アカウントとリソース グループを作成することができます。 

ラボ アカウント、リソース グループ、およびコストの分離の詳細については、以下を参照してください。
- 「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#resource-group)」の「リソース グループ」セクション
- 「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#lab-account)」の「ラボ アカウント」セクション 
- [Azure Lab Services のコスト管理](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>ラボ アカウントの設定を計画する

ラボ アカウントの設定を計画するには、以下の質問を検討します。

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>ラボ アカウントの所有者と共同作成者を誰にするべきか?

通常、学校の IT 管理者がラボ アカウントの所有者と共同作成者のロールを引き受けます。 これらのロールは、ラボ アカウントのすべてのラボに適用されるポリシーを管理する責任があります。 ラボ アカウントの作成者は自動的に所有者になります。 サブスクリプションに関連付けられている Azure Active Directory (Azure AD) テナントから、所有者と共同作成者を追加できます。 

ラボ アカウントの所有者と共同作成者のロールの詳細については、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#manage-identity)」の「ID の管理」セクションを参照してください。

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

ラボ ユーザーには、Azure Lab Services 内の Azure AD テナント全体にアクセスできる VM のリストが 1 つだけ表示されます。

### <a name="who-will-be-allowed-to-create-labs"></a>誰にラボの作成を許可するか?

IT チームまたは教職員のメンバーにラボを作成させることを選択できます。 ラボを作成するには、これらのメンバーをラボ アカウント内のラボ作成者ロールに割り当てます。 通常、このロールは、学校のサブスクリプションに関連付けられている Azure AD テナントから割り当てます。 ラボの作成者が、ラボの所有者として自動的に割り当てられます。  

ラボ作成者ロールの詳細については、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#manage-identity)」の「ID の管理」セクションを参照してください。

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>誰にラボの所有と管理を許可するか?

また、IT や教職員のメンバーに、ラボの作成を許可すること "*なく*"、ラボを所有して管理できるようにすることもできます。  この場合、サブスクリプションの Azure AD テナントのユーザーには、既存のラボの所有者または共同作成者が割り当てられます。  

ラボ所有者と共同作成者のロールの詳細については、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#manage-identity)」の「ID の管理」セクションを参照してください。

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>イメージを保存してラボ間で共有するか?

Shared Image Gallery は、イメージの保存と共有に使用できるサービスです。 複数のクラスで同じイメージを使用する必要がある場合、ラボ作成者は、イメージを作成して共有イメージ ギャラリーにエクスポートできます。  イメージを共有イメージ ギャラリーにエクスポートしたら、新しいラボの作成に使用できます。

イメージを物理環境で作成して、共有イメージ ギャラリーにインポートすることもできます。 詳細については、[共有イメージ ギャラリーへのカスタム イメージのインポート](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)に関するブログ記事をご覧ください。

Shared Image Gallery サービスを使用することを決定した場合は、共有イメージ ギャラリーを作成するか、ご自分のラボ アカウントにアタッチする必要があります。 共有イメージ ギャラリーはいつでもラボ アカウントにアタッチできるため、ここでは、この決定を延期できます。  

詳細については、次を参照してください。
- 「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#shared-image-gallery)」の「共有イメージ ギャラリー」セクション
- 「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#pricing)」の「価格」セクション

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Azure Marketplace のどのイメージをラボで使用するか?

Azure Marketplace には、有効にできる何百ものイメージが用意されており、ラボ作成者はそれらを使用してラボを作成できます。 イメージによっては、ラボに必要なすべてのものが最初から含まれている場合があります。 また、1 つのイメージを出発点として使用し、後からラボ作成者が追加のアプリケーションやツールなどをインストールして、それをカスタマイズすることもできます。

必要なイメージがわからない場合は、後で戻ってきて有効にしてもかまいません。 使用可能なイメージを確認する最善の方法は、最初にラボ アカウントを作成することです。 これにより、アクセスできるようになり、使用可能なイメージとその内容の一覧を確認できるようになります。  

詳細については、[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)方法に関するページを参照してください。
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>ラボ VM から他の Azure またはオンプレミスのリソースにアクセスできる必要があるか?

ラボ アカウントを設定するときに、ラボ アカウントを仮想ネットワークとピアリングすることもできます。  仮想ネットワークとラボ アカウントは両方とも同じリージョンに配置する必要があることにご注意ください。  仮想ネットワークとピアリングする必要があるかどうかを判断するには、以下のシナリオを検討します。

- **ライセンス サーバーへのアクセス**
  
   Azure Marketplace のイメージを使用する場合、オペレーティング システムのライセンスのコストは Lab Services の価格にバンドルされています。 ただし、オペレーティング システム自体のライセンスを提供する必要はありません。 インストールされる追加のソフトウェアとアプリケーションについては、必要に応じてライセンスを提供する必要があります。  ライセンス サーバーにアクセスするには:
   - オンプレミスのライセンス サーバーに接続することを選択できます。  オンプレミスのライセンス サーバーに接続するには、追加の設定が必要です。
   - もう 1 つのオプションは、Azure VM でホストするライセンス サーバーを作成するもので、よりすばやく設定できます。  この Azure VM は、ラボ アカウントとピアリングする仮想ネットワーク内に存在します。

- **ファイル共有やデータベースなどの他のオンプレミス リソースへのアクセス**

   通常、オンプレミス リソースへのアクセスを提供するために、サイト間仮想ネットワーク ゲートウェイを使用して、仮想ネットワークを作成します。 この種類の環境を設定するには、時間が余分にかかります。

- **仮想ネットワークの外部にある他の Azure リソースへのアクセス**

   仮想ネットワーク内のセキュリティ保護 "*されていない*" Azure リソースにアクセスする必要がある場合は、パブリック インターネット経由でアクセスでき、ピアリングを行う必要はありません。

   仮想ネットワークの詳細については、次を参照してください。
   - 「[Azure Lab Services のアーキテクチャの基礎](./classroom-labs-fundamentals.md#virtual-network)」の「仮想ネットワーク」セクション
   - [Azure Lab Services でラボのネットワークとピア仮想ネットワークを接続する](./how-to-connect-peer-virtual-network.md)
   - [Azure Lab Services で共有リソースを使用してラボを作成する](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>ラボ アカウントの設定

計画が終わったら、ラボ アカウントを設定することができます。 同じ手順を適用して、[Teams 内の Azure Lab Services](./lab-services-within-teams-overview.md) を設定できます。

1. **ラボ アカウントを作成します**。 手順については、「[ラボ アカウントを作成する](./tutorial-setup-lab-account.md#create-a-lab-account)」を参照してください。
   
    名前付け規則の詳細については、「[Azure Lab Services - 管理者ガイド](./administrator-guide.md#naming)」の「名前を付ける」セクションを参照してください。

1. **ユーザーをラボ作成者ロールに追加します**。 手順については、「[ユーザーをラボの作成者ロールに追加する](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」を参照してください。

1. **ピア仮想ネットワークに接続します**。 手順については、[ラボのネットワークとピア仮想ネットワークの接続](./how-to-connect-peer-virtual-network.md)に関するページをご覧ください。

   [ラボ VM のアドレス範囲を構成する](./how-to-configure-lab-accounts.md)方法に関する手順も参照することが必要な場合があります。

1. **イメージを有効にして確認します**。 手順については、「[ラボ作成者が利用できる Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

   Azure Marketplace の各イメージの内容を確認するには、イメージ名を選択します。 たとえば、次のスクリーンショットは Ubuntu Data Science VM イメージの詳細を示したものです。

   ![Azure Marketplace で確認できるイメージの一覧のスクリーンショット。](./media/setup-guide/review-marketplace-images.png)

   共有イメージ ギャラリーがラボ アカウントにアタッチされていて、カスタム イメージをラボ作成者が共有できるようにする場合は、次のスクリーンショットで示されているように、同様の手順を完了します。

   ![共有イメージ ギャラリー内の有効化されたカスタム イメージの一覧のスクリーンショット。](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>次のステップ

ラボの設定と管理の詳細については、以下を参照してください。

- [ラボ アカウントの管理](how-to-manage-lab-accounts.md)  
- [ラボ設定ガイド](setup-guide.md)
