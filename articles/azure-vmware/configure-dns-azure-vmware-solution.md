---
title: Azure VMware Solution 用に DNS フォワーダーを構成する
description: Azure portal を使用して Azure VMware Solution 用に DNS フォワーダーを構成する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323944"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure portal で DNS フォワーダーを構成する

>[!IMPORTANT]
>2021 年 7 月 1 日以降に作成された Azure VMware Solution のプライベート クラウドに対して、プライベート DNS 解決を構成する機能が追加されました。 プライベート DNS 解決を必要とする、2021 年 7 月 1 日より前に作成されたプライベート クラウドの場合は、[サポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)を開き、プライベート DNS 構成を要求してください。 

既定では、vCenter などの Azure VMware Solution 管理コンポーネントは、パブリック DNS 経由で使用できる名前レコードのみを解決できます。 ただし、特定のハイブリッド ユース ケースでは、正しく機能するためには、vCenter や Active Directory などのカスタマー マネージド システムを含め、Azure VMware Solution 管理コンポーネントは、非公開でホストされている DNS からの名前レコードを解決する必要があります。

Azure VMware Solution 管理コンポーネントのプライベート DNS を使用すると、NSX-T DNS サービスを介して、目的のドメイン名に対して、選択したプライベート DNS サーバー セットへの条件付き転送ルールを定義できます。 

この機能では、NSX-T の DNS フォワーダー サービスが使用されます。 DNS サービスと既定の DNS ゾーンは、プライベート クラウドの一部として提供されます。 Azure VMware Solution 管理コンポーネントでプライベート DNS システムからのレコードを解決するには、FQDN ゾーンを定義し、それを NSX-T DNS サービスに適用する必要があります。 DNS サービスは、そのゾーンに定義された外部 DNS サーバーに基づいて、各ゾーンに対する DNS クエリを条件付きで転送します。

>[!NOTE]
>DNS サービスは、最大 5 つの FQDN ゾーンに関連付けられます。 各 FQDN ゾーンは、最大 3 つの DNS サーバーに関連付けられます。

>[!TIP]
>必要に応じて、ワークロード セグメントに条件付き転送ルールを使用することもできます。その場合は、それらのセグメント上の仮想マシンで、NSX-T DNS サービスの IP アドレスが DNS サーバーとして使用されるように構成します。


## <a name="architecture"></a>アーキテクチャ

この図は、NSX-T DNS サービスが、Azure およびオンプレミス環境でホストされている DNS システムに DNS クエリを転送できることを示しています。

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="NSX-T DNS サービスが、Azure およびオンプレミス環境でホストされている DNS システムに DNS クエリを転送できることを示す図。" border="false":::


## <a name="configure-dns-forwarder"></a>DNS フォワーダーを構成する

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下から **[DNS]**  >  **[DNS ゾーン]** を選択します。 その後、 **[追加]** を選択します。

   >[!NOTE]
   >2021 年 7 月 1 日以降に作成されたプライベート クラウドの場合、プライベート クラウドの作成時に既定の DNS ゾーンが自動的に作成されます。

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="Azure VMware Solution プライベート クラウドに DNS ゾーンを追加する方法を示すスクリーンショット。":::

1. **[FQDN ゾーン]** を選択し、名前と最大 3 つの DNS サーバーの IP アドレスを **10.0.0.53** の形式で指定します。 **[OK]** をクリックします。

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="FQDN ゾーンを追加するために必要な情報を示すスクリーンショット。":::

   >[!IMPORTANT]
   >NSX-T では、スペースと、他の英数字以外の文字を DNS ゾーン名に使用することが許可されていますが、DNS ゾーンなどの特定の NSX リソースは、名前で特定の文字が許可されない Azure リソースにマップされます。 
   >
   >その結果、NSX-T では有効な DNS ゾーン名を、[Azure リソースの名前付け規則](../azure-resource-manager/management/resource-name-rules.md#microsoftresources)に準拠するように調整する必要がある場合があります。

      完了までには数分かかります。進行状況は、**通知** から確認できます。 DNS ゾーンが作成されると、[通知] にメッセージが表示されます。

1. プライベート クラウドの一部として自動的に作成されるため、既定の DNS ゾーンに関するメッセージは無視してください。

1. **[DNS サービス]** タブを選択し、 **[編集]** を選択します。

   >[!TIP]
   >2021 年 7 月 1 日以降に作成されたプライベート クラウドの場合、既定の DNS ゾーンはプライベート クラウドの作成中に自動的に作成されるので、それに関するメッセージは無視してかまいません。


   >[!IMPORTANT]
   >プライベート クラウド内の特定の操作は NSX-T Manager から実行できますが、2021 年 7 月 1 日以降に作成されたプライベート クラウドの場合、既定の Tier-1 ゲートウェイに対して行われた構成変更については、Azure portal の簡素化されたネットワーク エクスペリエンスから DNS サービスを編集する "_必要があります_"。  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="[編集] ボタンが選択されている [DNS サービス] タブを示すスクリーンショット。":::   

1. **[FQDN ゾーン]** ドロップダウンから、新しく作成された FQDN を選択し、 **[OK]** を選択します。

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="DNS サービスに対して選択された FQDN を示すスクリーンショット。":::

   完了までには数分かかります。 **[通知]** から "*完了*" メッセージが表示されます。 この時点で、プライベート クラウドの管理コンポーネントは、NSX-T DNS サービスに指定された FQDN ゾーンから DNS エントリを解決できます。 

1. 該当する逆引き参照ゾーンを含め、他の FQDN ゾーンに対して上記の手順を繰り返します。


## <a name="verify-name-resolution-operations"></a>名前解決の操作を確認する

DNS フォワーダーを構成した後は、名前解決操作を確認するために使用できるいくつかのオプションがあります。 

### <a name="nsx-t-manager"></a>NSX-T Manager

NSX-T Manager では、グローバル サービス レベルとゾーン単位で DNS フォワーダー サービスの統計情報が提供されます。 

1. NSX-T Manager で、 **[ネットワーク]**  >  **[DNS]** を選択し、DNS フォワーダー サービスを展開します。

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="NSX-T Manager の [DNS サービス] タブを示すスクリーンショット。":::

1. **[View Statistics]\(統計情報の表示\)** を選択し、 **[Zone Statistics]\(ゾーンの統計情報\)** ドロップダウンから該当する FQDN ゾーンを選択します。

   上半分にはサービス全体の統計情報が表示され、下半分には指定したゾーンの統計情報が表示されます。 この例では、FQDN ゾーンの構成中に指定された DNS サービスに転送されたクエリを確認できます。

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="DNS フォワーダーの統計情報を示すスクリーンショット。":::


### <a name="powercli"></a>PowerCLI

NSX-T Policy API を使用すると、NSX-T DNS フォワーダー サービスから nslookup コマンドを実行できます。 必要なコマンドレットは、PowerCLI の `VMware.VimAutomation.Nsxt` モジュールの一部です。 次の例は、そのモジュールのバージョン 12.3.0 からの出力を示しています。

1. NSX-T サーバーに接続します。 

   >[!TIP]
   >NSX-T サーバーの IP アドレスは、Azure portal の **[管理]**  >  **[ID]** から取得できます。
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. DNS フォワーダーの nslookup サービスへのプロキシを取得します。

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. DNS フォワーダー サービスから検索を実行します。

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  コマンドの最初のパラメーターは、プライベート クラウドの T1 ゲートウェイの ID です。これは、Azure portal の [DNS サービス] タブから取得できます。

1. 応答の次のプロパティを使用して、検索から未加工の回答を取得します。

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   この例では、アドレスが 172.21.90.2 の A レコードを示す vc01.contoso.corp のクエリの回答を確認できます。 また、この例では、DNS フォワーダー サービスからのキャッシュされた応答も示されています。そのため、出力は若干異なる場合があります。
