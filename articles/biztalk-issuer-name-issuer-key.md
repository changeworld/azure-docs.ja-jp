<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="発行者名および発行者キー" pageTitle="BizTalk サービスにおける発行者名と発行者キー | Azure" metaKeywords="BizTalk サービス、BizTalk、発行者名、発行者キー, Azure" description="BizTalk サービスのサービス バスまたは Access Control (ACS) の発行者名と発行者キーを取得する方法について説明します。" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk サービス: 発行者名および発行者キー" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />




# BizTalk サービス: 発行者名および発行者キー

Azure BizTalk サービスでは、サービス バス発行者名および発行者キーと Access Control 発行者名および発行者キーが使用されます。具体的には次の処理が行われます。


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>タスク</strong></td>
<td><strong>発行者名および発行者キー</strong></td>
</tr>
<tr>
<td>Visual Studio からアプリケーションをデプロイする</td>
<td>Access Control 発行者名および発行者キー</td>
</tr>
<tr>
<td>Azure BizTalk サービス ポータルを構成する</td>
<td>Access Control 発行者名および発行者キー</td>
</tr>
<tr>
<td>Visual Studio で BizTalk Adapter サービスを使用して LOB リレーを作成する</td>
<td>サービス バス発行者名および発行者キー</td>
</tr>
</table>


このトピックでは、発行者名と発行者キーを取得する手順を示します。

## Access Control 発行者名および発行者キー
Access Control 発行者名および発行者キーは次のコンポーネントで使用されます。

- Visual Studio で作成した Azure BizTalk サービス アプリケーション: Visual Studio の BizTalk サービス アプリケーションを Azure に正しくデプロイするために、Access Control 発行者名と発行者キーを入力します。
- Azure BizTalk サービス ポータル: BizTalk サービス ポータルに初めてログインするときに、BizTalk サービス名をサービス プロバイダーとして入力し、Access Control 発行者名と Access Control 発行者キーを入力します。

### Access Control 発行者名および発行者キーを取得するには

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。
2. 左のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックします。
3. BizTalk サービスを選択します。
4. タスク バーで **[接続情報]** をクリックします。Access Control 名前空間、既定の発行者 (発行者名)、既定のキー (発行者キー) が一覧に表示され、コピーして貼り付けることができます。<br/><br/>
まとめると次のようになります。<br/>
発行者名 = 既定の発行者名<br/>
発行者キー = 既定のキー


**[ACS 管理ポータルを開く]** をクリックして Access Control の値を取得することもできます。

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。
2. 左のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックします。
3. BizTalk サービスを選択します。
4. [接続情報] をクリックし、**[ACS 管理ポータルを開く]** をクリックします。
5. **[サービスの設定]** にあるポータルで、**[サービス ID]** をクリックします。これにより、サービス ID が表示されます。これは Access Control 発行者名の値です。サービス ID リンクをクリックすると、パスワードが表示されます。これは発行者キーの値です。これらの値はコピーできます。<br/><br/>
たとえば、**[サービス ID]** に "所有者" と表示されているとします。"所有者" は Access Control 発行者名です。"所有者" リンクをクリックすると、**パスワード**が表示されます。"パスワード" リンクをクリックすると、値が表示されます。このパスワード値は Access Control 発行者キーです。<br/><br/>
まとめると次のようになります。<br/>
発行者名 = サービス ID の名前<br/>
発行者キー = パスワード値

左側のナビゲーション ウィンドウで **[Active Directory]** をクリックして Access Control の値を取得することもできます。

<div class="dev-callout"> 
<b>重要</b>
<p><strong>Active Directory</strong> を使用して Access Control 名前空間を作成すると、サービス ID は自動的に作成<strong>されません</strong>。BizTalk サービスをプロビジョニングすると、Access Control 名前空間、"所有者" (発行者名) という名前のサービス ID、パスワード (発行者キー)、および対称キーが自動的に作成されます。</p>
<p>「<a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">方法: ACS 管理サービスを使用してサービス ID を構成する</a>」には、Access Control サービス ID に関する詳しい情報が記載されています。</p>
</div>


## サービス バス発行者名および発行者キー
サービス バス発行者名および発行者キーは BizTalk Adapter サービスで使用されます。Visual Studio の BizTalk サービス プロジェクトでは、BizTalk Adapter サービスを使用して内部設置型の基幹業務 (LOB) システムに接続します。接続するには、LOB リレーを作成し、LOB システムの詳細を入力します。このとき、サービス バス発行者名と発行者キーも入力します。

### サービス バス発行者名および発行者キーを取得するには

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にログインします。
2. 左のナビゲーション ウィンドウで、**[サービス バス]** をクリックします。
3. 名前空間をクリックします。タスク バーで **[接続情報]** をクリックします。これにより、**既定の発行者** (発行者名) と**既定のキー** (発行者キー) が表示されます。これらの値はコピーできます。<br/><br/>
まとめると次のようになります。<br/>
発行者名 = 既定の発行者名<br/>
発行者キー = 既定のキー

## 次のステップ
Azure BizTalk サービスに関するその他のトピック:

-  [Azure BizTalk サービス SDK のインストール](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [チュートリアル: Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## 関連項目
-  [方法: ACS 管理サービスを使用してサービス ID を構成する](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: Azure 管理ポータルを使用した BizTalk サービスのプロビジョニングに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: プロビジョニングの状態のチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk サービス: バックアップと復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk サービス: 調整に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

