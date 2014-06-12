<properties linkid="manage-linux-other-resources-endorsed-distributions" urlDisplayName="動作保証済みディストリビューション" pageTitle="Azure での動作保証済み Linux ディストリビューション" metaKeywords="" description="Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、OpenLogic、および SUSE に関する指針も含めて、説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure での動作保証済み Linux ディストリビューション" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





#Azure での動作保証済み Linux ディストリビューション

ギャラリーにあるディストリビューション イメージは、以下のパートナーから提供されており、マイクロソフトはコミュニティと協力して動作保証済みディストリビューションの数を増やそうと努めています。一方で、このページの指針に従えば、いつでも独自の Linux を展開することができます。

## Canonical##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。Canonical は、Ubuntu に関して、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイスを備え、携帯電話からクラウドまで統一されたフリー プラットフォームにするというビジョンを持っているため、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまで多様な企業が最初に選択するディストリビューションとなり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC からサーバーや携帯機器まで、さまざまな Ubuntu ソリューションを市場に送り出しています。

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic は、クラウドおよびデータ センター向けの企業オープン ソース ソリューションの一流プロバイダーです。OpenLogic は、多種多様な業界の一流企業数百社に対して、オープン ソース ソフトウェアを安全に入手し、サポートし、コントロールできるよう支援しています。OpenLogic は、600 種類のオープン ソース パッケージについて、OpenLogic Expert Community に支えられた商用品質のテクニカル サポートと保証を提供しています。たとえば、CentOS を企業レベルでサポートし、Azure に Centos イメージを提供する開設時パートナーでもあります。

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle の戦略は、顧客に Oracle クラウドおよびその他のクラウドにおいて Oracle ソフトウェアを展開する選択肢と幅広い運用を提供しながら、パブリック クラウドおよびプライベート クラウドにソリューションの幅広いポートフォリオを提供するものです。Microsoft との Oracle のパートナーシップによって、顧客は Oracle ソフトウェアを Microsoft のパブリック クラウドおよびプライベート クラウドで Oracle からの証明とサポートに自信を持って展開することができます。Oracle の取り組みと Oracle のパブリックとプライベート クラウド ソリューションへの投資が変わることはありません。

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に自信を持って展開できることを保証しています。

## サポートされているバージョン##

以下のテーブルは、Azure 上での動作がテストされた各種ディストリビューション、Linux Integration Services (LIS)、および Azure Linux エージェントのバージョンを示しています。LIS ドライバーは [http://www.microsoft.com/ja-jp/download/details.aspx?id=34603](http://www.microsoft.com/ja-jp/download/details.aspx?id=34603) にあります。Linux エージェントのバージョンは [https://github.com/windowsazure/walinuxagent](https://github.com/windowsazure/walinuxagent) にあります。

テーブルには、Azure で最適な動作をするために一部のディストリビューション バージョンで必要になるカーネル互換性パッチのリンクも記載しています。

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>ディストリビューション</th>		
	    <th>バージョン</th>
	    <th>ドライバー</th>
		<th>カーネル互換性パッチ</th>
		<th>エージェント</th>
			</tr>
	<tr>
		<th>  Canonical UBUNTU</th>
		<td> Ubuntu 12.04.1、12.10、および 13.04</td>
		<td>カーネル内</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">12.04 または 12.04.01 のみ必要</a></td>
		<td>パッケージ: walinuxagent のパッケージ リポジトリ内<br />
			ソース: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
			</tr>
	<tr>
		<th> CENTOS by Open Logic</th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/p/?LinkID=254263">LIS ドライバー</a>。CentOS 6.4 + ドライバー: カーネル</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">6.3 のみ必要</a></td>
		<td>パッケージ: walinuxagent の <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic パッケージ リポジトリ</a>内<br />
			ソース: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
 		
	</tr>
	<tr>
		<th> Oracle Linux</th>
		<td> 6.4+</td>
        <td>カーネル内</td>
		<td>該当なし</td>
		<td>パッケージ: リポジトリ内、名前: WALinuxAgent
<br />
			ソース: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr><tr>
		<th> SUSE Linux Enterprise</th>
		<td> SLES 11 SP3+</td>
        <td>カーネル内</td>
		<td>該当なし</td>
		<td>パッケージ: <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A> リポジトリ内、名前: WALinuxAgent<br />
			ソース コード: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
	<tr>
		<th> openSUSE</th>
		<td> OpenSUSE 13.1+</td>
		<td>カーネル内</td>
		<td>該当なし</td>
		<td>パッケージ: <A HREF="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</A> リポジトリ内、名前: WALinuxAgent<br />
			ソース コード: <A HREF="http://go.microsoft.com/fwlink/p/?LinkID=250998">GITHUB</A></td>
		
	</tr>
</table>

