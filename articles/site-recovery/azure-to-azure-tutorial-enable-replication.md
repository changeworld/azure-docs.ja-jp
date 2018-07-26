---
title: Azure Site Recovery でセカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する
description: Azure Site Recovery サービスを使用して、別の Azure リージョンへの Azure VM のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: aaed3dd5a2a7b32d24aa8b19dab870c28e6f58ec
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216184"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>セカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのコンピューターと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、セカンダリ Azure リージョンへの Azure VM のディザスター リカバリーを設定する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Recovery Services コンテナーを作成する
> * ターゲット リソースの設定を確認する
> * VM の発信アクセスを設定する
> * VM のレプリケーションを有効にする


## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [シナリオのアーキテクチャとコンポーネント](concepts-azure-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-azure-to-azure.md)を確認する。

## <a name="create-a-vault"></a>コンテナーの作成

任意のリージョン (ソース リージョンを除く) にコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]** > **[監視 + 管理]** > **[Backup and Site Recovery]** の順にクリックします。
3. **[名前]** に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、適切なものを選択します。
4. リソース グループを作成するか、既存のリソース グループを選択します。 Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]**、**[作成]** の順にクリックします。

   ![新しいコンテナー](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ページに追加されます。

## <a name="verify-target-resources"></a>ターゲット リソースの確認

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery によって、ターゲット VM に対して同じサイズまたは最も近いサイズが選択されます。

## <a name="configure-outbound-network-connectivity"></a>発信ネットワーク接続の構成

Site Recovery が期待どおりに動作するには、レプリケートしたい VM から、送信ネットワークの接続に変更を加える必要があります。

- Site Recovery では、ネットワーク接続を制御するための認証プロキシの使用をサポートしていません。
- 認証プロキシがある場合、レプリケーションを有効にできません。

### <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、Site Recovery によって使用される次の URL へのアクセスを許可します。

| **URL** | **詳細** |
| ------- | ----------- |
| *.blob.core.windows.net | ソース リージョンのキャッシュ ストレージ アカウントに、VM からデータが書き込まれるよう許可します。 |
| login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証を提供します。 |
| *.hypervrecoverymanager.windowsazure.com | VM と Site Recovery サービスの通信を許可します。 |
| *.servicebus.windows.net | VM による Site Recovery の監視および診断データの書き込みを許可します。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

URL の代わりに IP アドレスを使用して送信接続を制御する場合は、適切なデータセンターの範囲、Office 365 のアドレス、サービス エンドポイント アドレスを、IP ベースのファイアウォール、プロキシ、または NSG ルールのホワイトリストに登録します。

  - [Microsoft Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [ドイツの Windows Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [中国の Windows Azure データセンターの IP 範囲](http://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL および IP アドレス範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery サービス エンドポイントの IP アドレス](https://aka.ms/site-recovery-public-ips)

こちらの[スクリプト](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)を使用して、必要な NSG ルールを作成することができます。

## <a name="verify-azure-vm-certificates"></a>Azure VM の証明書の確認

レプリケートする Windows または Linux VM に、最新のルート証明書がすべて存在することを確認します。 最新のルート証明書がない場合、セキュリティの制約のため、VM を Site Recovery に登録できません。

- Windows VM については、最新のすべての Windows 更新プログラムを VM にインストールして、すべての信頼されたルート証明書をマシンに用意します。 接続されていない環境の場合は、組織の標準の Windows Update プロセスおよび証明書更新プロセスに従ってください。

- Linux VM の場合は、Linux ディストリビューターから提供されるガイダンスに従って、VM で最新の信頼されたルート証明書と証明書失効リストを取得します。

## <a name="set-permissions-on-the-account"></a>アカウントのアクセス許可の設定

Azure Site Recovery には、Site Recovery の管理操作を制御するための組み込みロールが 3 つ用意されています。

- **Site Recovery 共同作成者** - このロールには、Recovery Services コンテナーでの Azure Site Recovery の操作の管理に必要なすべてのアクセス許可があります。 ただし、このロールを持つユーザーは、Recovery Services コンテナーの作成や削除、または他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、アプリケーションまたは組織全体のディザスター リカバリーを有効にして管理できる、ディザスター リカバリー管理者に最も適しています。

- **Site Recovery オペレーター** - この役割には、フェールオーバーとフェールバックの操作を実行して管理するアクセス許可があります。 このロールのユーザーは、レプリケーションの有効化または無効化、コンテナーの作成または削除、新しいインフラストラクチャの登録、他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、アプリケーション所有者および IT 管理者から指示されて、仮想マシンまたはアプリケーションをフェールオーバーできる、ディザスター リカバリー オペレーターに最も適しています。 障害が解決された後、DR オペレーターは仮想マシンを再び保護してフェールバックを行うことができます。

- **Site Recovery 閲覧者** - このロールには、すべての Site Recovery 管理操作を見るアクセス許可があります。 このロールは、保護の現在の状態を監視し、サポート チケットを発行できる、IT 監視担当役員に最も適しています。

[Azure RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)について説明します。

## <a name="enable-replication"></a>レプリケーションを有効にする

### <a name="select-the-source"></a>ソースを選択する

1. [Recovery Services コンテナー] で、[コンテナー名] > **[+ レプリケート]** の順にクリックします。
2. **[ソース]** で **[Azure]** を選択します。
3. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
4. VM の **Azure 仮想マシンのデプロイ モデル**として、**[Resource Manager]** または **[クラシック]** を選択します。
5. Resource Manager VM の場合は**ソース リソース グループ**を、クラシック VM の場合は**クラウド サービス**を選択します。
6. **[OK]** をクリックして設定を保存します。

### <a name="select-the-vms"></a>VM を選択する

Site Recovery は、サブスクリプションとリソース グループ/クラウド サービスに関連付けられている VM の一覧を取得します。

1. **[仮想マシン]** で、レプリケートする VM を選択します。
2. Click **OK**.

### <a name="configure-replication-settings"></a>レプリケーションの設定を構成する

Site Recovery では、ターゲット リージョンの既定の設定とレプリケーション ポリシーが作成されます。 設定は、要件に基づいて変更できます。

1. **[設定]** をクリックしてターゲットおよびレプリケーションの設定を表示します。
2. 既定のターゲット設定をオーバーライドするには、**[リソース グループ、ネットワーク、ストレージ、可用性セット]** の横にある **[カスタマイズ]** をクリックします。

  ![設定を構成する](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **ターゲットの場所**: ディザスター リカバリーに使用するターゲット リージョン。 ターゲットの場所が Site Recovery コンテナーの場所と一致していることをお勧めします。

- **ターゲット リソース グループ**: フェールオーバー後、Azure VM を保持する、ターゲット リージョンのリソース グループ。 既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しいリソース グループを作成します。 ターゲット リソース グループの場所は、ソース仮想マシンがホストされているリージョンを除き、どのリージョンでも構いません。 

- **ターゲット仮想ネットワーク**: フェールオーバー後、VM が配置される、ターゲット リージョンのネットワーク。
  既定では、Site Recovery は "asr" というサフィックスを持つターゲット リージョンに、新しい仮想ネットワーク (およびサブネット) を作成します。

- **ストレージ アカウントのキャッシュ**: Site Recovery では、ソース リージョンにストレージ アカウントを使用します。 ソース VM への変更は、ターゲットの場所にレプリケートする前に、このアカウントに送信されます。

- 
    **ターゲット ストレージ アカウント (ソース VM でマネージド ディスクが使用されない場合)**: 既定では、Site Recovery はターゲット リージョンに新しいストレージ アカウントを作成して、ソース VM のストレージ アカウントをミラーします。

- 
    **レプリカ マネージド ディスク (ソース VM でマネージド ディスクが使用されている場合)**: 既定では、Site Recovery によってターゲット リージョンにレプリカ マネージド ディスクが作成され、ソース VM のマネージド ディスクと同じストレージ タイプ (標準またはプレミアム) でソース VM のマネージド ディスクがミラーリングされます。

- **ターゲットの可用性セット:** 既定では、Site Recovery は "asr" サフィックスを付けて、新しい可用性セットをターゲット リージョンに作成します。 VM がソース リージョンにあるセットの一部である場合、可用性セットのみを追加できます。

既定のレプリケーション ポリシー設定をオーバーライドするには、**[レプリケーション ポリシー]** の横にある **[カスタマイズ]** をクリックします。  

- **レプリケーション ポリシー名** : ポリシーの名前。

- **復旧ポイントのリテンション期間**: 既定では、Site Recovery は 24 時間の復旧ポイントを保持します。 1 ～ 72 時間の値を構成できます。

- **アプリ整合性スナップショットの頻度**: 既定では、Site Recovery はアプリ整合性スナップショットを 4 時間ごとに取得します。 1 ～ 12 時間の任意の値を構成できます。 アプリ整合性スナップショットは、VM 内のアプリケーション データのポイントインタイム スナップショットです。 ボリューム シャドウ コピー サービス (VSS) によって、スナップショットの作成時、VM 上のアプリの整合性が維持されます。

- **レプリケーション グループ**: VM 間のマルチ VM 整合性がアプリケーションで必要な場合は、それらの VM のレプリケーション グループを作成できます。 既定では、選択された VM は、どのレプリケーション グループにも属していません。

  **[レプリケーション ポリシー]** の横にある **[カスタマイズ]** をクリックし、マルチ VM 整合性について **[はい]** を選択して、複数の VM をレプリケーション グループに含めます。 新しいレプリケーション グループを作成することも、既存のレプリケーション グループを使用することもできます。 レプリケーション グループに含める VM を選択して **[OK]** をクリックします。

> [!IMPORTANT]
  レプリケーション グループのすべてのマシンが、フェールオーバー時に共有のクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを持ちます。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるので、複数のマシンが同じワークロードを実行しており、複数のマシン間の整合性が必要な場合にのみ使用します。

> [!IMPORTANT]
  マルチ VM 整合性を有効にすると、レプリケーション グループ内のマシンは、ポート 20004 を介して相互に通信します。 ポート 20004 経由での VM 間の内部通信をブロックするファイアウォール アプライアンスがないことを確認します。 Linux VM をレプリケーション グループに含めるには、ポート 20004 の送信トラフィックが、特定の Linux バージョンのガイダンスに従って手動で開かれていることを確認します。

### <a name="track-replication-status"></a>レプリケーションの状態を追跡する

1. **[設定]** で、**[更新]** をクリックして最新の状態を取得します。

2. **保護の有効化**ジョブの進行状況は、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で追跡できます。

3. **[設定]** > **[レプリケートされたアイテム]** では、VM の状態と初期レプリケーションの進行状況を確認できます。 VM の設定をドリルダウンするには、その VM をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VM のディザスター リカバリーを構成しました。 次の手順では、構成をテストします。

> [!div class="nextstepaction"]
> [ディザスター リカバリーのテストを実行する](azure-to-azure-tutorial-dr-drill.md)
