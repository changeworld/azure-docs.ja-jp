<properties 
	pageTitle="Azure Virtual Machines に対する計画的なメンテナンス" 
	description="Azure の計画的なメンテナンスの概要と、それが Azure で実行されている仮想マシンに及ぼす影響について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="kenazk"/>


# Azure Virtual Machines に対する計画的なメンテナンス

## Azure が計画的なメンテナンスを実行する理由
<p> Microsoft Azure は、世界各地で定期的に更新を行い、Virtual Machines の基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。これらの更新の多くは、Virtual Machines や Cloud Services に影響を及ぼさずに実行されます。ただし、一部の更新では、インフラストラクチャに必須の更新を適用する際に仮想マシンの再起動が必要になります。その場合、インフラストラクチャにパッチを適用する間、仮想マシンはシャットダウンされ、その後、仮想マシンの再起動が行われます。仮想マシンの可用性に影響を及ぼす可能性があるメンテナンスには、計画的なメンテナンスと計画外のメンテナンスの 2 種類があります。このページでは、Microsoft Azure による計画的なメンテナンスの実行について説明します。計画外のメンテナンスの詳細については、「[Understand planned versus unplanned maintenance (計画的なメンテナンスと計画外のメンテナンスの概要)]」を参照してください。

## 仮想マシンの構成
仮想マシンの構成には、マルチインスタンスと単一インスタンスの 2 種類があります。マルチインスタンスの仮想マシンは、1 つの可用性セットに同一の仮想マシンを複数配置することで構成します。マルチインスタンス構成では冗長性が得られます。アプリケーションの可用性を確保するために、この構成の使用をお勧めします。可用性セットには、いずれもほぼ同一の仮想マシンを配置し、それぞれのアプリケーションに対する目的も同じにする必要があります。高可用性を達成するための仮想マシン構成の詳細については、「<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">仮想マシンの可用性管理</a>」を参照してください。

対照的に、単一インスタンスの仮想マシンは、可用性セットに配置されていないスタンドアロンの仮想マシンです。単一インスタンスの仮想マシンは、それ自体では、複数の仮想マシンが同一の可用性セットにデプロイされていることを必要とするサービス レベル アグリーメント (SLA) の対象になりません。SLA の詳細については、「[サービス レベル アグリーメント](http://azure.microsoft.com/support/legal/sla/)」の「Cloud Services、Virtual Machines、Virtual Network」のセクションを参照してください。


## マルチインスタンスの更新
計画的なメンテナンス時に、Azure プラットフォームは最初にホスト コンピューターのセットを更新します。これらのコンピューターは単一のマルチインスタンス構成に含まれる複数の仮想マシンをホストしているため、更新により、それらの仮想マシンの再起動が行われます。マルチインスタンス構成内の仮想マシンは、それぞれがセット内の他のマシンと同様の機能を提供していると仮定し、更新プロセス全体にわたって可用性が維持されるように更新されます。基盤を成す Azure プラットフォームにより、可用性セット内の各仮想マシンに更新ドメイン (UD) と障害ドメイン (FD) が割り当てられます。各 UD は同じ時間帯に再起動される仮想マシンのグループです。また、各 FD は共通の電源とネットワーク スイッチを使用する仮想マシンのグループです。

UD および FD の詳細については、「<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">冗長性実現のために複数の仮想マシンを可用性セット内に構成する</a>」を参照してください。

Microsoft Azure は、どのような計画的メンテナンス イベントでも、2 つの異なる UD に属する仮想マシンが同時にオフラインにならないことを保証します。メンテナンスは、それぞれの仮想マシンをシャットダウンし、ホスト コンピューターに更新を適用した後、仮想マシンを再起動して、次の UD に移るという流れで進行します。すべての UD が更新されると、計画的メンテナンス イベントは終了します。計画的なメンテナンス中に UD が順番に再起動されるとは限りませんが、一度に 1 つの UD のみが再起動されます。現在、マルチインスタンス構成内の仮想マシンに対して、計画的なメンテナンスの事前通知は一切行われません。

仮想マシンが再起動すると、Windows イベント ビューアーに次のような情報が表示されます。

<!--Image reference-->
![][image2]

## 単一インスタンスの更新
マルチインスタンスの更新完了後に、Azure は単一インスタンスの仮想マシンをホストしているホスト コンピューターのセットに対する更新を実行します。この更新によって、可用性セットで実行していない仮想マシンが再起動されることもあります。仮に可用性セットで 1 つのインスタンスしか実行していなくても、Azure プラットフォームはそのインスタンスをマルチインスタンスとして扱います。単一インスタンス構成の仮想マシンは、仮想マシンをシャットダウンし、ホスト コンピューターに更新を適用した後、仮想マシンを再起動することで更新されます。こうした更新は、1 地域の 1 つのメンテナンス期間に含まれるすべての仮想マシンに対して実行されます。この計画的メンテナンス イベントは、この種の仮想マシン構成を使用するアプリケーションの可用性に影響を及ぼします。
 
### 電子メール通知
Azure は単一インスタンス構成の仮想マシンに対してのみ、計画的なメンテナンスの実施日が近づいていることを遅くとも予定の 1 週間前に電子メールで通知します。この電子メールはサブスクリプションで指定されたプライマリ電子メール アカウントに送信されます。次の図は、送信される電子メールの例を示しています。

<!--Image reference-->
![][image1]

## リージョンのペア
Azure では、一連のリージョンのペアがまとめられており、ペアのうち一方のリージョンのみで計画的なメンテナンスが行われることが保証されています。Azure の計画的なメンテナンス中は、ペアになったリージョンに対する更新が同時にロールアウトされることはありません。現在のリージョンのペアに関する情報については、次の表を参照してください。

リージョン 1 | リージョン 2
:----- | ------:
米国中北部 | 米国中南部
米国東部 | 米国西部
米国東部 2 | 米国中央部
北ヨーロッパ | 西ヨーロッパ
東南アジア | 東アジア
中国東部 | 中国北部
東日本 | 西日本
ブラジル南部 | 米国中南部
オーストラリア南東部 | オーストラリア東部

たとえば、計画的なメンテナンスのロールアウト中、Azure では、米国東部がメンテナンス中の場合、米国西部に対して同時に更新がロールアウトされることはありません。ただし、北ヨーロッパなどのその他のリージョンは、米国東部と同時にメンテナンスされる可能性があります。

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance (計画的なメンテナンスと計画外のメンテナンスの概要)]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=58--> 