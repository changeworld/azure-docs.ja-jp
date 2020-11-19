---
title: Azure Lab Services ラボ アカウント迅速設定ガイド
description: 管理者はこのガイドに従って、学校内で使用するラボ アカウントをすばやく作成できます。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659864"
---
# <a name="lab-account-setup-guide"></a>ラボ アカウント設定ガイド
Azure Lab Services の環境を設定するには、管理者は最初に、自分の Azure サブスクリプション内で **ラボ アカウント** を設定する必要があります。 ラボ アカウントはラボ用のコンテナーであり、設定には数分しかかかりません。

このガイドには、次の 3 つのセクションが含まれます。
-  最初のセクションでは、ラボ アカウントを設定する "*前に*" 完了する必要がある前提条件について説明します。
-  2 番目のセクションでは、ラボ アカウントの設定の計画に関するガイダンスを示します。
-  3 番目のセクションでは、ラボ アカウントを設定する詳細な手順について説明します。

## <a name="prerequisites-for-setting-up-your-lab-account"></a>ラボ アカウントを設定するための前提条件
このセクションでは、ラボ アカウントを設定する前に済ましておく必要がある前提条件の概要を説明します。

### <a name="obtain-an-azure-subscription"></a>Azure サブスクリプションを取得する
ラボ アカウントを作成するには、学校用に設定されている Azure サブスクリプションにアクセスする必要があります。 学校は 1 つ以上のサブスクリプションを所有している場合があります。 サブスクリプションを使用して、ラボ アカウントなど、すべてのAzure リソースとサービスの課金とセキュリティを管理します。  Azure サブスクリプションは、通常、IT 部門によって管理されます。  詳細については、次のトピックを参照してください。
 - [管理者ガイド - サブスクリプション](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>必要な VM の数と VM のサイズを推定する
学校で必要な仮想マシン (VM) の数と [VM のサイズ](./administrator-guide.md#vm-sizing)を見積もる必要があります。  ラボとイメージを構築する方法のガイダンスについては、次のブログ記事をお読みください。  このブログ記事は、必要な VM の数と VM のサイズを決定するのにも役立ちます。
- [物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

また、ラボの構成方法に関するその他のガイダンスが説明されている次の記事も参照してください。
- [管理者ガイド - ラボ](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>サブスクリプションの VM の制限とリージョンの VM の容量を理解する
ラボの VM の数とサイズの見積もりが完了したら、次のことを行う必要があります。

- ラボで使用する予定の VM の数と VM のサイズが、Azure サブスクリプションの容量制限の範囲内であることを確認します。

- 十分な VM 容量を使用できるリージョン内にラボ アカウントを作成します。

詳細については、次のブログ記事をご覧ください。[VM サブスクリプションの制限とリージョンの容量](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)。

### <a name="decide-how-many-lab-accounts-to-create"></a>作成するラボ アカウントの数を決定する

すぐに開始するには、独自のリソース グループ内に 1 つのラボ アカウントを作成します。  後で、必要に応じて追加のラボ アカウント (およびリソース グループ) を作成できます。 たとえば、コストを明確に区別する手段として、部門ごとに 1 つのラボ アカウントとリソース グループを作成することができます。  ラボ アカウント、リソース グループ、およびコストの分離の詳細については、次の記事を参照してください。
- [管理者ガイド - リソース グループ](./administrator-guide.md#resource-group)
- [管理者ガイド - ラボ アカウント](./administrator-guide.md#lab-account) 
- [Azure Lab Services のコスト管理](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>ラボ アカウントの設定を計画する

ラボ アカウントの設定を計画するには、以下の質問を検討する必要があります。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>ラボアカウントの所有者と共同作成者を誰にするべきか?

   通常は、学校の IT 管理者がラボ アカウントの所有者と共同作成者になります。 所有者と共同作成者は、ラボ アカウントに含まれるすべてのラボに適用されるポリシーを管理する責任があります。 ラボ アカウントの作成者は自動的に所有者になります。 サブスクリプションに関連付けられている Azure Active Directory (AD) テナントから、所有者と共同作成者を追加できます。 ラボ アカウントの所有者ロールと共同作成者ロールの詳細については、次を参照してください。
   -  [管理者ガイド - ID を管理する](./administrator-guide.md#manage-identity)。

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   ラボ ユーザーには、Azure Lab Services 内のテナント全体にアクセスできる仮想マシンのリストが 1 つだけ表示されます。

### <a name="who-will-be-allowed-to-create-labs"></a>誰にラボの作成を許可するか?

   IT や教職員のメンバーにラボを作成させることを選択できます。 ユーザーがラボを作成すると、ラボの所有者として自動的に割り当てられます。  ラボを作成するためには、ユーザーは (通常は、サブスクリプションに関連付けられている Azure AD テナントから)、ラボ アカウント内のラボ作成者ロールに割り当てられる必要があります。  ラボ作成者ロールの詳細については、次を参照してください。
   -  [管理者ガイド - ID を管理する](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>誰にラボの所有と管理を許可するか?

   また、IT や教職員のメンバーに、ラボの作成を許可すること "*なく*"、ラボを所有して管理できるようにすることもできます。  この場合、サブスクリプションの Azure AD テナントのユーザーには、既存のラボの所有者または共同作成者が割り当てられます。  ラボの所有者および共同作成者ロールの詳細については、次を参照してください。
   - [管理者ガイド - ID を管理する](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>ラボ間で共有可能なイメージを保存するか?
Shared Image Gallery は、イメージの保存と共有に使用できるリポジトリです。 複数のクラスで同じイメージが必要な場合、ラボ作成者はイメージを作成した後、それを Shared Image Gallery にエクスポートできます。  イメージが Shared Image Gallery にエクスポートされると、それを使用して新しいラボを作成できます。

また、物理環境でイメージを作成してから、Shared Image Gallery にインポートすることもできます。  このプロセスの詳細については、次のブログ記事を参照してください。 
- [カスタム イメージを Shared Image Gallery にインポートする](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Shared Image Gallery を使用する必要がある場合は、ラボ アカウントに Shared Image Gallery を作成するかアタッチする必要があります。 また、ラボ アカウントへのアタッチはいつでもできるので、この決定は後で行ってもかまいません。  Shared Image Gallery の詳細については、次を参照してください。
- [管理者ガイド - Shared Image Gallery](./administrator-guide.md#shared-image-gallery)
- [管理者ガイド - Shared Image Gallery の価格](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Azure Marketplace のどのイメージをラボで使用するか?
Azure Marketplace には、有効にできる何百ものイメージが用意されており、ラボ作成者はイメージを使用してラボを作成できます。 イメージによっては、ラボに必要なすべてのものが最初から含まれている場合があります。 出発点としてイメージを使用し、後からラボ作成者が追加のアプリケーションやツールなどをインストールしてイメージをカスタマイズできる場合もあります。

必要なイメージがわからない場合は、後で戻ってきて有効にしてもかまいません。 また、使用可能なイメージを確認する最善の方法は、最初にラボ アカウントを作成することです。 これにより、アクセスできるようになり、使用可能なイメージとその内容の一覧を確認できるようになります。  Marketplace イメージの詳細については、次を参照してください。
- [ラボ作成者が利用できる Marketplace イメージを指定する](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>ラボの VM で他の Azure またはオンプレミスのリソースにアクセスできる必要があるか?
ラボ アカウントを設定するときに、ラボ アカウントと仮想ネットワーク (VNet) をピアリングすることもできます。  VNet とラボ アカウントは両方とも同じリージョンに配置する必要があることに注意してください。  VNet とピアリングする必要があるかどうかを判断するには、以下のシナリオを検討します。

- **ライセンス サーバーへのアクセス**
  
   Azure Marketplace のイメージを使用する場合、オペレーティング システムのライセンスのコストは Lab Services の価格にバンドルされています。 ただし、オペレーティング システム自体のライセンスを提供する必要はありません。 ただし、インストールする追加のソフトウェアやアプリケーションについては、必要に応じてライセンスを提供する必要があります。  ライセンス サーバーにアクセスするには:
   - オンプレミスのライセンス サーバーに接続することを選択できます。  オンプレミスのライセンス サーバーに接続するには、追加の設定が必要です。
   - より早く設定できるもう 1 つのオプションは、Azure VM でホストするライセンス サーバーを作成することです。  Azure VM は、ラボ アカウントにピアリングする仮想ネットワーク内に存在します。

- **ファイル共有やデータベースなどの他のオンプレミス リソースへのアクセス**

   オンプレミス リソースへのアクセスを提供するために、通常はサイト間仮想ネットワーク ゲートウェイを使用して、VNet を作成します。 この種類の環境を設定するには、時間が余分にかかります。

- **VNet の外部にある他の Azure リソースへのアクセス**

   VNet 内でセキュリティ保護されて "*いない*" Azure リソースにアクセスする必要がある場合、ピアリングを行わずにパブリック インターネット経由でこれらのリソースにアクセスできます。

仮想ネットワークの詳細については、以下を参照してください。
- [アーキテクチャの基礎 - 仮想ネットワーク](./classroom-labs-fundamentals.md#virtual-network)
- [仮想ネットワークに接続する方法](./how-to-connect-peer-virtual-network.md)
- [Azure Lab Services で共有リソースを使用してラボを作成する方法](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>ラボ アカウントの設定

計画が終わったら、ラボ アカウントを設定することができます。  これらと同じ手順を使用して、[Teams で Azure Lab Services](./lab-services-within-teams-overview.md) のラボを設定することもできます。

1. **ラボ アカウントを作成します。** 手順については、[ラボ アカウントの作成](./tutorial-setup-lab-account.md#create-a-lab-account)に関するチュートリアルを参照してください。
   
    名前付けの詳細については、次の記事を参照してください。

   - [リソースの名前付けのガイダンス](./administrator-guide.md#naming)

2. **ユーザーをラボの作成者ロールに追加します。** 手順については、[ラボの作成者ロールへのユーザーの追加](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)に関する記事を参照してください。


3. **ピア仮想ネットワークに接続します。** 手順については、[ラボのネットワークとピア仮想ネットワークの接続](./how-to-connect-peer-virtual-network.md)に関する記事をご覧ください。

   [ラボ VM のアドレス範囲を構成する](./how-to-configure-lab-accounts.md)手順も参照することが必要な場合があります。

4. **イメージを有効にして確認します。** 手順については、[ラボ作成者向けの Azure Marketplace イメージの有効化](./specify-marketplace-images.md)に関する記事をご覧ください。

   Azure Marketplace の各イメージの内容を確認するには、イメージ名を選択します。 たとえば、次のスクリーンショットは Ubuntu Data Science VM イメージの詳細を示したものです。

   ![Azure Marketplace イメージの確認のスクリーンショット](./media/setup-guide/review-marketplace-images.png)

   ラボ アカウントに Shared Image Gallery がアタッチされていて、カスタム イメージをラボ作成者が共有できるようにする場合は、次のスクリーンショットで示されているように、同様の手順を完了します。

   ![共有イメージ ギャラリーでのカスタム イメージの有効化のスクリーンショット](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>次のステップ

ラボ環境のセットアップに共通する次の手順:

- [ラボ アカウントの管理](how-to-manage-lab-accounts.md)
- [ラボ設定ガイド](setup-guide.md)