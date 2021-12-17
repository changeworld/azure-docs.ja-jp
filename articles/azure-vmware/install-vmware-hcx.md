---
title: Azure VMware Solution に VMware HCX をインストールする
description: Azure VMware Solution プライベート クラウドに VMware HCX をインストールします。
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: e3db000661ea310c35d32d988db5cd2001290004
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600819"
---
# <a name="install-and-activate-vmware-hcx-in-azure-vmware-solution"></a>Azure VMware Solution に VMware HCX をインストールしてアクティブ化する

VMware HCX Advanced とそれに関連する Cloud Manager は、Azure VMware Solution に事前にデプロイされなくなりました。 代わりに、ユーザーが Azure portal からアドオンとしてインストールを行います。 引き続き HCX コネクタ OVA をダウンロードし、仮想アプライアンスをオンプレミスの vCenter にデプロイします。 

VMware HCX のすべてのエディションでは、25 のサイト ペアリング (オンプレミスからクラウド、またはクラウドからクラウド) をサポートしています。  既定は HCX Advanced ですが、[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開くことで、現在パブリック プレビュー中の HCX Enterprise Edition を有効にすることができます。 サービスが一般公開されたら、次のステップを決定するまで 30 日間あります。 HCX Enterprise Edition サービスは無効にしたりオプトアウトしたりすることもできますが、HCX Advanced はノード コストの一部であるため、そのままにしておきます。

再デプロイなしでの HCX Enterprise Edition から HCX Advanced へのダウングレードは可能です。 まず、Enterprise 機能を使用せずに、HCX Advanced 構成状態に戻ったことを確認します。 ダウングレードを計画している場合は、スケジュールされた移行がないこと、RAV や [HCX Mobility Optimized Networking (MON)](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) などの機能が使用されていないこと、およびサイトのペアリングが 3 つ以下であることを確認してください。

>[!TIP]
>ポータルから [HCX Advanced をアンインストールする](#uninstall-hcx-advanced)こともできます。 HCX Advanced をアンインストールするときは、進行中のアクティブな移行がないことを確認してください。 HCX Advanced を削除すると、HCX 仮想アプライアンスによって占有されているプラ​​イベート クラウドにリソースが返されます。

この記事では、次のことについて説明します。

* Azure portal を使用して HCX Advanced をインストールする
* VMware HCX コネクタ OVA をダウンロードしてデプロイする
* ライセンス キーを使用して HCX Advanced をアクティブ化する


完了したら、最後に推奨される次の手順に従って、この入門ガイドの手順に進みます。

## <a name="prerequisites"></a>前提条件

- [HCX インストールの準備](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-A631101E-8564-4173-8442-1D294B731CEB.html)

- VMware HCX Enterprise を使用する予定がある場合は、[サポート リクエスト](https://portal.azure.com/#create/Microsoft.Support)を通じて [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) アドオンを有効にしてあることを確認してください。 これは、Azure VMware Solution の無料の 12 か月間試用版です。

- [VMware ブログ シリーズ - クラウド移行](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)


## <a name="install-vmware-hcx-advanced"></a>VMware HCX Advanced のインストール

1. Azure VMware Solution のプライベート クラウドから、 **[管理]**  >  **[アドオン]** を選択します。

1. **[HCX Workload Mobility]** の **[開始]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-get-started.png" alt-text="HCX Workload Mobility の [開始] ボタンを示すスクリーンショット。":::

1. **[同意する]** チェック ボックスを選択し、 **[インストール]** を選択します。

   HCX Advanced のインストールと Cloud Manager の構成には約 35 分かかります。 インストールすると、HCX オンプレミス コネクタ サイトのペアリングに必要な HCX Manager の URL と HCX キーが、 **[HCX を使用した移行]** タブに表示されます。

   :::image type="content" source="media/tutorial-vmware-hcx/deployed-hcx-migration-using-hcx-tab.png" alt-text="[接続性] の下の [HCX を使用した移行] タブを示すスクリーンショット。":::


## <a name="download-and-deploy-the-vmware-hcx-connector-ova"></a>VMware HCX コネクタ OVA をダウンロードしてデプロイする 

このステップでは、VMware HCX コネクタ OVA ファイルをダウンロードしてから、VMware HCX コネクタをオンプレミスの vCenter にデプロイします。

1. ブラウザー ウィンドウを開き、**cloudadmin\@vsphere.local** ユーザー資格情報を使用して、`https://x.x.x.9` のポート 443 で Azure VMware Solution HCX Manager にサインインします

1. **[管理]**  >  **[システム更新]** で、 **[ダウンロード リンクを要求]** を選択します。 ボックスが灰色になっている場合は、リンクが生成されるまで数秒待ちます。

1. ローカル vCenter にデプロイする VMware HCX コネクタ OVA ファイルのリンクをダウンロードまたは受信します。

1. オンプレミスの vCenter で、VMware HCX コネクタをオンプレミスの vCenter にデプロイするための [OVF テンプレート](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)を選択します。

1. ダウンロードした OVA ファイルに移動して選択し、 **[開く]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="OVF テンプレートを参照しているスクリーンショット。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::

1. 名前と場所を選択し、VMware HCX コネクタをデプロイするリソースまたはクラスターを選択します。 次に、詳細情報と必要なリソースを確認し、 **[次へ]** を選択します。

1. ライセンス条項を確認し、必要なストレージとネットワークを選択します。次に、 **[次へ]** を選択します。

1. 計画状態で定義した [[VMware HCX 管理ネットワーク セグメント]](plan-private-cloud-deployment.md#define-vmware-hcx-network-segments) を選択します。 次に、**[次へ]** を選択します。  

1. **[テンプレートのカスタマイズ]** で必要なすべての情報を入力し、 **[次へ]** を選択します。

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="テンプレートをカスタマイズするためのボックスのスクリーンショット。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 確認してから、 **[完了]** を選択して、VMware HCX コネクタ OVA をデプロイします。

   >[!IMPORTANT]
   >仮想アプライアンスを手動で有効にする必要があります。  電源投入後、10 分から 15 分待ってから、次の手順に進みます。


## <a name="activate-vmware-hcx"></a>VMware HCX をアクティブにする

VMware HCX コネクタ OVA をオンプレミスにデプロイし、アプライアンスを起動したら、アクティブにすることができます。 まず、Azure VMware Solution ポータルからライセンス キーを取得する必要があります。次に、VMware HCX Manager でライセンス キーをアクティブ化します。 最後に、デプロイされたオンプレミス HCX コネクタごとにキーが必要になります。

1. Azure VMware Solution のプライベート クラウドから、 **[管理]**  >  **[アドオン]**  >  **[HCX を使用した移行]** を選択します。 次に、 **[アクティブ化キー]** をコピーします。

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-activation-key.png" alt-text="アクティブ化キーを示したスクリーンショット。":::   

1. **管理者** 資格情報を使用して `https://HCXManagerIP:9443` のオンプレミスの VMware HCX Manager にサインインします。  VMware HCX Manager の IP アドレスと共に、必ず `9443` のポート番号を含めてください。

   >[!TIP]
   >**管理者** ユーザーのパスワードは、VMware HCX Manager OVA ファイルのデプロイ中に定義しました。

1. **[Licensing]\(ライセンス\)** で、 **[HCX Advanced Key]\(HCX Advanced キー\)** にキーを入力し、 **[Activate]** \(アクティブ化\) を選択します。

    >[!IMPORTANT]
    >VMware HCX Manager では、インターネット アクセスが開かれているか、プロキシが構成されている必要があります。

1. **[Datacenter Location]\(データセンターの場所\)** で、VMware HCX Manager をオンプレミスにインストールする最も近い場所を指定します。 その後 **[続行]** を選択します。

1. **[System Name]\(システム名\)** で名前を変更するか既定値をそのまま使用し、 **[続行]** を選択します。

1. **[Yes, Continue]\(はい、続行します\)** を選択します。

1. **[VCenter の接続]** で、お使いの vCenter サーバーの FQDN または IP アドレスと適切な資格情報を入力し、 **[続行]** を選択します。

   >[!TIP]
   >vCenter サーバーは、データセンターで VMware HCX コネクタをデプロイした場所です。

1. **[SSO/PSC の構成]** で、Platform Services Controller の FQDN または IP アドレスを入力し、 **[続行]** を選択します。

   >[!NOTE]
   >一般に、これは vCenter の FQDN または IP アドレスと同じです。

1. 入力した情報が正しいことを確認し、 **[再起動]** を選択します。

   >[!NOTE]
   >再起動後、次の手順の操作が求められるまでに待機時間が生じます。

サービスの再起動後は、表示される画面に vCenter が緑色で示されます。 vCenter と SSO のどちらにも、適切な構成パラメーターを指定する必要があります。それらは、前の画面と同じにする必要があります。

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="vCenter の状態が緑色のダッシュボードのスクリーンショット。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::


## <a name="uninstall-hcx-advanced"></a>HCX Advanced をアンインストールする

ポータルから HCX Advanced をアンインストールできます。これにより、既存のペアリングとソフトウェアが削除されます。 

>[!NOTE]
>HCX 仮想アプライアンスによって占有されているプライベート クラウドにリソースを返すには、約 30 分かかる場合があります。 

1. 進行中のアクティブな移行がないことを確認してください。

1. L2 拡張機能が不要になったこと、またはネットワークが宛先に「拡張されていない」ことを確認します。 

1. MON を使用するワークロードの場合は、既定のゲートウェイが削除されていることを確認します。 そうしないと、ワークロードが通信したり機能したりできない可能性があります。  

1. Azure VMware Solution のプライベート クラウドから、 **[管理]**  >  **[アドオン]** を選択します。

1. **[HCX Workload Mobility]** の **[開始]** を選択し、 **[アンインストール]** を選択します。
   
1. **[はい]** を選択して、アンインストールを確認します。

この時点で、HCX Advanced には vCenter プラグインがなくなり、必要に応じていつでも再インストールできます。


## <a name="next-steps"></a>次のステップ

次のチュートリアルに進んで、VMware HCX コネクタを構成します。  VMware HCX コネクタを構成すると、仮想マシン (VM) を作成と移行のための運用準備ができた環境に対応した環境になります。 


>[!div class="nextstepaction"]
>[Azure VMware Solution に VMware HCX を構成する](configure-vmware-hcx.md)
