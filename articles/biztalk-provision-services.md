<properties urlDisplayName="Provision BizTalk Services in management portal" pageTitle="管理ポータルでの BizTalk サービスの作成 | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Azure の管理ポータルで BizTalk サービスをプロビジョニングする方法と、オプションの SQL データベース サーバーおよびストレージ アカウントを作成する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk サービス: Azure の管理ポータルを使用したプロビジョニング" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Azure 管理ポータルを使用して BizTalk サービスを作成する

このトピックでは、Azure 管理ポータルで Azure BizTalk サービスを作成する手順を説明します。具体的には次の処理が行われます。

-   [BizTalk サービスを作成する][BizTalk サービスを作成する]
-   [プロビジョニング後の手順][プロビジョニング後の手順]
-   [要件の説明][要件の説明]
-   [ハイブリッド接続 - New!][ハイブリッド接続 - New!]

<div class="dev-callout">
<b>ヒント</b> 
<p>Azure 管理ポータルにログインするには、Azure アカウントと Azure サブスクリプションが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。<a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Azure 無料評価版のサイト</a>を参照してください。</p> 
</div>

## <a name="BizTalk"></a>BizTalk サービスを作成する

選択したエディションによっては、BizTalk サービスの設定の一部が使用できないことがあります。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  一番下のナビゲーション ウィンドウで **[新規]** を選択します。
<br/>
    ![新規 ボタンを選択]( ./media/biztalk-provision-services/WABS_New.png)

3.  **[アプリケーション サービス]** \> **[BizTalk サービス]** \> **[カスタム作成]** の順に選択します。
<br/>
![BizTalk サービス と カスタム作成 を選択]( ./media/biztalk-provision-services/WABS_NewBizTalkService.png)

4.  BizTalk サービスの設定を入力します。

	<table border="1">
	<tr>
	<td><strong>BizTalk サービス名</strong></td>
	<td>どのような名前でも入力できますが、具体的な名前を入力してください。 次に例をいくつか示します。<br/><br/>
	<em>mycompany</em>.biztalk.windows.net<br/>
	<em>mycompanymyapplication</em>.biztalk.windows.net<br/>
	<em>myapplication</em>.biztalk.windows.net<br/><br/>
入力した名前には ".biztalk.windows.net" が自動的に追加されます。これで、この BizTalk サービスにアクセスするための URL が作成され、たとえば <strong>https://<em>myapplication</em>.biztalk.windows.net</strong>となります。
	</td>
	</tr>
	<tr>
	<td><strong>エディション</strong></td>
	<td>テスト/開発フェーズで作業している場合は <strong>開発者</strong>. テスト/開発フェーズで作業している場合は <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk サービス: エディションのチャート</a> を使用して <strong>プレミアム</strong>, <strong>標準</strong>, or <strong>基本</strong> のどれが実際のビジネス シナリオに最適であるかを判断してください。	 
	</td>
	</tr>
	<tr>
	<td><strong>リージョン</strong></td>
	<td>BizTalk サービスをホストするリージョンを選択します。</td>
	</tr>
	<tr>
	<td><strong>ドメイン URL</strong></td>
	<td><strong>省略可能</strong>。既定では、ドメイン URL は<em>YourBizTalkServiceName</em>.biztalk.windows.net. です。カスタム ドメインを入力することもできます。たとえば、ドメインが <em>contoso</em>, である場合は、次のように入力できます。 <br/><br/>
	<em>MyCompany</em>.contoso.com<br/>
	<em>MyCompanyMyApplication</em>.contoso.com<br/>
	<em>MyApplication</em>.contoso.com<br/>
	<em>YourBizTalkServiceName</em>.contoso.com<br/>
	</td>
	</tr>
	</table>

5.  ストレージとデータベースの設定を入力します。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>ストレージ アカウントの監視/アーカイブ</strong></td>
    <td align="left">既存のストレージ アカウントを選択するか、新しいストレージ アカウントの作成を選択します。<br /><br /> 新しいストレージ アカウントを作成する場合は、<strong>[ストレージ アカウント名]</strong> に入力します。</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>トラッキング データベース</strong></td>
    <td align="left">既存の Azure SQL データベースを使用する場合に、そのデータベースを別の BizTalk サービスでも使用することはできません。Azure SQL データベース サーバーの作成時に入力したログイン名とパスワードが必要です。<br /><br />
    <div class="dev-callout">
    <strong>ヒント</strong>
    <p>トラッキング データベースと監視/アーカイブ ストレージ アカウントを BizTalk サービスと同じリージョンに作成します。</p>
    </div></td>
    </tr>
    </tbody>
    </table>

    次へ進む矢印を選択します。

6.  データベースの設定を入力します。

	<table border="1">
	<tr>
	<td><strong>名前</strong></td>
	<td>前の画面で <strong>新しい SQL データベース インスタンスを使用する</strong> を選択した場合に指定できます。
	<br/><br/>
	BizTalk サービスで使用される SQL データベース名を入力します。</td>
	</tr>
	<tr>
	<td><strong>サーバー</strong></td>
	<td>前の画面で <strong>新しい SQL データベース インスタンスを使用する</strong> を選択した場合に指定できます。
	<br/><br/>
	既存の SQL データベース サーバーを選択するか、新しい SQL データベース サーバーを作成します。</td>
	</tr>
	<tr>
	<td><strong>サーバー ログイン名</strong></td>
	<td>ログイン ユーザー名を入力します。</td>
	</tr>
	<tr>
	<td><strong>サーバー ログイン パスワード</strong></td>
	<td>ログイン パスワードを入力します。</td>
	</tr>
	<tr>
	<td><strong>リージョン</strong></td>
	<td><strong>新しい SQL データベース インスタンスを作成する</strong> を選択した場合に指定可能になります。SQL データベースをホストするリージョンを選択します。</td>
	</tr>
	</table>

チェック マークをオンにして、ウィザードを完了します。進行状況のアイコンが表示されます。
<br/>
![完了すると進行状況のアイコンが表示される][完了すると進行状況のアイコンが表示される]

完了すると、Azure BizTalk サービスが作成され、アプリケーションで使用できる状態になります。既定の設定で十分です。既定の設定を変更する場合は、左側のナビゲーション ウィンドウで **[BizTalk サービス]** を選択し、BizTalk サービスを選択します。追加の設定が、一番上の [[ダッシュボード]、[監視]、および [スケール] の各タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)に表示されます。

BizTalk サービスの状態によっては、一部の操作を完了できません。これに該当する操作の一覧については、[BizTalk サービスの状態のチャート][BizTalk サービスの状態のチャート]を参照してください。

## <a name="PostProv"></a>プロビジョニング後の手順

-   [証明書をローカル コンピューターにインストールする][証明書をローカル コンピューターにインストールする]
-   [運用対応証明書を追加する][運用対応証明書を追加する]
-   [Access Control 名前空間を取得する][Access Control 名前空間を取得する]

#### <a name="InstallCert"></a>証明書をローカル コンピューターにインストールする

BizTalk サービスをプロビジョニングするときに、自己署名証明書が作成されて BizTalk サービス サブスクリプションに関連付けられます。コンピューターから BizTalk サービス アプリケーションをデプロイする、または BizTalk サービス エンドポイントにメッセージを送信するには、この証明書をダウンロードしてそのコンピューターにインストールする必要があります。

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  左側のナビゲーション ウィンドウで **[BizTalk サービス]** をクリックし、BizTalk サービス サブスクリプションを選択します。
3.  **[ダッシュボード]** タブをクリックします。
4.  **[SSL 証明書のダウンロード]** をクリックします。
![SSL 証明書の変更][SSL 証明書の変更]
5.  証明書をダブルクリックし、ウィザードを最後まで実行して証明書をインストールします。この証明書は必ず **[信頼されたルート証明機関]** ストアにインストールしてください。

#### <a name="AddCert"></a>運用対応証明書を追加する

BizTalk サービスのプロビジョニング時に自動的に作成される自己署名証明書は、開発環境に限定して使用するためのものです。本稼働のときは、この証明書を運用対応証明書で置き換える必要があります。

1.  **[ダッシュボード]** タブで、**[SSL 証明書の更新]** をクリックします。
2.  BizTalk サービス名が含まれているプライベート SSL 証明書 (*CertificateName*.pfx) を一覧から選択し、パスワードを入力して、チェック マークを選択します。

#### <a name="ACS"></a>Access Control 名前空間を取得する

1.  [Azure 管理ポータル][Azure 管理ポータル]にログインします。
2.  左側のナビゲーション ウィンドウで **[BizTalk サービス]** を選択し、BizTalk サービスを選択します。
3.  タスク バーで、**[接続情報]** を選択します。
<br/>
![接続情報 を選択][択]

4.  Access Control の値をコピーします。

Visual Studio から BizTalk サービス プロジェクトを展開するときは、この Access Control 名前空間を入力します。BizTalk サービスの Access Control 名前空間が自動的に作成されます。

Access Control の値は、どのアプリケーションでも使用できます。Azure BizTalk サービスが作成されると、この Access Control 名前空間によって BizTalk サービス デプロイメントでの認証が制御されます。サブスクリプションを変更する場合や名前空間を管理する場合は、左側のナビゲーション ウィンドウで **[Active Directory]** をクリックし、名前空間を選択します。タスク バーに、選択可能なオプションが表示されます。

**[管理]** をクリックすると、Access Control 管理ポータルが開きます。Access Control 管理ポータルでは、BizTalk サービスにより**サービス ID** が使用されます。
<br/>
![Access Control 管理ポータルにおける ACS サービス ID][Access Control 管理ポータルにおける ACS サービス ID]

Access Control サービス ID は、アプリケーションまたはクライアントが Access Control で直接認証してトークンを受け取るための一連の資格情報です。

**重要**
BizTalk サービスでは、**所有者**が既定のサービス ID として使用され、**パスワード**の値が使用されます。パスワード値の代わりに対称キー値を使用した場合、次のエラーが発生する可能性があります。

*指定された資格情報を使用して Access Control 管理サービス アカウントに接続できませんでした*

ガイドラインと推奨事項については、[ACS 名前空間の管理][ACS 名前空間の管理]に関するページを参照してください。

## <a name="Requirements"></a>要件の説明

これらの要件は、無料エディションには適用されません。

<table border="1">

<tr bgcolor="FAF9F9">

<td>
<b>必要なもの</b>

</td>

<td>
<b>必要である理由</b>

</td>

</tr>

<tr>

<td>
Azure サブスクリプション

</td>

<td>
どのユーザーが Azure 管理ポータルにログインできるかは、サブスクリプションによって決まります。アカウント保有者がサブスクリプションを <a HREF="https://account.windowsazure.com/Subscriptions">Azure サブスクリプションのページ</a>で作成します。
<br/><br/>
つの Azure アカウントで複数のサブスクリプションを契約することもでき、管理は許可を受けたユーザーであればだれでも行うことができます。たとえば、Azure アカウント保有者が <i>BizTalkServiceSubscription</i> という名前のサブスクリプションを作成して、社内の BizTalk 管理者 (たとえば ContosoBTSAdmins@live.com) にこのサブスクリプションへのアクセスを付与するとします。このシナリオでは、BizTalk 管理者は、Azure 管理ポータルにログインすると、Azure BizTalk サービスを含む、サブスクリプション内のすべてのホステッド サービスに対する完全な管理者権限を持つことになります。BizTalk 管理者は Azure アカウント保有者ではないため、課金情報へはアクセスできません。<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">Azure 管理ポータルでのサブスクリプションとストレージ アカウントの管理に関するページ</a>に、詳しい説明があります。

</td>

</tr>

<tr>

<td>
Azure SQL Database

</td>

<td>
BizTalk サービスで使用されるテーブル、ビュー、およびストアド プロシージャが格納されます。これには、トラッキングのデータも含まれます。
<br/><br/>
BizTalk サービスを作成するときに、既存の Azure SQL サーバーまたは Azure SQL データベースを使用することも、新しいサーバーまたはデータベースを自動的に作成することもできます。
<br/><br/>
SQL データベースのスケールは自動的に構成されます。一般的に、BizTalk サービスのスケールは既定の設定で十分です。スケールを変更すると、料金に影響が及びます。「<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Azure SQL データベースのアカウントと課金</a>」を参照してください。
<br/><br/>
<strong>注</strong>
<br/>
<ul>
<li>新しい Azure SQL サーバーとデータベースを作成すると、Azure サービスが自動的に有効になります。BizTalk サービスを使用するには、Azure サービスが有効になっている必要があります。</li>
<li>既存の Azure SQL サーバー上に新しい Azure SQL データベースを作成した場合に、サーバーのファイアウォール ルールが変更されることはありません。その結果として、他の Azure サービスがこのサーバーのデータベースにアクセスできなくなる可能性があります。</li>
</ul>
</td>

</tr>

<tr>

<td>
Azure の Access Control 名前空間

</td>

<td>
Azure BizTalk サービスで認証を行います。Visual Studio から BizTalk サービス プロジェクトをデプロイするときは、この Access Control 名前空間を入力します。BizTalk サービスを作成すると、Access Control 名前空間が自動的に作成されます。

</td>

</tr>
</p>
<tr>
<td>
Azure ストレージ アカウント

</td>
<td>
次のような BizTalk サービスのデータを保存するためのテーブル、BLOB、およびキューにアクセスできるようになります。

<ul>
<li>BizTalk サービスを監視するログ ファイル。監視出力は Azure 管理ポータルの [監視] タブにも表示されます。</li>
<li>パートナー間で X12 または AS2 契約を作成するときに、アーカイブ機能を有効にするとメッセージのプロパティを保存できます。このデータは、このストレージ アカウントに保存されます。</li>
</ul>
<br/>
BizTalk サービスを作成するときに、既存のストレージ アカウントを使用することも、新しいストレージ アカウントを自動的に作成することもできます。 
<br/><br/>
BizTalk サービスのストレージ設定は、既定の設定で十分です。 
<br/><br/>
ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。これらのキーは、ストレージ アカウントへのアクセスを制御します。BizTalk サービスは自動的にプライマリ キーを使用します。
<br/><br/>
詳細については、「<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">ストレージ</a>」を参照してください。

</td>
</tr>
<tr>
<td>
SSL プライベート証明書

</td>
<td>
Azure BizTalk サービスが作成されるときに、BizTalk サービス名が含まれている HTTPS URL も作成されます。この URL は自動的に、開発専用の自己署名証明書を使用するように構成されます。本稼働用には、プライベート SSL 証明書が必要です。
<br/><br/>
<strong>重要な SSL 証明書情報</strong>

<ul>
<li>証明書の有効期限は、5 年未満にしてください。</li>
<li>プライベート証明書はすべて、パスワードを必要とします。このパスワードを知っている必要があります。また、他の管理者にこのパスワードを知らせておくことをお勧めします。</li>
<li>自己署名証明書は、テスト/開発環境で使用されます。自己署名証明書を使用するときは、証明書を [個人用] 証明書ストアと [信頼されたルート証明機関] 証明書ストアにインポートします。</li>
</ul>
<br/>運用証明書要求を証明機関に送信するときに、次の証明書プロパティを指定します。
<br/>

<ul>
<li><strong>拡張キー使用法</strong>: 少なくとも、Azure BizTalk サービスにはサーバー認証が必要です。</li>
<li><strong>共通名</strong>: Azure BizTalk サービス URL の完全修飾ドメイン名 (FQDN) を入力します。このトピックの「<a HREF="#BizTalk">BizTalk サービスを作成する</a>」を参照してください。</li>
</ul>
<br/>
BizTalk サービスが作成された後で、新しい、または別の証明書を追加できます。

</td>
</tr>
</table>
## <a name="HC"></a>ハイブリッド接続

Azure BizTalk サービスを作成するときに、**ハイブリッド接続** タブが使用可能になります。

![ハイブリッド接続 タブ][HybridConnectionTab]

"ハイブリッド接続" を使用すると、Azure Web サイトや Azure モバイル サービスと、静的 TCP ポートを使用する内部設置型リソース (たとえば SQL Server、MySQL、HTTP Web API、Mobile Services、ほとんどのカスタム Web サービス) とを接続できます。ハイブリッド接続と BizTalk Adapter サービスは別のものです。BizTalk Adapter サービスは、Azure BizTalk サービスを内部設置型の基幹業務 (LOB) システムに接続するために使用されます。

ハイブリッド接続の作成と管理の方法などの情報については、「[ハイブリッド接続][ハイブリッド接続]」を参照してください。

## 次へ

BizTalk サービスが作成されたので、[BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281) のページでこれらのタブについての理解を深めてください。BizTalk サービスをアプリケーションで使用する準備ができました。アプリケーションの作成を開始するには、[Azure BizTalk サービス][Azure BizTalk サービス]に関するページを参照してください。

## 関連項目

-   [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート][BizTalk サービス: エディションのチャート]
-   [BizTalk サービス: 状態のチャート][BizTalk サービスの状態のチャート]
-   [BizTalk サービス: バックアップと復元][BizTalk サービス: バックアップと復元]
-   [BizTalk サービス: 調整][BizTalk サービス: 調整]
-   [BizTalk サービス: 発行者名および発行者キー][BizTalk サービス: 発行者名および発行者キー]
-   [Azure BizTalk サービス SDK の使用開始に関するページ][Azure BizTalk サービス SDK の使用開始に関するページ]
-   [Hybrid Connections][ハイブリッド接続]

  [BizTalk サービスを作成する]: #BizTalk
  [プロビジョニング後の手順]: #PostProv
  [要件の説明]: #Requirements
  [ハイブリッド接続 - New!]: #HC
  [Azure 無料評価版のサイト]: http://go.microsoft.com/fwlink/p/?LinkID=239738
  [Azure 管理ポータル]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [BizTalk サービス: エディションのチャート]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [完了すると進行状況のアイコンが表示される]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [BizTalk サービスの状態のチャート]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [証明書をローカル コンピューターにインストールする]: #InstallCert
  [運用対応証明書を追加する]: #AddCert
  [Access Control 名前空間を取得する]: #ACS
  [SSL 証明書の変更]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [択]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [Access Control 管理ポータルにおける ACS サービス ID]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [ACS 名前空間の管理]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [Azure サブスクリプションのページ]: https://account.windowsazure.com/Subscriptions
  [Azure 管理ポータルでのサブスクリプションとストレージ アカウントの管理に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=267577
  [Azure SQL データベースのアカウントと課金]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [ストレージ]: http://go.microsoft.com/fwlink/p/?LinkID=285671
  [ハイブリッド接続]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure BizTalk サービス]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk サービス: バックアップと復元]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [BizTalk サービス: 調整]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk サービス: 発行者名および発行者キー]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Azure BizTalk サービス SDK の使用開始に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=302335
  [HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
