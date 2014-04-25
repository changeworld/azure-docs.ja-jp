<properties linkid="provisioning-biztalk-service" urlDisplayName="管理ポータルでの BizTalk サービスのプロビジョニング" pageTitle="管理ポータルでの BizTalk サービスのプロビジョニング | Azure" metaKeywords="Azure BizTalk サービスの使用、プロビジョニング、Azure 非構造化データ" description="Azure 管理ポータルで BizTalk サービスをプロビジョニングする方法と、オプションの SQL データベース サーバーおよびストレージ アカウントを作成する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />



# BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング

<div class="dev-callout">
<b>ヒント</b>
<p>Azure 管理ポータルにログインするには、Azure アカウントと Azure サブスクリプションが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://go.microsoft.com/fwlink/p/?LinkID=239738">Azure の無料評価版のサイト</a>を参照してください。</p>
</div>

Azure BizTalk サービスは、次のコンポーネントで構成されています。

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>要件</strong></td>
        <td><strong>説明</strong></td>
</tr>
<tr>
<td>Azure サブスクリプション</td>
<td><p>サブスクリプションは、Azure 管理ポータルへのアクセスを管理し、<a HREF="https://account.windowsazure.com/Subscriptions">Azure サブスクリプション</a>で Azure アカウント保有者により作成されます。</p>
<p>Azure アカウントは複数のサブスクリプションを持つことができます。Azure アカウントは、Azure アカウント保有者が管理することも、別のユーザーやグループが管理することもできます。たとえば、Azure アカウント保有者が <em>BizTalkServiceSubscription</em> という名前のサブスクリプションを作成して、社内の BizTalk 管理者 (たとえば ContosoBTSAdmins@live.com) にこのサブスクリプションへのアクセスを付与するとします。このシナリオでは、BizTalk 管理者は、Azure 管理ポータルにログインすると、Azure BizTalk サービスを含む、サブスクリプション内のすべてのホステッド サービスに対する完全な管理者権限を持つことになります。BizTalk 管理者は Azure アカウント保有者ではないため、課金情報へはアクセスできません。</p>
Azure アカウントとサブスクリプションの詳細については、「<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">アカウント、サブスクリプション、管理ロールの管理</a>」を参照してください。
</td>
</tr>
<tr>
<td>Azure SQL データベース</td>
<td><p>SQL データベースには、Azure BizTalk サービスで使用されるテーブル、ビュー、およびストアド プロシージャが格納されます。</p>
<p>BizTalk サービスをプロビジョニングするときは、既存の Azure SQL Server や Azure SQL データベースを使用するか、新しいサーバーやデータベースを自動的に作成することができます。新しい Azure SQL Server とデータベースを作成することを選択した場合、Azure サービスが自動的に有効になります。</p>
<p>既存の Azure SQL Server で新しい Azure SQL データベースを作成した場合、サーバーのファイアウォール ルールは変更されません。このため、他の Azure サービスがサーバーのデータベースにアクセスできない可能性があります。</p>
SQL データベースの設定に最小スケール要件はありません。</td>
</tr>
<tr>
<td>Azure の Access Control 名前空間</td>
<td>Access Control 名前空間を使用して Azure BizTalk サービスで認証します。Visual Studio から BizTalk サービス プロジェクトをデプロイするときは、この Access Control 名前空間を入力します。BizTalk サービスのプロビジョニングを行うと、Access Control 名前空間が自動的に作成されます。</td>
</tr>

<tr>
<td>Azure ストレージ アカウント</td>
<td><p>Azure ストレージ アカウントにより、テーブル、BLOB、およびキューへのアクセスが付与されます。BizTalk サービスをプロビジョニングするときは、既存のストレージ アカウントを使用するか、新しいストレージ アカウントを自動的に作成することができます。テーブル、BLOB、およびキューは、BizTalk サービスで次を実行するために使用されます。</p>
<ul>
<li>BizTalk サービスを監視するログ ファイルが格納されます。Azure 管理ポータルの [監視] タブには監視出力も表示されます。</li>
<li>パートナー間で X12 または AS2 契約を作成する場合は、アーカイブ機能を有効にしてメッセージのプロパティを格納できます。このトラッキング データは、このストレージ アカウントに保存されます。</li>
</ul>
</td>
</tr>

<tr>
<td>SSL プライベート証明書</td>
<td><p>Azure BizTalk サービスをプロビジョニングするときに、BizTalk サービスの名前を含む URL を作成します。このプライベート SSL 証明書 (.pfx) は、BizTalk サービスの URL が要求されたときに HTTPS サーバー認証の証明書として使用されます。BizTalk サービスのプロビジョニングを行うと、自己署名 SSL 証明書が自動的に作成されます。</p>
<strong>重要な SSL 証明書情報</strong>

<ul>
<li>証明書の有効期限は、5 年未満にしてください。</li>
<li>すべてのプライベート証明書にはパスワードが必要です。このパスワードを把握し、管理者とこのパスワードを共有することをお勧めします。</li>
<li>自己署名証明書は、テストまたは開発環境で使用できます。自己署名証明書を使用するときは、証明書を [個人用] 証明書ストアと [信頼されたルート証明機関] 証明書ストアにインポートします。</li>
</ul>
<br/>運用証明書要求を証明機関に送信するときに、次の証明書プロパティを指定します。
<br/>

<ul>
<li><p><strong>拡張キー使用法</strong>サーバー認証の追加のキー使用法を
証明書で有効にすることができます。少なくとも、Azure BizTalk サービスにはサーバー認証が必要です。</p></li>
<li><p><strong>共通名</strong>Azure BizTalk サービス URL の完全修飾ドメイン名 (FQDN) を入力します。FQDN は、このトピックの「<a HREF="#BizTalk">BizTalk サービスをプロビジョニングする</a>」で BizTalk サービスをプロビジョニングするときに作成されます。</p>
<p>そのため、証明書要求を証明機関に送信するときに URL を把握しておく必要があります。BizTalk がプロビジョニングされると、新しい証明書または別の証明書を追加できます。</p></li>
</ul>
<br/>
</td>
</tr>
</table>


このトピックでは、次のような Azure BizTalk サービスをプロビジョニングする手順を示します。

-  [手順 1. BizTalk サービスをプロビジョニングする](#BizTalk)
-  [手順 2. プロビジョニング後の手順](#PostProv)
-  [省略可能: SQL データベース サーバーを作成する](#SQLDB)
-  [省略可能: ストレージ アカウントを作成する](#Storage)

##<a name="BizTalk"></a>手順 1. BizTalk サービスをプロビジョニングする

BizTalk サービスには、Azure BizTalk サービス アプリケーションがホストされます。BizTalk サービスのプロビジョニングを行うと、Access Control 名前空間と自己署名 SSL 証明書が自動的に作成されます。新しい Azure SQL データベースと新しいストレージ アカウントを作成することを選択できます。BizTalk がプロビジョニングされると、これらの要件の一部が更新される可能性があります。

次に、新しい Azure BizTalk サービスをプロビジョニングする手順を示します。

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。

2. ナビゲーション ウィンドウの下部にある **[+新規]** をクリックします。

	![[新規] をクリック][NEWButton]

3. **[アプリケーション サービス]**、**[BizTalk サービス]**、**[カスタム作成]** の順にクリックします。

	![[BizTalk サービス]、[カスタム作成] の順にクリックします。][NewBizTalkService]

4. 次の BizTalk サービス設定を入力します。

	<table border="1">
	<tr>
	<td><strong>BizTalk サービス名</strong></td>
	<td>BizTalk サービスの名前を入力してください。入力した名前には ".biztalk.windows.net" が自動的に追加されます。これにより、BizTalk サービスへのアクセスに使用する URL になります。任意の名前を入力できますが、具体的な名前を入力することをお勧めします。次に例をいくつか示します。<br/><br/>
	<em>mycompany</em>.biztalk.windows.net<br/>
	<em>mycompanymyapplication</em>.biztalk.windows.net<br/>
	<em>myapplication</em>.biztalk.windows.net
	</td>
	</tr>
	<tr>
	<td><strong>ドメイン URL</strong></td>
	<td><strong>省略可能</strong>。既定では、ドメイン URL は <em>YourBizTalkServiceName</em>.biztalk.windows.net です。カスタム ドメインを入力することもできます。たとえば、ドメインが <em>contoso</em> である場合は、次のように入力できます。<br/><br/>
	<em>MyCompany</em>.contoso.com<br/>
	<em>MyCompanyMyApplication</em>.contoso.com<br/>
	<em>MyApplication</em>.contoso.com<br/>
	<em>YourBizTalkServiceName</em>.contoso.com<br/>
	</td>
	</tr>
	<tr>
	<td><strong>エディション</strong></td>
	<td>次のオプションがあります。
	<ul>
	<li>開発者</li>
	<li>標準</li>
	<li>基本</li>
	<li>プレミアム</li>
	</ul>
	<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk サービスの開発者、基本、標準、およびプレミアム エディションのチャート</a>に関するページに、エディションの違いが記載されています。テスト/開発フェーズで作業している場合は、<strong>開発者</strong>を選択します。運用フェーズで作業している場合は、プレミアム、標準、または基本のどれがビジネス シナリオに適しているかを表で判断してください。
	</td>
	</tr>
	<tr>
	<td><strong>リージョン</strong></td>
	<td>BizTalk サービスをホストするリージョンを選択します。</td>
	</tr>
	<tr>
	<td><strong>トラッキング データベース</strong></td>
	<td><p>BizTalk サービスで使用されるテーブルを格納する SQL データベースを選択します。次のオプションから選択します。</p>
	<ul>
	<li><strong>[既存の SQL データベース インスタンスを使用する]</strong>: 既存の Azure SQL データベースを使用するには、このオプションを選択します。既存の Azure SQL データベースは、別の BizTalk サービスで使用されていない場合に使用できます。Azure SQL データベース サーバーの作成時に指定したログイン名とパスワードが必要です。</li>
	<li><p><strong>[新しい SQL データベース インスタンスを作成する]</strong>: 新しい SQL データベースを作成するには、このオプションを選択します。</p></li>
	<p><strong>注</strong></p>
	<p>新しい Azure SQL Server とデータベースを作成した場合、SQL データベースで Azure サービスが自動的に有効になります。BizTalk サービスを使用するには、Azure SQL データベースで Azure を有効にする必要があります。</p>
	<p><strong>ヒント</strong></p>
	トラッキング データベースと監視/アーカイブ ストレージ アカウントを BizTalk サービスと同じリージョンに作成します。
	</ul>
	</td>
	</tr>
	<tr>
	<td><strong>サブスクリプション</strong></td>
	<td><strong>省略可能</strong>。複数のサブスクリプションがある場合にのみ指定できます。BizTalk サービスをホストするサブスクリプションを選択します。</td>
	</tr>
	</table>
次へ進む矢印を選択します。

5. 次のデータベース設定を入力します。

	<table border="1">
	<tr>
	<td><strong>サブスクリプション</strong></td>
	<td><strong>省略可能</strong>。複数のサブスクリプションがある場合にのみ指定できます。Azure SQL データベースをホストするサブスクリプションを選択します。</td>
	</tr>
	<tr>
	<td><strong>データベース</strong></td>
	<td><p>前の画面で <strong>[既存の SQL データベース インスタンスを使用する]</strong> を選択した場合に指定できます。</p>
	BizTalk サービスで使用されるテーブルを格納する SQL データベースを選択します。
	</td>
	</tr>
	<tr>
	<td><strong>名前</strong></td>
	<td><p>前の画面で <strong>[新しい SQL データベース インスタンスを使用する]</strong> を選択した場合に指定できます。</p>
	BizTalk サービスにより使用される SQL データベース名を入力します。既定では、<em>YourBizTalkServiceName</em>_db と入力されます。</td>
	</tr>
	<tr>
	<td><strong>サーバー</strong></td>
	<td><p>前の画面で <strong>[新しい SQL データベース インスタンスを使用する]</strong> を選択した場合に指定できます。</p>
	既存の SQL データベース サーバーを選択します。または、<strong>[新しい SQL データベース サーバー]</strong> を選択して新しい SQL データベース サーバーを作成します。</td>
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
	<td><strong>[新しい SQL データベース インスタンスを作成する]</strong> を選択した場合に指定できます。SQL データベースをホストするリージョンを選択します。</td>
	</tr>
	</table>
次へ進む矢印を選択します。

6. 次の Azure 監視設定を入力します。

	<table border="1">
	<tr>
	<td><strong>ストレージ アカウントの監視/アーカイブ</strong></td>
	<td>既存のストレージ アカウントを選択するか、<strong>[新しいストレージ アカウントを作成する]</strong> を選択します。</td>
	</tr><tr>
	<td><strong>ストレージ アカウント名</strong></td>
	<td><strong>[新しいストレージ アカウントを作成する]</strong> を選択した場合に指定できます。BizTalk サービスにより使用されるストレージ アカウントの名前を入力します。</td>
	</tr>
	</table>

チェック マークをオンにして、ウィザードを完了します。完了すると、進行状況アイコンが表示されます。<br/>

![完了時に表示される進行状況アイコン][ProgressComplete]


完了すると、Azure BizTalk サービスがプロビジョニングされ、アプリケーションで使用できるようになります。

既定の設定で十分です。既定の設定を変更する場合は、左側のナビゲーション ウィンドウで **[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。その他の設定は、[ダッシュボード]、[監視]、および [スケール] タブに表示されます。

BizTalk サービスの状態によっては、一部の操作を完了できません。それらの操作の一覧については、[BizTalk サービスの状態のグラフ](http://go.microsoft.com/fwlink/p/?LinkID=329870)に関するページを参照してください。

##<a name="PostProv"></a>手順 2. プロビジョニング後の手順

このセクションでは、次の手順を示します。

-  [プライベート証明書の追加](#AddCert)
-  [Access Control 名前空間の取得](#ACS)

####<a name="AddCert"></a>プライベート証明書の追加
Azure BizTalk サービスをプロビジョニングすると、BizTalk サービスの名前を含む URL が作成されます。プライベート SSL 証明書 (.pfx) は、BizTalk サービスの URL が要求されたときに HTTPS サーバー認証の証明書として使用されます。

BizTalk サービスのプロビジョニングを行うと、自己署名 SSL 証明書が自動的に作成されます。この証明書は、BizTalk サービス ダッシュボードでダウンロードまたは置き換えることができます。自己署名証明書は、開発環境で使用されます。

運用対応証明書を追加するには

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。
2. 左側のナビゲーション ウィンドウで **[BizTalk サービス]** をクリックし、目的の BizTalk サービスを選択します。
3. **[ダッシュボード]** タブを選択します。
4. **[SSL 証明書の更新]** を選択します。<br/><br/>
![SSL 証明書の変更][QuickGlance]

5. BizTalk サービス名を含むプライベート SSL 証明書 (*CertificateName*.pfx) に移動し、パスワードを入力して、チェック マークをオンにします。


####<a name="ACS"></a>Access Control 名前空間の取得

Access Control 名前空間を使用して Azure BizTalk サービスで認証します。Visual Studio から BizTalk サービス プロジェクトを展開するときは、この Access Control 名前空間を入力します。

BizTalk サービスの Access Control 名前空間が自動的に作成されます。Access Control 名前空間、既定の発行者、発行者キーを取得するには、BizTalk サービス ダッシュボードで **[接続情報]** をクリックします。

Access Control 名前空間を取得するには

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。
2. 左側のナビゲーション ウィンドウで **[BizTalk サービス]** をクリックし、目的の BizTalk サービスを選択します。
3. タスク バーで **[接続情報]** をクリックします。<br/><br/>
![[接続情報] の選択][ACSConnectInfo]

Access Control の値をコピーして貼り付けることができます。

Access Control 名前空間が作成されると、Access Control の値をどのアプリケーションでも使用することができます。Azure BizTalk サービスをプロビジョニングするときに、この Access Control 名前空間で、BizTalk サービスの展開での認証を制御します。サブスクリプションを変更する場合や名前空間を管理する場合は、左側のナビゲーション ウィンドウで **[Active Directory]** をクリックし、名前空間を選択します。下のナビゲーション ウィンドウにオプションが示されます。

**[管理]** をクリックすると、Access Control 管理ポータルが開きます。Access Control 管理ポータルでは、BizTalk サービスにより**サービス ID** が使用されます。<br/><br/>
![Access Control 管理ポータルにおける ACS サービス ID][ACSServiceIdentities]

Access Control サービス ID は、アプリケーションまたはクライアントが Access Control で直接認証してトークンを受け取るための一連の資格情報です。

**重要**<br/>
BizTalk サービスでは、既定のサービス ID の**所有者**と**パスワード**値が使用されます。パスワード値の代わりに対称キー値を使用した場合、次のエラーが発生する可能性があります。

*指定された資格情報を使用して Access Control 管理サービス アカウントに接続できませんでした*

ガイドラインと推奨事項については、[ACS 名前空間の管理](http://go.microsoft.com/fwlink/p/?LinkID=285670)に関するページを参照してください。




##<a name="SQLDB"></a>省略可能: SQL データベース サーバーを作成する

Azure BizTalk サービスをプロビジョニングすると、新しい SQL データベース サーバーが自動的に作成されます。BizTalk サービスから独立した SQL データベース サーバーを作成する場合は、[.NET アプリケーションで Azure SQL データベースを使用する方法](http://go.microsoft.com/fwlink/p/?LinkID=251285)に関するページを参照してください。

完了すると、新しい Azure SQL データベースにログインして、テーブル、ビュー、およびストアド プロシージャを作成できるようになります。

既定では、SQL データベースのスケールは次の設定で構成されます。

- Web Edition
- 1 GB のデータベース サイズ

BizTalk サービスには、既定の構成で十分です。スケールの構成設定を変更する場合は、左側のナビゲーション ウィンドウで **[SQL データベース]** をクリックし、SQL データベースをダブルクリックして、**[構成]** タブを選択します。スケールを変更すると、料金に影響する可能性があります。エディションと課金については、「[Azure SQL データベースのアカウントと課金](http://go.microsoft.com/fwlink/p/?LinkID=234930)」を参照してください。


##<a name="Storage"></a>省略可能: ストレージ アカウントを作成する

Azure BizTalk サービスをプロビジョニングすると、Azure ストレージ アカウントが自動的に作成されます。BizTalk サービスから独立した Azure ストレージ アカウントを作成する場合は、[ストレージ アカウントの作成方法](http://go.microsoft.com/fwlink/p/?LinkID=279823)に関するページを参照してください。

完了すると、テーブル、BLOB、およびキューへのアクセスを提供する新しい Azure ストレージ アカウントが作成されます。

BizTalk サービスには、既定の設定で十分です。既定の設定を変更する場合は、左側のナビゲーション ウィンドウで **[ストレージ]** をクリックし、ストレージ アカウントを選択します。設定は、[ダッシュボード]、[監視]、[構成]、および [コンテナー] の各タブに表示されます。

ストレージ アカウントを作成すると、プライマリ キーおよびセカンダリ キーが自動的に作成されます。これらのキーは、ストレージ アカウントへのアクセスを制御します。BizTalk サービスは自動的にプライマリ キーを使用します。

[[ストレージ]](http://go.microsoft.com/fwlink/p/?LinkID=285671)には、ストレージ アカウントに関する情報が表示されます。



## 次のステップ

これで BizTalk サービスがプロビジョニングされました。[BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)に関するページでは、さまざまなタブについて理解を深めることができます。BizTalk サービスをアプリケーションで使用する準備ができました。アプリケーションの作成を開始するには、[Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)に関するページを参照してください。

## 関連項目
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: BizTalk サービスの状態のグラフに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk サービス: バックアップと復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk サービス: 調整に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk サービス: 発行者名および発行者キーに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png

