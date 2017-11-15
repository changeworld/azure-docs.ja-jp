---
title: "Azure App Service のハイブリッド接続 | Microsoft Docs"
description: "ハイブリッド接続を作成し、それを使用して分散ネットワーク内のリソースにアクセスする方法"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Services からのハイブリッド接続 #

## <a name="overview"></a>概要 ##

ハイブリッド接続は、Azure のサービスであり、Azure App Service の機能でもあります。  サービスとして使用した場合、その用途と能力は、Azure App Service での機能を上回ります。  ハイブリッド接続の詳細と Azure App Service では提供されない使用方法については、[Azure Relay ハイブリッド接続][HCService]に関するページを参照してください。

Azure App Service 内では、ハイブリッド接続を使用して、他のネットワークのアプリケーション リソースにアクセスできます。 それは、アプリからアプリケーション エンドポイントへのアクセスを提供します。  アプリケーションにアクセスするために別の機能を有効にすることはありません。  App Service で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。  つまり、TCP リッスン ポートをターゲットにしている限り、ハイブリッド接続エンドポイントは、任意のオペレーティング システムの任意のアプリケーションにすることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されず、問題になりません。  それは、ネットワーク アクセスを提供するだけです。  


## <a name="how-it-works"></a>動作のしくみ ##
ハイブリッド接続機能は、Service Bus Relay に対する 2 つの発信呼び出しで構成されます。  App Service でアプリが実行されているホストのライブラリからの接続と、ハイブリッド接続マネージャー (HCM) から Service Bus Relay への接続があります。  HCM は、アクセスしようとしているネットワーク ホスト内にデプロイされるリレー サービスです。 

この 2 つの結合された接続を通して、HCM の他方の側の固定されたホストとポートの組み合わせに至るアプリの TCP トンネルが設定されます。  接続では、セキュリティのために TLS 1.2 が、認証/承認のために SAS キーが使用されます。    

![ハイブリッド接続のフローの概要][1]

アプリが DNS 要求を行い、その要求が構成済みのハイブリッド接続エンドポイントと一致した場合、発信 TCP トラフィックがハイブリッド接続を介してリダイレクトされます。  

> [!NOTE]
> つまり、ハイブリッド接続では常に DNS 名を使用するようにします。  一部のクライアント ソフトウェアは、エンドポイントで IP アドレスが使用されている場合は DNS 参照を実行しません。
>
>

ハイブリッド接続の種類は 2 つあります。Azure Relay でサービスとして提供される新しいハイブリッド接続と、従来の BizTalk ハイブリッド接続です。  従来の BizTalk ハイブリッド接続は、ポータルではクラシック ハイブリッド接続と呼ばれます。  これらについては、このドキュメントの中で、後で詳しく説明します。

### <a name="app-service-hybrid-connection-benefits"></a>App Service のハイブリッド接続のメリット ###

ハイブリッド接続機能には、次のようなさまざまなメリットがあります。

- アプリがオンプレミスのシステムをサービスにセキュリティで保護されたアクセスを実行できます。
- インターネットにアクセス可能なエンドポイントは必要ありません。
- すばやく簡単に設定できます。 
- 各ハイブリッド接続が単一のホストとポートの組み合わせと一致します。これはセキュリティ面で優れています。
- 接続はすべて標準的な Web ポート経由で発信されるため、通常はファイアウォールを通過するための設定は必要ありません。
- ネットワーク レベルの機能であるため、アプリで使用される言語とエンドポイントで使用されるテクノロジに依存しません。
- 単一のアプリから複数のネットワークにアクセスするために使用できます。 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>ハイブリッド接続で実行できないこと ###

ハイブリッド接続で実行できないことがいくつかあります。

- ドライブのマウント
- UDP の使用
- FTP パッシブ モードや拡張パッシブ モードなどの動的ポートを使用する TCP ベースのサービスへのアクセス
- LDAP のサポート (UDP が必要な場合があるため)
- Active Directory のサポート

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>アプリでのハイブリッド接続の追加と作成 ##

ハイブリッド接続を作成するには、Azure Portal で App Service アプリを使用します。また、Azure Portal で Azure Relay から作成することもできます。  ハイブリッド接続は、そのハイブリッド接続で使用する App Service アプリを使って作成することを強くお勧めします。  ハイブリッド接続を作成するには、[Azure Portal][portal] に移動し、アプリを選択します。  **[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順に選択します。  ここで、アプリ用に構成されているハイブリッド接続を確認できます。  

![ハイブリッド接続の一覧][2]

新しいハイブリッド接続を追加するには、[ハイブリッド接続の追加] をクリックします。  表示される UI に、既に作成したハイブリッド接続が一覧表示されます。  アプリに 1 つ以上のハイブリッド接続を追加するには、追加する接続先をクリックし、**[選択したハイブリッド接続の追加]** をクリックします。  

![ハイブリッド接続ポータル][3]

新しいハイブリッド接続を作成する場合は、**[新しいハイブリッド接続の作成]** をクリックします。  ここで、次の項目を指定します。 

- エンドポイント名
- エンドポイント ホストの名前
- エンドポイント ポート
- 使用する Service Bus 名前空間

![ハイブリッド接続の追加][4]

すべてのハイブリッド接続は Service Bus 名前空間に関連付けられており、各 Service Bus 名前空間は Azure リージョンに存在します。  ネットワークで誘発される待機時間を防ぐために、できるだけアプリと同じリージョンの Service Bus 名前空間を使用することが重要です。

ハイブリッド接続をアプリから削除する場合は、ハイブリッド接続を右クリックし、**[切断]** を選択します。  

ハイブリッド接続がアプリに追加されたら、ハイブリッド接続をクリックするだけで詳細情報を表示できます。 

![ハイブリッド接続の詳細][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay ポータルでのハイブリッド接続の作成 ###

アプリ内からポータルを操作するだけでなく、Azure Relay ポータル内からハイブリッド接続を作成することもできます。 Azure App Service でハイブリッド接続を使用するには、2 つの条件を満たす必要があります。 その条件を次に示します。

* クライアント承認を要求する
* メタデータ項目に、host:port の組み合わせを値として含むエンドポイントの名前が付いている

## <a name="hybrid-connections-and-app-service-plans"></a>ハイブリッド接続と App Service プラン ##

ハイブリッド接続は、Basic、Standard、Premium、および Isolated の価格 SKU でのみ利用できます。  料金プランに関連付けられている制限があります。  

> [!NOTE] 
> 新しく作成できるのは、Azure Relay に基づいたハイブリッド接続だけです。 新しい BizTalk ハイブリッド接続を作成することはできません。
>

| 料金プラン | プランで使用できるハイブリッド接続の数 |
|----|----|
| 基本 | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

App Service プランの制限があるため、App Service プランごとの UI もあり、各 UI にはいくつのハイブリッド接続がどのアプリによって使用されているかが表示されます。  

![App Service プラン レベルのプロパティ][6]

ハイブリッド接続の詳細を表示するには、それをクリックします。  表示されるプロパティでは、アプリ ビューと同じ情報をすべて確認できます。また、同じ App Service プランで、そのハイブリッド接続を使用しているアプリが他にいくつあるかを確認することもできます。

App Service プランで使用できるハイブリッド接続のエンドポイントの数には制限がありますが、各ハイブリッド接続は、同じ App Service プラン内の複数のアプリで使用することができます。  つまり、App Service プランで 5 つの別々のアプリによって使用されている 1 つのハイブリッド接続は、1 つのハイブリッド接続としてカウントされます。

ハイブリッド接続を使用するには、追加コストがかかります。  ハイブリッド接続の価格の詳細については、「[Service Bus の価格][sbpricing]」を参照してください。

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager の使用 ##

ハイブリッド接続を機能させるためには、ハイブリッド接続エンドポイントをホストしているネットワークにリレー エージェントを作成する必要があります。  そのリレー エージェントは、Hybrid Connection Manager (HCM) と呼ばれます。  このツールをダウンロードするには、[Azure Portal][portal] のアプリから、**[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順に移動します。  

このツールは、Windows Server 2012 以降のバージョンの Windows で実行されます。  HCM は、インストール後はサービスとして実行されます。  このサービスは、構成されたエンドポイントに基づいて Azure Service Bus Relay に接続します。  HCM からの接続は、ポート 443 経由で Azure に対して発信されます。    

HCM には、それを構成するための UI があります。  HCM をインストールした後、ハイブリッド接続マネージャーのインストール ディレクトリ内に配置される HybridConnectionManagerUi.exe を実行してその UI を起動できます。  Windows 10 では、検索ボックスに「*Hybrid Connection Manager UI*」と入力することで簡単に起動できます。  

![ハイブリッド接続ポータル][7]

HCM UI が起動すると、最初に表示されるのは、HCM のこのインスタンスで構成されているハイブリッド接続の一覧です。  変更する場合は、Azure で認証する必要があります。 

1 つまたは複数のハイブリッド接続を HCM に追加するには:

1. HCM UI を起動します。
1. [Configure another Hybrid Connection]\(別のハイブリッド接続の構成\) をクリックします。![HCM での HC の追加][8]

1. Azure アカウントでサインインします。
1. サブスクリプションを選択します。
1. HCM をリレーするハイブリッド接続をクリックします。![HC の選択][9]

1. [保存] をクリックします。

この時点で、追加したハイブリッド接続が表示されます。  構成済みのハイブリッド接続をクリックして、その詳細を表示することもできます。

![HC の詳細][10]

構成済みのハイブリッド接続を HCM でサポートできるようにするには、以下が必要です。

- Azure に対するポート 80 と 443 経由の TCP アクセス
- ハイブリッド接続エンドポイントに対する TCP アクセス
- エンドポイント ホストと Azure Service Bus 名前空間で DNS 参照を実行する能力

HCM は、新しいハイブリッド接続と従来の BizTalk ハイブリッド接続の両方をサポートします。

> [!NOTE]
> Azure Relay は、Web ソケットを使用して接続します。 この機能は、Windows Server 2012 以降でのみご利用いただけます。 このため、ハイブリッド接続マネージャーは、Windows Server 2012 より前のバージョンではサポートされていません。
>

### <a name="redundancy"></a>冗長性 ###

HCM はそれぞれ、複数のハイブリッド接続をサポートできます。  また、特定のハイブリッド接続については、複数の HCM がサポートすることができます。  既定の動作は、特定のエンドポイントに対して構成された HCM でのトラフィックのラウンド ロビンです。  ネットワークからのハイブリッド接続の高可用性を求める場合は、単純に複数の HCM を別個のコンピューターにインスタンス化します。 

### <a name="manually-adding-a-hybrid-connection"></a>ハイブリッド接続の手動追加 ###

サブスクリプションを持っていないユーザーで特定のハイブリッド接続に対する HCM インスタンスをホストする場合は、ハイブリッド接続用のゲートウェイ接続文字列を共有できます。  これは、[Azure Portal][portal] のハイブリッド接続のプロパティで確認できます。 この文字列を使用するには、HCM で **[手動で入力]** ボタンをクリックし、ゲートウェイ接続文字列を貼り付けます。


## <a name="troubleshooting"></a>トラブルシューティング ##

ハイブリッド接続が接続の状態の場合、それは少なくとも 1 つの HCM がそのハイブリッド接続で構成され、Azure にアクセスできることを意味します。  ハイブリッド接続の状態が **[接続済み]** にならない場合、Azure にアクセスできる HCM でハイブリッド接続が構成されていません。

クライアントがエンドポイントに接続できない主な理由は、エンドポイントが DNS 名の代わりに IP アドレスを使用して指定されたためです。  アプリが目的のエンドポイントに到達できないときに IP アドレスを使用していた場合は、HCM を実行中のホストで有効な DNS 名の使用に切り替えます。  他にチェックするのは、HCM を実行中のホストで DNS 名が正しく解決されることと、HCM を実行中のホストからハイブリッド接続エンドポイントへの接続が存在していることです。  

App Service には、tcpping と呼ばれる高度なツール (Kudu) コンソールから呼び出すことのできるツールがあります。  このツールは、TCP エンドポイントにアクセスできるかどうかを通知できますが、ハイブリッド接続エンドポイントにアクセスできるかどうは通知しません。  コンソールでハイブリッド接続エンドポイントに対して使用し、ping が成功した場合でも、それは、ホストとポートの組み合わせを使用するハイブリッド接続がアプリに構成されていることを通知しているだけです。  

## <a name="biztalk-hybrid-connections"></a>BizTalk ハイブリッド接続 ##

従来の BizTalk ハイブリッド接続機能では、新しい BizTalk ハイブリッド接続を使用できなくなりました。  既存の BizTalk ハイブリッド接続は、引き続きアプリでご利用いただけますが、Azure Relay を使用する新しいハイブリッド接続に移行する必要があります。  新しいサービスには、BizTalk バージョンに比べて次のメリットがあります。

- BizTalk アカウントを追加する必要はありません。
- TLS は、BizTalk ハイブリッド接続の 1.0 ではなく 1.2 になります。
- Azure に到達するための通信は、ポート 80 と 443 で DNS 名 を使用して行われ、IP アドレスとその他のポートの追加使用はありません  

既存の BizTalk ハイブリッド接続をアプリに追加するには、[Azure Portal][portal] でアプリに移動し、**[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順にクリックします。  [クラシック ハイブリッド接続] テーブルで、**[クラシック ハイブリッド接続の追加]** をクリックします。  BizTalk ハイブリッド接続の一覧が表示されます。  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/
