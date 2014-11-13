<properties urlDisplayName="Endorsed distributions" pageTitle="Azure での動作保証済み Linux ディストリビューション" metaKeywords="" description="Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、OpenLogic、および SUSE に関する指針も含めて、説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure での動作保証済み Linux ディストリビューション" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Azure での動作保証済み Linux ディストリビューション

Azure ギャラリーにあるディストリビューション イメージは、以下のパートナー様から提供されており、マイクロソフトはさまざまな Linux コミュニティと協力して動作保証済みディストリビューションの数を増やそうと努めています。ギャラリーから利用できないディストリビューションの場合も、[このページ][このページ]の指針に従うことにより、いつでも独自の Linux を展開することができます。

## Canonical

[][]<http://www.ubuntu.com/cloud/azure></a>

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。Canonical は、Ubuntu に関して、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイスを備え、携帯電話からクラウドまで統一されたフリー プラットフォームにするというビジョンを持っているため、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまで多様な企業が最初に選択するディストリビューションとなり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC からサーバーや携帯機器まで、さまざまな Ubuntu ソリューションを市場に送り出しています。

## OpenLogic

[][1]<http://www.openlogic.com/azure></a>

OpenLogic は、クラウドおよびデータ センター向けの企業オープン ソース ソリューションの一流プロバイダーです。OpenLogic は、多種多様な業界の一流企業数百社に対して、オープン ソース ソフトウェアを安全に入手し、サポートし、コントロールできるよう支援しています。OpenLogic は、600 種類のオープン ソース パッケージについて、OpenLogic Expert Community に支えられた商用品質のテクニカル サポートと保証を提供しています。たとえば、CentOS を企業レベルでサポートし、Azure に CentOS ベースのイメージを提供する開設時パートナー様でもあります。

## Oracle

[][2]<http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html></a>

Oracle の戦略は、パブリック クラウドとプライベート クラウドに対して広範なソリューションを用意し、顧客が Oracle のクラウドや他社のクラウドにおいて Oracle のソフトウェアを柔軟にデプロイできるようにすることです。マイクロソフトと Oracle との提携により、お客様はマイクロソフトのパブリック クラウドおよびプライベート クラウドにおいて Oracle の保証とサポートの下で安心して Oracle のソフトウェアをデプロイできます。Oracle のパブリックおよびプライベート クラウド ソリューションに対する取り組みと投資が変わることはありません。

## SUSE

[][3]<http://www.suse.com/suse-linux-enterprise-server-on-azure></a>

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に安心して自信を持って展開できることを保証しています。

## サポートされているバージョン

次の表は、Azure 上での動作がテストされた各種ディストリビューションのバージョン、Linux Integration Services (LIS) ドライバー、および Azure Linux エージェントのバージョンを示しています。LIS ドライバーは、ディストリビューションのカーネルに既定で組み込まれているか、[ここ][ここ]で入手できます。Linux エージェントのバージョンは、ディストリビューションのパッケージのリポジトリか、[Github][Github] で入手できます。

この表には、Azure で最適な動作をするために一部のディストリビューション/カーネルのバージョンで必要な [Linux カーネル互換性パッチ][Linux カーネル互換性パッチ]のリンクも記載されています。

<table border="1" width="600">
<tr bgcolor="#E9E7E7">
<th>
ディストリビューション

</th>
<th>
バージョン

</th>
<th>
ドライバー

</th>
<th>
カーネル互換性パッチ

</th>
<th>
エージェント

</th>
</tr>
<tr>
<th>
Canonical Ubuntu

</th>
<td>
Ubuntu 12.04.1+、14.04、14.10

</td>
<td>
カーネル内

</td>
<td>
[12.04 または 12.04.01 のみ必要][12.04 または 12.04.01 のみ必要]

</td>
<td>
パッケージ: walinuxagent のパッケージ リポジトリ内
 ソース: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
CentOS by OpenLogic

</th>
<td>
CentOS 6.3+

</td>
<td>
CentOS 6.3: [LIS ドライバー][ここ]; CentOS 6.4+ drivers: カーネル内

</td>
<td>
[6.3 のみ必要][6.3 のみ必要]

</td>
<td>
パッケージ: walinuxagent の [Open Logic パッケージ リポジトリ][Open Logic パッケージ リポジトリ]内
 ソース: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
CoreOS

</th>
<td>
475.1.0 *Alpha*<sup>1</sup>

</td>
<td>
カーネル内

</td>
<td>
該当なし

</td>
<td>
ソース: [GitHub][4]

</td>
</tr>
<tr>
<th>
Oracle Linux

</th>
<td>
6.4+

</td>
<td>
カーネル内

</td>
<td>
該当なし

</td>
<td>
パッケージ: リポジトリ内、名前: WALinuxAgent
 ソース: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
SUSE Linux Enterprise

</th>
<td>
SLES 11 SP3+

</td>
<td>
カーネル内

</td>
<td>
該当なし

</td>
<td>
パッケージ: [Cloud:Tools][Cloud:Tools] リポジトリ内、名前: WALinuxAgent
 ソース コード: [GitHub][GitHub]

</td>
</tr>
<tr>
<th>
openSUSE

</th>
<td>
openSUSE 13.1+

</td>
<td>
カーネル内

</td>
<td>
該当なし

</td>
<td>
パッケージ: [Cloud:Tools][Cloud:Tools] リポジトリ内、名前: WALinuxAgent
 ソース コード: [GitHub][GitHub]

</td>
</tr>
</table>
<sup>1</sup> **メモ:** Azure 上の CoreOS は、現在開発者プレビュー (*alpha*) の段階です。

  [このページ]: ../virtual-machines-linux-create-upload-vhd/
  []: http://www.ubuntu.com/cloud/azure
  [1]: http://www.openlogic.com/azure
  [2]: http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html
  [3]: http://www.suse.com/suse-linux-enterprise-server-on-azure
  [ここ]: http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409
  [Github]: https://github.com/azure/walinuxagent
  [Linux カーネル互換性パッチ]: http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409
  [12.04 または 12.04.01 のみ必要]: http://go.microsoft.com/fwlink/?LinkID=275152&clcid=0x409
  [GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=250998
  [6.3 のみ必要]: http://go.microsoft.com/fwlink/?LinkID=275153&clcid=0x409
  [Open Logic パッケージ リポジトリ]: http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/
  [4]: https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent
  [Cloud:Tools]: https://build.opensuse.org/project/show/Cloud:Tools
