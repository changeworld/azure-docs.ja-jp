<properties
	pageTitle="クラウド サービスのカスタム ドメイン名の構成"
	description="DNS 設定を構成して、カスタム ドメインで Azure のアプリケーションやデータを公開する方法について説明します。"
	services="cloud-services"
	documentationCenter=".net"
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/29/2015"
	ms.author="adegeo"/>

# Azure クラウド サービスのカスタム ドメイン名の構成

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-custom-domain-name.md)
- [Azure Preview Portal](cloud-services-custom-domain-name-portal.md)

クラウド サービスを作成するときに、Azure は **cloudapp.net** のサブドメインにそのアプリを割り当てます。たとえば、クラウド サービスの名前が "contoso" の場合、ユーザーは http://*contoso*.cloudapp.net のような URL でアプリケーションにアクセスできます。また Azure によって仮想 IP アドレスも割り当てられます。

ただし、**contoso.com** のような独自のドメイン名を使用してアプリケーションを公開することもできます。この記事では、クラウド サービス Web ロールのカスタム ドメイン名を予約、構成する方法について説明します。

CNAME レコードと A レコードについて既に理解している場合は、 [説明を読まずに次に進みます](#add-a-cname-record-for-your-custom-domain)。

> [AZURE.NOTE]このタスクの手順は、Azure Cloud Services に適用されます。Web サイトについては、「[Azure App Service Web アプリのカスタム ドメイン名の構成](../app-service-web/web-sites-custom-domain-name.md)」を参照してください。ストレージ アカウントについては、「[Azure ストレージ アカウントの BLOB データのカスタム ドメイン名の構成](../storage/storage-custom-domain-name.md)」をご覧ください。

<p/>

> [AZURE.TIP]より速く進める --新しい Azure の使用[チュートリアル ガイド](http://support.microsoft.com/kb/2990804)! Azure クラウド サービスや Azure Websites を使用したカスタム ドメイン名の関連付けや通信 (SSL) のセキュリティ保護がすばやく行えます。

## CNAME レコードと A レコードについて

CNAME レコード (またはエイリアス レコード) および A レコードはどちらもドメイン名を特定のサーバー (この場合、またはサービス) に関連付けることができますが、機能は異なります。また、Azure のクラウド サービスで A レコードを使用する場合には固有の考慮事項もいくつかあるため、どちらのレコードを使用するかを決定する前に、これらの点を検討しておく必要があります。

### CNAME レコードまたはエイリアス レコード

CNAME レコードは、*contoso.com* や **www.contoso.com** などの**特定の**ドメインを正規のドメイン名にマップします。この場合、正規のドメイン名は Azure ホステッド アプリケーションの **[myapp].cloudapp.net** ドメイン名です。作成すると、CNAME は **[myapp].cloudapp.net** のエイリアスを作成します。CNAME エントリは **[myapp].cloudapp.net** サービスの IP アドレスを自動的に解決するため、クラウド サービスの IP アドレスが変更されても、特別な対応をする必要はありません。

> [AZURE.NOTE]いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (contoso.com など) ではなく、サブドメイン (www.contoso.com など) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、「[the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)](http://en.wikipedia.org/wiki/CNAME_record)」か、「[IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)](http://tools.ietf.org/html/rfc1035)」をご覧ください。

### A レコード

*A* レコードは、ドメイン (**contoso.com**、**www.contoso.com** など)、*ワイルドカード ドメイン* (**\*.contoso.com** など) を IP アドレスにマップします。Azure のクラウド サービスの場合は、サービスの仮想 IP です。CNAME レコードと比較したときの A レコードの主な利点は、エントリにワイルドカードを使用できる (\*\*\*.contoso.com\*\* など) ため、複数のサブドメイン (**mail.contoso.com**、**login.contoso.com**、**www.contso.com** など) の要求を処理できることです。

> [AZURE.NOTE]A レコードは静的 IP にマップされるため、変更をクラウド サービスの IP アドレスに自動的に解決することはできません。クラウド サービスによって使用される IP アドレスは、空のスロット (運用またはステージング) に初めて展開したときに割り当てられます。 スロットの展開を削除すると、IP アドレスは Azure によって解放され、そのスロットへの今後の展開は新しい IP アドレスに与えられます。
>
> 都合が良いのは、ステージング展開と運用展開との間のスワッピングまたは既存の展開のインプレース アップグレードを実行する場合に、所定の展開スロット (運用またはステージング) の IP アドレスが保持されることです。これらの操作の実行の詳細については、「[How to manage cloud services (クラウド サービスの管理方法)](cloud-services-how-to-manage.md)」を参照してください。


## カスタム ドメインの CNAME レコードの追加

CNAME レコードを作成するには、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加する必要があります。それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. これらの手段のいずれかを使用して、クラウド サービスに割り当てられた **.cloudapp.net** ドメイン名を見つけます。

    * [Azure プレビュー ポータル]にログインし、クラウド サービスを選択して、**[Essentials]** セクションを確認して **[サイトの URL]** エントリを見つけます。

        ![サイトの URL を表示する [概要] セクション][csurl]
            
        **OR**
  
    * [Azure Powershell](../install-configure-powershell.md) をインストールして構成し、次のコマンドを使用します。

        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    CNAME レコードを作成する場合に必要になるため、いずれかの方法で返された URL で使用されているドメイン名を保存します。

1.  DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

2.  ここで CNAME レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。**CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

3.  また、**www.customdomain.com** のエイリアスを作成する場合は、CNAME のドメインまたはサブドメイン エイリアス (**www** など) を指定する必要があります。ルート ドメインのエイリアスを作成する場合は、レジストラーの DNS ツールに '\*\*@\*\*' シンボルとして示される場合があります。

4. 次に、正規のホスト名 (ここではアプリケーションの **cloudapp.net** ドメイン) を指定します。

たとえば、次の CNAME レコードでは、すべてのトラフィックが **www.contoso.com** から、展開されたアプリケーションのカスタム DNS 名である **contoso.cloudapp.net** に転送されます。

| エイリアス/ホスト名/サブドメイン | 正規のドメイン |
| ------------------------- | -------------------- |
| www | contoso.cloudapp.net |

> [AZURE.NOTE]**www.contoso.com** の訪問者が本当のホスト (contoso.cloudapp.net) を識別することはないため、転送プロセスはエンド ユーザーから見えなくなります。

> 上の例は、**www** サブドメインのトラフィックのみに該当します。CNAME レコードにはワイルドカードを使用できないため、各ドメインとサブドメインに 1 つの CNAME を作成する必要があります。サブドメイン (*.contoso.com など) からトラフィックを cloudapp.net アドレスに転送するには、DNS 設定の **URL リダイレクト** エントリまたは **URL 転送**エントリを構成するか、A レコードを作成します。


## カスタム ドメインの A レコードの追加

A レコードを作成するには、まず、クラウド サービスの仮想 IP アドレスを見つける必要があります。次に、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加します。それぞれのレジストラーでは A レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. 次の手段のいずれかを使用して、クラウド サービスの IP アドレスを取得します。

    * [Azure プレビュー ポータル]にログインし、クラウド サービスを選択して、**[Essentials]** セクションを確認して **[Public IP addresses]** エントリを見つけます。

        ![VIP を表示する [概要] セクション][vip]

        **OR**

    * [Azure Powershell](../install-configure-powershell.md) をインストールして構成し、次のコマンドを使用します。

        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    A レコードを作成する場合に必要になるため、IP アドレスを保存します。

1.  DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

2.  ここで A レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。

3. この A レコードを使用するドメインまたはサブドメインを選択または入力します。たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。すべてのサブドメインのワイルドカード エントリを作成する場合は、「\_\_\*\_\_」と入力します。これは、**mail.customdomain.com**、**login.customdomain.com**、**www.customdomain.com** などすべてのサブドメインを対象とします。

    ルート ドメインに A レコードを作成する場合は、レジストラーの DNS ツールに '\*\*@\*\*' シンボルとして示される場合があります。

4. 表示されたフィールドのクラウド サービスの IP アドレスを入力します。これによって、A レコードで使用されるドメイン エントリがクラウド サービスの展開の IP アドレスに関連付けられます。

たとえば、次の A レコードでは、すべてのトラフィックが **contoso.com** から、展開されたアプリケーションの IP アドレス名である **137.135.70.239** に転送されます。

| ホスト名/サブドメイン | IP アドレス |
| ------------------- | -------------- |
| @ | 137\.135.70.239 |


この例では、ルート ドメインの A レコードを作成する方法を示します。すべてのサブドメインを対象とするワイルドカードを作成する場合は、サブドメインとして「\_\_\*\_\_」と入力します。

>[AZURE.WARNING]Azure の IP アドレスは、既定では動的です。自分の IP アドレスが変更されないようにするために、[予約済み IP アドレス](..\virtual-network\virtual-networks-reserved-public-ip.md)を使用すると便利です。

## 次のステップ

-   [クラウド サービスの管理方法](cloud-services-how-to-manage.md)
-   [CDN コンテンツをカスタム ドメインにマッピングする方法](http://msdn.microsoft.com/library/windowsazure/gg680307.aspx)

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure プレビュー ポータル]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
 

<!---HONumber=July15_HO5-->