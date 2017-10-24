---
title: "Microsoft Azure Cloud Services の接続とネットワークの問題についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Microsoft Azure Cloud Services の接続とネットワークについてよくあるご質問を紹介します。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: 7b435b6904b05228a63e3ed3a9fed78747b843c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services の接続とネットワークの問題についてよくあるご質問 (FAQ)

この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) の接続とネットワークの問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>マルチ VIP クラウド サービスの IP アドレスを予約できません
まず、IP アドレスを予約しようとしている仮想マシン インスタンスが有効であることを確認してください。 次に、ステージング デプロイと運用環境デプロイの両方で予約済みの IP を使っていることを確認します。 **変更しない** でください。

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>NSG がある場合にリモート デスクトップを使用するには
ポート **3389** および **20000** でのトラフィックを許可するルールを NSG に追加します。  リモート デスクトップはポート **3389** を使用します。  クラウド サービス インスタンスは負荷分散されるため、接続先のインスタンスを直接制御することはできません。  *RemoteForwarder* および *RemoteAccess* エージェントは、RDP トラフィックを管理し、クライアントが RDP クッキーを送信して接続先の個々のインスタンスを指定できるようにします。  *RemoteForwarder* および *RemoteAccess* エージェントではポート **20000** を開く必要があります。NSG がある場合、このポートはブロックされている可能性があります。

## <a name="can-i-ping-a-cloud-service"></a>クラウド サービスに ping を実行することはできますか。

いいえ、通常の "ping"/ICMP プロトコルは使用できません。 ICMP プロトコルは、Azure Load Balancer を通過することを許可されません。

接続をテストするには、ポートの ping を実行することをお勧めします。 Ping.exe は ICMP を使用していますが、PSPing、Nmap、telnet など、他のツールを使用すると、特定の TCP ポートへの接続をテストできます。

詳細については、「[Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (ICMP の代わりにポート ping を使用して Azure VM の接続をテストする)」を参照してください。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>クラウド サービスへの悪意のある何らかの攻撃を示す、不明な IP アドレスからのヒットが数千に上りますが、どのように防止すればよいですか。
Azure では、分散型サービス拒否 (DDoS) 攻撃からプラットフォーム サービスを保護するために複数層のネットワーク セキュリティを実装しています。 Azure の DDoS 防御システムは、Azure の継続的な監視プロセスの一部であり、侵入テストを通して継続的に強化されています。 この DDoS 防御システムは、外部からの攻撃だけではなく、他の Azure テナントからの攻撃にも耐えられるように設計されています。 詳細については、「[Microsoft Azure Network Security](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Microsoft Azure のネットワーク セキュリティ)」を参照してください。

また、いくつか特定の IP アドレスを選択的にブロックするスタートアップ タスクを作成することもできます。 詳細については、「[特定の IP アドレスをブロックする](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)」を参照してください。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>自分のクラウド サービス インスタンスに RDP 接続しようとすると、「ユーザー アカウントの有効期限が切れました」というメッセージを受け取ります。
RDP の設定で構成されている有効期限の日付を無視すると、「このユーザー アカウントは有効期限が切れました」のエラー メッセージを受け取ることがあります。 ポータルから有効期限の日付を変更するには、次の手順を実行します。
1. Azure の管理コンソール (https://manage.windowsazure.com) にログインし、お使いのクラウド サービスに移動して、**[構成]** タブを選択します。
2. **[リモート]** を選択します。
3. [有効期限] の日付を変更し、構成を保存します。

これで、お使いのマシンに RDP 接続できるようになるはずです。

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>LoadBalancer がトラフィックを均等に分散しないのはなぜですか。
ロード バランサー内部のしくみについては、「[Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Azure ロード バランサーの新しい分散モード)」を参照してください。

使用される分散アルゴリズムは、使用可能なサーバーにトラフィックをマップする 5 組 (ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類) のハッシュです。 これは、トランスポート セッション内でのみ持続性を提供します。 TCP または UDP の同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータ センターの IP (DIP) インスタンスに送信されます。 クライアントがもう一度接続を開くか、同じソース IP から新しいセッションを開始すると、ソース ポートが変更され、トラフィックは別の DIP エンドポイントに送信されます。

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>クラウド サービスの自分の既定の URL への着信トラフィックは、どのようにしてカスタム URL にリダイレクトすればよいですか。 

URL Rewrite Module of IIS を使って、クラウド サービスの既定の URL (例: \*.cloudapp.net) への着信トラフィックを、カスタムの DNS 名または URL にリダイレクトすることができます。 URL Rewrite Module は Web ロール上で既定で有効になっており、そのルールはアプリケーションの web.config に構成されているので、再起動や再イメージ化に関わりなく、いつでも VM で使用可能です。 詳細については、次を参照してください。

- [URL Rewrite Module の書き換えルールを作成する](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [既定のリンクを削除する方法](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>どうすれば自分のクラウド サービスの既定の URL への着信トラフィックをブロックまたは無効にできますか。 

自分のクラウド サービスの既定の URL または名前 (例: \*.cloudapp.net) への着信トラフィックは、下記のように、クラウド サービス定義 (*.csdef) ファイル内のサイト バインド構成でホスト ヘッダーをカスタムの DNS 名 (例: www.MyCloudService.com) に設定することにより、回避できます。 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
このホスト ヘッダー バインドは csdef ファイルを介して強制されるので、サービスはカスタム名 'www.MyCloudService.com' を介してのみアクセス可能となり、'*.cloudapp.net' ドメインへのすべての着信要求は常に失敗します。 ただし、サービス内でカスタム SLB プローブまたは内部ロード バランサーを使用している場合は、サービスの既定の URL や名前をブロックすると、プローブの動作を妨害する可能性があります。 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>クラウド サービスのパブリックに公開された IP アドレス (別名、VIP) を変わらないようにして、特定のクライアントによって常にホワイトリストに登録できるようにするにはどうすればよいですか。

お使いのクラウド サービスの IP アドレスのホワイトリスト登録については、それに関連付けた予約済み IP を持つことをお勧めします。そうでないと、デプロイを削除すると、Azure が提供する仮想 IP がご自分のサブスクリプションから割り当て解除されることになります。 VIP スワップ操作を成功させるには、運用スロットとステージング スロットの両方について個々の予約済み IP が必要であり、これがないとスワップ操作が失敗するので、注意してください。 IP アドレスを予約しそれをクラウド サービスに関連付けるには、次の記事に従ってください。  
 
- [既存のクラウド サービスの IP アドレスを予約する](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [サービス構成ファイルを使用してクラウド サービスに予約済み IP を関連付ける](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

お使いのロールに複数のインスタンスがある限り、クラウド サービスに RIP を関連付けることでダウンタイムが発生することはありません。 また、お使いの Azure データ センターの IP 範囲をホワイトリスト登録することができます。 Azure の IP 範囲はすべて[ここ](https://www.microsoft.com/en-us/download/details.aspx?id=41653)で検索できます。 

このファイルには、Microsoft Azure データ センターで使用される IP アドレス範囲 (Compute、SQL、および Storage の範囲を含む) が含まれています。 毎週投稿される最新のファイルには、現在デプロイされている範囲と今後変更される IP 範囲が反映されています。 ファイルに含まれている新しい範囲は、少なくとも 1 週間はデータ センターで使用されません。 Azure で実行されているサービスを正しく識別するために、毎週新しい xml ファイルをダウンロードし、サイトで必要な変更を実行してください。 ExpressRoute ユーザーは、このファイルを使用して、毎月第 1 週に Azure 領域の BGP アドバタイズが更新されていることに注目してください。 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>どうやって Azure Resource Manager VNet とクラウド サービスを使用すればよいですか。 

クラウド サービスは、Azure Resource Manager VNet に配置することはできませんが、Azure Resource Manager VNet と Classic VNet はピアリング経由で接続することができます。 詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」をご覧ください。