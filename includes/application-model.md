# コンピューティング
 Azure では、Microsoft データ センター内で実行されるアプリケーション コードをデプロイし、監視できます。Azure でアプリケーションを作成して実行する場合、そのコードと構成は、総称して Azure ホステッド サービスと呼ばれます。ホステッド サービスは、管理、スケール アップとスケール ダウン、再構成、新しいバージョンのアプリケーション コードでの更新が容易です。この記事では、Azure ホステッド サービスのアプリケーション モデルを中心に説明します。<a id="compare" name="compare"></a>

## 目次<a id="_GoBack" name="_GoBack"></a>

-   [Azure アプリケーション モデルの利点][]
-   [ホステッド サービスの中心概念][]
-   [ホステッド サービスの設計に関する考慮事項][]
-   [規模に合わせたアプリケーションの設計][]
-   [ホステッド サービスの定義と構成][]
-   [サービス定義ファイル][]
-   [サービス構成ファイル][]
-   [ホステッド サービスの作成と展開][]
-   [参照][]

## <a id="benefits"> </a>Azure アプリケーション モデルの利点

アプリケーションをホステッド サービスとしてデプロイすると、そのアプリケーションのコードが含まれる仮想マシン (VM) が 1 つ以上作成され、いずれかの Azure データ センターに存在する物理マシン上の VM が起動されます。ホステッド アプリケーションへのクライアント要求がデータ センターに届くと、ロード バランサーがその要求を VM に均等に分散させます。アプリケーションを Azure にホストしたときのメリットは、主に次の 3 つです。

-   **高可用性:** 高可用性とは、アプリケーションの実行が可能な限り維持され、アプリケーションがクライアント要求に応答できる状態が Azure によって保証されることを意味します。ハンドルされない例外などが原因でアプリケーションが終了すると、Azure がそれを検知し、アプリケーションを自動的に再起動します。アプリケーションが実行されているマシンでなんらかのハードウェア障害が発生した場合も、Azure がそれを検知し、稼働している別の物理マシンに新しい VM を自動的に作成して、コードをそこから実行します。メモ: アプリケーションでマイクロソフトの 99.95% のサービス レベル契約を満たせるように、アプリケーション コードを少なくとも 2 つの VM で実行する必要があります。そうすれば、障害が発生した VM から問題のない新しい VM にコードが移動されている間も、もう一方の VM でクライアント要求を処理できます。

-   **拡張性:** Azure では、アプリケーション コードを実行する VM の数を簡単かつ動的に変更して、アプリケーションに対する実際の負荷を処理することができます。そのため、必要なときに必要な VM に対してだけ料金を払いつつ、顧客の使用による負荷に合わせてアプリケーションを調整できます。VM の数を変更する必要が生じてから数分以内に Azure が対応し、実行中の VM の数を動的に変更できるようになります。しかも、その回数に制限はありません。

-   **管理のしやすさ:** Azure は PaaS (サービスとしてのプラットフォーム) サービスであるため、これらのマシンが稼働し続けるうえで不可欠なインフラストラクチャ (ハードウェア自体、電力、ネットワーキング) を管理します。Azure はプラットフォームの管理も行い、すべての適切な修正プログラムやセキュリティ更新プログラムのほか、.NET Framework やインターネット インフォメーション サービスなどのコンポーネントの更新プログラムも適用して、オペレーティング システムを最新の状態に保ちます。どの VM でも Windows Server 2008 が実行されているため、診断監視、リモート デスクトップのサポート、ファイアウォール、証明書ストアの構成などの機能も別途提供されます。これらの機能はいずれも追加コストなしで利用できます。実際に、アプリケーションを Azure で実行する際には、Windows Server 2008 オペレーティング システム (OS) のライセンスが含まれています。すべての VM で Windows Server 2008 が実行されるため、Windows Server 2008 で実行できるコードであれば、Azure でも正常に機能します。

## <a id="concepts"> </a>ホステッド サービスの中心概念

Azure でホステッド サービスとしてデプロイされたアプリケーションは、1 つ以上の*ロール*として実行されます。*ロール*とは単にアプリケーション ファイルと構成のことを表します。アプリケーションには 1 つ以上のロールを定義でき、そのそれぞれに独自のアプリケーション ファイルと構成のセットがあります。アプリケーションのロールごとに、実行する VM (つまり*ロール インスタンス*) の数を指定できます。以下の図は、ロールとロール インスタンスを使って、ホステッド サービスとしてモデル化されたアプリケーションの例を示します。

##### 図 1: Azure データ センターで実行されている 3 つのインスタンス (VM) を持つ 1 つのロール

![image][0]

##### 図 2: それぞれ Azure データ センターで実行されている 2 つのインスタンス (VM) を持つ 2 つのロール

![image][1]

ロール インスタンスは通常、*入力エンドポイント*を介してデータ センターに入ってくるインターネット クライアント要求を処理します。1 つのロールには、1 つ以上の入力エンドポイントが存在します (存在しないこともあります)。各エンドポイントにはプロトコル (HTTP、HTTPS、または TCP) とポートが設定されます。HTTP はポート 80 でリッスンし、HTTPS はポート 443 でリッスンするというぐあいに、ロールに 2 つの入力エンドポイントを構成するのが一般的です。以下の図は 2 つの異なるロールの例ですが、それぞれが、クライアント要求を受信する異なる入力エンドポイントを持っています。

![image][2]

Azure にホステッド サービスを作成すると、パブリックにアドレス指定可能な IP アドレスが割り当てられ、クライアントはそのアドレスを使ってアクセスできるようになります。ホステッド サービスの作成時には、その IP アドレスにマップされる URL プレフィックスも選択する必要があります。このプレフィックスは一意である必要があります。実質的に*プレフィックス*.cloudapp.net という URL を予約することになるため、他のだれも利用できなくなります。クライアントは、この URL を使用してロール インスタンスと通信します。通常は、Azure の*プレフィックス*.cloudapp.net という URL を配布したり、公開したりすることはありません。その代わりに、好きな DNS レジストラーから DNS 名を購入し、クライアント要求が Azure の URL にリダイレクトされるように DNS 名を構成します。詳細については、「[Configuring a Custom Domain Name in Azure (Azure でのカスタム ドメイン名の構成)][]」を参照してください。

## <a id="considerations"> </a>ホステッド サービスの設計に関する考慮事項

クラウド環境で実行するアプリケーションを設計する際には、待機時間、高可用性、スケーラビリティなど、いくつかの事項を考慮する必要があります。

Azure でホステッド サービスを実行する場合は、アプリケーション コードを配置する場所を考慮することが重要です。待機時間を減らし、パフォーマンスを最大化するために、クライアントに最も近いデータ センターにアプリケーションをデプロイするのが一般的ですが、データやデータが存在する場所に関して法的な懸念がある場合は、自社か自社データに近い場所にあるデータ センターも選択肢となります。アプリケーション コードをホストできるデータ センターは、世界に 6 つあります。選べる場所を以下の表に示します。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**国/リージョン**

</td>
<td style="width: 200px;">
**サブリージョン**

</td>
</tr>
<tr>
<td>
米国

</td>
<td>
米国中南部、米国中北部

</td>
</tr>
<tr>
<td>
ヨーロッパ

</td>
<td>
米国北部、米国西部

</td>
</tr>
<tr>
<td>
アジア

</td>
<td>
米国南東部、米国東部

</td>
</tr>
</tbody>
</table>
ホステッド サービスを作成する際には、コードを実行する場所を示すサブリージョンを選択します。

高い可用性とスケーラビリティを実現するには、アプリケーションのデータを複数のロール インスタンスからアクセスできる一元的なリポジトリに配置することがきわめて重要です。それができるように、Azure には、BLOB、テーブル、SQL Database などのストレージ オプションがいくつか用意されています。これらのストレージ テクノロジの詳細については、「[Data Storage Offerings in Azure (Azure におけるデータ ストレージ サービス)][]」を参照してください。以下の図は、Azure データ センター内のロード バランサーがクライアント要求を異なるロール インスタンス (いずれも同じデータ ストレージにアクセスできる) に分散させるようすを示しています。

![image][3]

通常は、アプリケーション コードとデータは同じデータ センターに配置します。アプリケーション コードがデータにアクセスするときの待機時間が短くなり、パフォーマンスが向上するためです。また、同じデータ センター内でデータを移動するときの帯域幅には課金されません。

## <a id="scale"> </a>規模に合わせたアプリケーションの設計

1 つのアプリケーション (単純な Web サイトなど) だけを Azure にホストするケースもあります。しかし、すべて連動する複数のロールでアプリケーションが構成されていることはよくあります。たとえば、以下の図では、Web サイト ロールの 2 つのインスタンス、注文処理ロールの 3 つのインスタンス、レポート ジェネレーター ロールの 1 つのインスタンスがあります。これらのロールはいずれも連動し、そのすべてのコードを単一のユニットとしてパッケージ化して Azure にデプロイできます。

![image][4]

アプリケーションを異なるロールに分割し、そのそれぞれで個別のロール インスタンス (VM) のセットを実行する主な理由は、ロールのスケールを個別に調整できるようにすることです。たとえば、ホリデー シーズンには、会社の製品を購入する顧客が増えるため、Web サイト ロールを実行するロール インスタンスと共に、注文処理ロールを実行するロール インスタンスも増やす必要があります。ホリデー シーズンが終わったら、多数の返品がある可能性があるため、Web サイト インスタンスは引き続き多数必要ですが、注文処理インスタンスは減らしてもかまいません。それ以外の時期は、Web サイト インスタンスも注文処理インスタンスも少数でかまいません。全シーズンを通じて、必要なレポート ジェネレーター インスタンスは 1 つだけです。Azure におけるロールベースのデプロイメントの柔軟性を活かして、アプリケーションをビジネス ニーズに合わせて簡単に調整できます。

ホステッド サービス内のロール インスタンスが相互に通信するケースは珍しくありません。たとえば、Web サイト ロールは顧客の注文を受信し、その注文処理を注文処理ロール インスタンスにオフロードします。処理をロール インスタンスのセット間で受け渡しする方法としては、Azure が提供するキュー テクノロジ (Queue サービスまたは Service Bus キュー) を使用するのが最適です。キューの使用は、この説明の重要な部分です。キューを利用することで、ホステッド サービスのロールのスケールを個別に調整できるため、コストに対するワークロードのバランスを取ることができます。徐々にキュー内のメッセージの数が増えてきたら、注文処理ロール インスタンスの数をスケール アップできます。徐々にキュー内のメッセージの数が減ってきたら、注文処理ロール インスタンスの数をスケール ダウンできます。この方法なら、支払いは、実際のワークロードを処理するのに必要なインスタンスの分だけで済みます。

キューには信頼性も備わっています。注文処理ロール インスタンスの数をスケール ダウンする際、どのインスタンスを終了するかは Azure によって決定されます。キュー メッセージを処理している最中のインスタンスが終了されることもあるものの、メッセージの処理は正常に完了しないため、そのメッセージは別の注文処理ロール インスタンスに再度認識され、そのインスタンスによって処理されます。このようなキュー メッセージの検出により、メッセージは最終的には必ず処理されます。キューはロード バランサーとしても機能し、メッセージの要求元のすべてのロール インスタンスにメッセージを効果的に配信します。

Web サイト ロール インスタンスでは、受信するトラフィックを監視できるため、インスタンスの数をスケール アップするかスケール ダウンするかを決定できます。キューにより、注文処理ロール インスタンスとは別に Web サイト ロール インスタンスの数を調整できます。これは非常に強力なため、柔軟性がかなり高まります。もちろん、アプリケーションにそれ以外のロールが含まれている場合は、ロール間の通信手段としてキューを追加できるため、同じスケーリングとコストのメリットを享受できます。

## <a id="defandcfg"> </a>ホステッド サービスの定義と構成

ホステッド サービスを Azure にデプロイするには、サービス定義ファイルとサービス構成ファイルも必要です。どちらのファイルも XML ファイルであり、宣言によってホステッド サービスのデプロイメント オプションを指定できます。サービス定義ファイルには、ホステッド サービスを構成するすべてのロールと、そのロール間の通信方法が記述されます。サービス構成ファイルには、各ロールのインスタンスの数と、各ロール インスタンスの構成に使用される設定が記述されます。

## <a id="def"> </a>サービス定義ファイル

既に述べたように、サービス定義 (CSDEF) ファイルはアプリケーション全体を構成するさまざまなロールが記述された XML ファイルです。XML ファイルのスキーマ全体については、[http://msdn.microsoft.com/library/windowsazure/ee758711.aspx][] を参照してください。CSDEF ファイルにはアプリケーションに必要な各ロールの WebRole または WorkerRole 要素が含まれます。(WebRole 要素を使って) ロールを Web ロールとしてデプロイした場合、コードは Windows Server 2008 とインターネット インフォメーション サービス (IIS) を含むロール インスタンスで実行されます。(WorkerRole 要素を使って) ロールを worker ロールとしてデプロイした場合、ロール インスタンスでは Windows Server 2008 が実行されます (IIS はインストールされません)。

他のメカニズムを使用して受信 Web 要求をリッスンする worker ロールを作成してデプロイできます (コードで .NET HttpListener を作成して使用するなど)。どのロール インスタンスでも Windows Server 2008 が実行されるため、Windows Server 2008 で実行されるアプリケーションで問題なく利用できる処理であれば、コードで実行することができます。

ロールごとに、そのロールのインスタンスで使用する必要な VM サイズを指定します。以下の表に、現在利用できるさまざまな VM サイズと、それぞれの属性を示します。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**VM サイズ**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**ディスク**

</td>
<td>
**ピーク  
ネットワーク I/O**

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra Small**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20 GB

</td>
<td>
5 Mbps 以下

</td>
</tr>
<tr align="left" valign="top">
<td>
**Small**

</td>
<td>
1 x 1.6 GHz

</td>
<td>
1.75 GB

</td>
<td>
225 GB

</td>
<td>
100 Mbps 以下

</td>
</tr>
<tr align="left" valign="top">
<td>
**Medium**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3.5 GB

</td>
<td>
490 GB

</td>
<td>
200 Mbps 以下

</td>
</tr>
<tr align="left" valign="top">
<td>
**Large**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1 TB

</td>
<td>
400 Mbps 以下

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra Large**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2 TB

</td>
<td>
800 Mbps 以下

</td>
</tr>
</tbody>
</table>
料金は、ロール インスタンスとして使用する VM ごとに時間単位で課金されます。また、ロール インスタンスからデータ センターの外に送信されたデータに対しても課金されます。データ デンターに到着するデータには課金されません。詳細については、[Azure の料金][]に関するページを参照してください。一般には、障害に対するアプリケーションの耐久性を高めるために、大きなロール インスタンスを少数使用するのではなく、小さなロール インスタンスを多数使用することをお勧めします。結局のところ、ロール インスタンスが少ないほど、そのいずれかで発生した障害がアプリケーション全体に及ぼす影響は大きくなります。また、既に述べたとおり、Microsoft が提供している 99.95% のサービス レベル契約を確保するには、ロールごとに少なくとも 2 つのインスタンスをデプロイする必要があります。

サービス定義 (CSDEF) ファイルも、アプリケーションの各ロールに関する多数の属性を指定する場所です。さらに便利な項目うち、いくつかを次に示します。

-   **証明書**: 証明書は、データを暗号化する場合か、Web サービスで SSL をサポートしている場合に使用します。すべての証明書を Azure にアップロードする必要があります。詳細については、「[証明書の管理][]」を参照してください。この XML 設定により、既にアップロードされている証明書がロール インスタンスの証明書ストアにインストールされ、アプリケーション コードで使用できるようになります。

-   **構成設定名**: ロール インスタンスでの実行中にアプリケーションで読み取る値に使用します。構成設定の実際の値は、コードを再度デプロイしなくてもいつでも更新できるサービス構成 (CSCFG) ファイルで設定します。実際に、アプリケーションは、ダウンタイムを招くことなく、変更された構成値を検出できるような方法でコーディングできます。

-   **入力エンドポイント**: *プレフィックス*.cloadapp.net という URL で外部に公開する HTTP、HTTPS、または TCP エンドポイント (およびポート) を指定します。Azure によってロールがデプロイされるときに、ロール インスタンスにファイアウォールが自動的に構成されます。

-   **内部エンドポイント**:これで、アプリケーションの一部としてデプロイされている他のロール インスタンスに公開する HTTP または TCP エンドポイントを指定します。内部エンドポイントにより、アプリケーション内のすべてのロール インスタンスが相互に通信できるようになりますが、アプリケーション外部のロール インスタンスからはアクセスできません。

-   **インポート モジュール**: オプションで、便利なコンポーネントをロール インスタンスにインストールできます。診断監視、リモート デスクトップ、Azure Connect (セキュリティで保護されたチャネルを経由してロール インスタンスが内部設置型リソースにアクセスできるようにする) のためのコンポーネントがあります。

-   **ローカル ストレージ**: アプリケーションで使用するロール インスタンスにサブディレクトリを割り当てます。詳細については、「[Data Storage Offerings in Azure (Azure におけるデータ ストレージ サービス)][]」を参照してください。

-   **スタートアップ タスク**: 起動時に、前提条件となるコンポーネントをロール インスタンスにインストールする手段です。このタスクは、必要な場合は昇格された管理者特権で実行できます。

## <a id="cfg"> </a>サービス構成ファイル

サービス構成 (CSCFG) ファイルは、アプリケーションを再度デプロイしなくても変更できる設定が記述される XML ファイルです。XML ファイルのスキーマ全体については、[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][] を参照してください。CSCFG ファイルには、アプリケーションの各ロールに対応する Role 要素が含まれています。CSCFG ファイルで指定できる項目のいくつかを次に示します。

-   **OS バージョン**: この属性により、アプリケーション コードを実行するすべてのロール インスタンスに使用するオペレーティング システム (OS) バージョンを選択できます。この OS は*ゲスト OS* と呼ばれ、新しい各バージョンには、ゲスト OS のリリース時に入手可能な最新のセキュリティ修正プログラムと更新プログラムが含まれます。osVersion 属性値を "\*" に設定した場合、新しいゲスト OS バージョンが利用可能になると、各ロール インスタンスでゲスト OS が自動的に更新されます。ただし、特定のゲスト OS バージョンを選択することで、自動更新を避けることができます。たとえば、osVersion 属性を "WA-GUEST-OS-2.8\_201109-01" という値に設定すると、すべてのロール インスタンスが [http://msdn.microsoft.com/library/hh560567.aspx][] に記載されているものを取得します。ゲスト OS のバージョンの詳細については、「[Azure ゲスト OS リリースと SDK の互換性対応表]」を参照してください。

-   **インスタンス**: この要素の値は、特定のロール向けのコードを実行するためにプロビジョニングするロール インスタンスの数を示します。Azure にはアプリケーションを再度デプロイしなくても新しい CSCFG ファイルをアップロードできるため、簡単にこの要素の値を変更し、新しい CSCFG ファイルをアップロードして、アプリケーション コードを実行するロール インスタンスの数を動的に増減できます。そのため、実際に必要なワークロードに合わせてアプリケーションのスケール アップとスケール ダウンを簡単に行うことができるほか、ロール インスタンスの実行にかかるコストを管理できます。

-   **構成設定値**: この要素は (CSDEF ファイルで定義されている) 設定の値を示します。ロールは実行中でもこれらの値を読み取ることができます。これらの構成設定値は、通常は SQL Database または Azure Storage への接続文字列に使用されますが、目的に応じて使用することもできます。

## <a id="hostedservices"> </a>ホステッド サービスの作成と展開

ホステッド サービスを作成する場合は、まず [Azure の管理ポータル]にアクセスし、ホステッド サービスをプロビジョニングします。具体的には、DNS プレフィックスと、コードを最終的に実行するデータ センターを指定します。次に、デプロイメント環境でサービス定義 (CSDEF) ファイルを作成し、アプリケーション コードを構築してから、これらすべてのファイルをサービス パッケージ (CSPKG) ファイルとしてパッケージ化します (zip)。さらに、サービス構成 (CSCFG) ファイルも準備する必要があります。ロールをデプロイするには、Azure サービス管理 API を使用して CSPKG ファイルと CSCFG ファイルをアップロードします。デプロイ後、Azure は (構成データに基づいて) ロール インスタンスをデータ センターでプロビジョニングし、アプリケーション コードをパッケージから抽出します。さらにそれをロール インスタンスにコピーし、インスタンスを起動します。これで、コードを実行できるようになります。

次の図は、開発コンピューター上で作成する CSPKG ファイルと CSCFG ファイルを示しています。CSPKG ファイルには、CSDEF ファイルのほか、2 つのロールのコードが含まれます。Azure サービス管理 API を使用して CSPKG ファイルと CSCFG ファイルをアップロードすると、ロール インスタンスがデータ センターに作成されます。この例では、CSCFG ファイルはロール \#1 のインスタンスを 3 つとロール \#2 のインスタンスを 2 つ作成する設定になっています。

![image][5]

ロールのデプロイ、アップグレード、再構成の詳細については、「[Deploying and Updating Azure Applications (Azure アプリケーションのデプロイと更新)][]
」を参照してください。<a id="Ref" name="Ref"></a>

## <a id="references"> </a>参照

-   [Azure 対応のホステッド サービスの作成][]

-   [Azure におけるホステッド サービスの管理][]

-   [Azure へのアプリケーションの移行][]

-   [Azure アプリケーションの構成][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>著者: Jeffrey Richter (Wintellect)</p>

</div>

  [Azure アプリケーション モデルの利点]: #benefits
  [ホステッド サービスの中心概念]: #concepts
  [ホステッド サービスの設計に関する考慮事項]: #considerations
  [規模に合わせたアプリケーションの設計]: #scale
  [ホステッド サービスの定義と構成]: #defandcfg
  [サービス定義ファイル]: #def
  [サービス構成ファイル]: #cfg
  [ホステッド サービスの作成と展開]: #hostedservices
  [参照]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure の料金]: http://www.windowsazure.com/ja-jp/pricing/calculator/
  [証明書の管理]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Azure ゲスト OS リリースと SDK の互換性対応表]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure 管理ポータル]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Deploying and Updating Azure Applications (Azure アプリケーションのデプロイと更新)]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/deploying-applications/
  [Azure 対応のホステッド サービスの作成]: http://msdn.microsoft.com/library/gg432967.aspx
  [Azure におけるホステッド サービスの管理]: http://msdn.microsoft.com/library/gg433038.aspx
  [Azure へのアプリケーションの移行]: http://msdn.microsoft.com/library/gg186051.aspx
  [Azure アプリケーションの構成]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
1.75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Medium**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3.5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Large**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**Extra Large**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
You are charged hourly for each VM you use as a role instance and you
are also charged for any data that your role instances send outside the
data center. You are not charged for data entering the data center. For
more information, see [Azure Pricing][]. In general, it is
advisable to use many small role instances as opposed to a few large
instances so that your application is more resilient to failure. After
all, the fewer role instances you have, the more disastrous a failure in
one of them is to your overall application. Also, as mentioned before,
you must deploy at least two instances for each role in order to get the
99.95% service level agreement Microsoft provides.

The service definition (CSDEF) file is also where you would specify many
attributes about each role in your application. Here are some of the
more useful items available to you:

-   **Certificates**. You use certificates for encrypting data or if
    your web service supports SSL. Any certificates need to be uploaded
    to Azure. For more information, see [Managing Certificates
    in Azure][]. This XML setting installs previously-uploaded
    certificates into the role instance's certificate store so that they
    are usable by your application code.

-   **Configuration Setting Names**. For values that you want your
    application(s) to read while running on a role instance. The actual
    value of the configuration settings is set in the service
    configuration (CSCFG) file which can be updated at any time without
    requiring you to redeploy your code. In fact, you can code your
    applications in such a way to detect the changed configuration
    values without incurring any downtime.

-   **Input Endpoints**. Here you specify any HTTP, HTTPS, or TCP
    endpoints (with ports) that you want to expose to the outside world
    via your *prefix*.cloadapp.net URL. When Azure deploys your
    role, it will configure the firewall on the role instance
    automatically.

-   **Internal Endpoints**. Here you specify any HTTP or TCP endpoints
    that you want exposed to other role instances that are deployed as
    part of your application. Internal endpoints allow all the role
    instances within your application to talk to each other but are not
    accessible to any role instances that are outside your application.

-   **Import Modules**. These optionally install useful components on
    your role instances. Components exist for diagnostic monitoring,
    remote desktop, and Azure Connect (which allows your role
    instance to access on-premises resources through a secure channel).

-   **Local Storage**. This allocates a subdirectory on the role
    instance for your application to use. It is described in more detail
    in the [Data Storage Offerings in Azure][] article.

-   **Startup Tasks**. Startup tasks give you a way to install
    prerequisite components on a role instance as it boots up. The tasks
    can run elevated as an administrator if required.

## <a id="cfg"> </a>The Service Configuration File

The service configuration (CSCFG) file is an XML file that describes
settings that can be changed without redeploying your application. The
complete schema for the XML file can be found here:
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][].
The CSCFG file contains a Role element for each role in your
application. Here are some of the items you can specify in the CSCFG
file:

-   **OS Version**. This attribute allows you to select the operating
    system (OS) version you want used for all the role instances running
    your application code. This OS is known as the *guest OS*, and each
    new version includes the latest security patches and updates
    available at the time the guest OS is released. If you set the
    osVersion attribute value to "\*", then Azure automatically
    updates the guest OS on each of your role instances as new guest OS
    versions become available. However, you can opt out of automatic
    updates by selecting a specific guest OS version. For example,
    setting the osVersion attribute to a value of
    "WA-GUEST-OS-2.8\_201109-01" causes all your role instances to get
    what is described on this web page:
    [http://msdn.microsoft.com/library/hh560567.aspx][]. For more
    information about guest OS versions, see [Managing Upgrades to the
    Azure Guests OS].

-   **Instances**. This element's value indicates the number of role
    instances you want provisioned running the code for a particular
    role. Since you can upload a new CSCFG file to Azure
    (without redeploying your application), it is trivially simple to
    change the value for this element and upload a new CSCFG file to
    dynamically increase or decrease the number of role instances
    running your application code. This allows you to easily scale your
    application up or down to meet actual workload demands while also
    controlling how much you are charged for running the role instances.

-   **Configuration Setting Values**. This element indicates values for
    settings (as defined in the CSDEF file). Your role can read these
    values while it is running. These configuration settings values are
    typically used for connection strings to SQL Database or to 
    Azure Storage, but they can be used for any purpose you desire.

## <a id="hostedservices"> </a>Creating and Deploying a Hosted Service

Creating a hosted service requires that you first go to the [Azure Management Portal] and provision a hosted service by specifying
a DNS prefix and the data center you ultimately want your code running
in. Then in your development environment, you create your service
definition (CSDEF) file, build your application code and package (zip)
all these files into a service package (CSPKG) file. You must also
prepare your service configuration (CSCFG) file. To deploy your role,
you upload the CSPKG and CSCFG files with the Azure Service
Management API. Once deployed, Azure, will provision role
instances in the data center (based upon the configuration data),
extract your application code from the package, copy it to the role
instances, and boot the instances. Now, your code is up and running.

The figure below shows the CSPKG and CSCFG files you create on your
development computer. The CSPKG file contains the CSDEF file and the
code for two roles. After uploading the CSPKG and CSCFG files with the
Azure Service Management API, Azure creates the role
instances in the data center. In this example, the CSCFG file indicated
that Azure should create three instances of role \#1 and two
instances of Role \#2.

![image][5]

For more information about deploying, upgrading, and reconfiguring your
roles, see the [Deploying and Updating Azure Applications][]
article.<a id="Ref" name="Ref"></a>

## <a id="references"> </a>References

-   [Creating a Hosted Service for Azure][]

-   [Managing Hosted Services in Azure][]

-   [Migrating Applications to Azure][]

-   [Configuring an Azure Application][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>Written by Jeffrey Richter (Wintellect)</p>

</div>

  [Azure Application Model Benefits]: #benefits
  [Hosted Service Core Concepts]: #concepts
  [Hosted Service Design Considerations]: #considerations
  [Designing your Application for Scale]: #scale
  [Hosted Service Definition and Configuration]: #defandcfg
  [The Service Definition File]: #def
  [The Service Configuration File]: #cfg
  [Creating and Deploying a Hosted Service]: #hostedservices
  [References]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/ja-jp/pricing/calculator/
  [Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure Management Portal]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Deploying and Updating Azure Applications]: http://www.windowsazure.com/ja-jp/develop/net/fundamentals/deploying-applications/
  [Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx
