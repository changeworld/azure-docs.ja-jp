 <properties
   pageTitle="Azure と Linux |Microsoft Azure"
   description="Linux 仮想マシンでの Azure Compute、Storage、Network サービスについて説明します。"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/06/2016"
   ms.author="rclaus"/>

# Azure と Linux
Microsoft Azure は、分析、仮想マシン、データベース、モバイル、ネットワーク、ストレージ、Web など、多様なパブリック クラウド サービスを一元化した集合体として成長し続け、さまざまなソリューションのホストとして理想的な形態となっています。Microsoft Azure のスケーラブルなコンピューティング プラットフォームによって、必要なときに使用した分だけを支払う料金体系が実現され、オンプレミスのハードウェアに投資する必要がありません。貴社の顧客のニーズに応えるうえで必要なレベルにまでソリューションをいつでもスケールアップまたはスケールアウトできる柔軟性が Azure にはあります。
 
## Azure Virtual Machines
Azure Virtual Machines を使用すると、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。事実上、すべてのワークロードをすべての言語でほぼすべてのオペレーティング システム上 (Windows、Linux、または増え続けるパートナー各社でカスタム作成されたもの) にデプロイできます。たとえ目的に合った仮想マシンが見つからなくても、 ご安心ください。オンプレミスからカスタム イメージを取り込むことができます。
 
## Microsoft Azure における Linux の基本

Microsoft Azure Virtual Machines、Storage、ネットワークを組み合わせることで、Linux コンピューティングへのニーズに応じた "Infrastructure-as-a-Service" をインターネット規模で実現できます。Azure で Linux を使用するにはまず、次の要件を満たす必要があります。

1. 無料試用版アカウント。[こちらから入手](https://azure.microsoft.com/pricing/free-trial/)してください。
2. Linux、Mac、Windows 用の Azure コマンド ライン インターフェイス (Azure CLI)。[こちら](../xplat-cli-install.md)からインストールできます。
3. Linux VM。[こちら](virtual-machines-linux-quick-create-cli.md)を参照して作成してください。
4. Linux と Azure に関する詳細情報。[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) の要件を満たす方法についても確認が必要です。**このドキュメントは、法務文書が苦手な方も必ずお読みください**。

## ロジスティック: リージョン、ディストリビューション、可用性、VM サイズ、クォータ
### 地域
Microsoft Azure のリソースは、世界各国複数の地理的リージョンに分散されます。"リージョン" とは、1 つの地域に存在する複数のデータ センターを表します。2016 年 1 月 1 日時点で米国に 8 つ、ヨーロッパに 2 つ、アジア太平洋地域に 6 つ、中国大陸に 2 つ、インドに 3 つのリージョンが存在します。すべての Azure リージョンを把握する必要がある場合は、既存のリージョンと新たに発表されたリージョンのリストを**[こちら](https://azure.microsoft.com/regions/)**で公開していますのでご覧ください。

### ディストリビューション
Microsoft Azure は、現在普及しているさまざまな Linux ディストリビューションに対応します。Microsoft の複数のパートナーが、それらのディストリビューションを提供、管理しています。Azure Marketplace からは、CentOS、Debian、Red Hat Enterprise、Ubuntu、FreeBSD などのディストリビューションが提供されています。Microsoft はさまざまな Linux コミュニティと積極的に連携し、動作保証済みディストリビューション リストを拡充しています。**[最新のディストリビューションはこちら](virtual-machines-linux-endorsed-distros.md)**で確認できます。必要な Linux ディストリビューションが現時点でギャラリーに存在しない場合は、**[こちらのページ](virtual-machines-linux-create-upload-generic.md)**のガイドラインに従ってカスタム Linux の VM を導入できます。

## 可用性と Microsoft Azure SLA
Microsoft が VM に設けている 99.95% というサービス レベル アグリーメントの要件をカスタム デプロイで満たすためには、複数の VM をデプロイし、可用性セット内でワークロードを実行する必要があります。そうすることで、Microsoft のデータ センターにある複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。**[こちらからオンラインで](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)** Microsoft の SLA 全文をご覧いただけます。

## VM のサイズとクォータ
Azure に VM をデプロイするとき、あらかじめ用意された一連のサイズから、目的のワークロードに適した VM サイズを選択する必要があります。また、サイズは仮想マシンの処理能力、メモリ、記憶容量にも影響します。課金は、VM の実行時間とその割り当てリソースの消費量に基づいて行われます。詳細なサイズ一覧については、「[Azure の Virtual Machines のサイズ](virtual-machines-linux-sizes.md)」の記事を参照してください。

ここでは、Microsoft が提供している一連の VM サイズ (A、D、DS、G、GS) からの選び方について基本的なガイドラインを紹介しています。

* A シリーズの VM は、軽量のワークロードと開発/テストを想定した低価格、入門レベルの VM です。すべてのリージョンで広く利用できます。定番となっているさまざまなリソースをつないで、仮想マシンから使用することが可能です。
* A シリーズ サイズ (A8 ～ A11) は、ハイパフォーマンス コンピューティング クラスター アプリケーションに適した特殊なコンピューティング集中型の構成です。
* D シリーズ VM は、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。
* Dv2 シリーズは D シリーズの最新版で、強力な CPU を特徴としています。Dv2 シリーズの CPU は D シリーズの CPU よりも、およそ 35% 高速です。これは最新世代の 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサに基づいており、Intel Turbo Boost Technology 2.0 を使用することで、最大 3.2 GHz まで実現できます。Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。
* G シリーズ VM は、最も多くのメモリを提供し、Intel Xeon E5 V3 ファミリのプロセッサが搭載されたホスト上で実行されます。

> [AZURE.NOTE] DS シリーズと GS シリーズの VM では、Premium Storage を使用できます。SSD をベースとした高性能かつ低レイテンシのストレージによって、I/O 負荷の高いワークロードに対応します。Premium Storage は特定のリージョンで使用できます。詳細については、「**[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)**」を参照してください。

Azure サブスクリプションにはそれぞれ既定のクォータ制限が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。このクォータ制限は、サポート チケットで制限の引き上げを要求することによって引き上げることができます。クォータ制限の詳細については、**[Azure サブスクリプションとサービスの制限](../azure-subscription-service-limits.md)**に関するページをご覧ください

## 次のステップ

無料試用版アカウント。**[こちらから入手してください。](https://azure.microsoft.com/pricing/free-trial/)** 既にお持ちの場合は、早速試してみましょう。まず **[Azure CLI をインストール](../xplat-cli-install.md)**します。 インストールが完了したら、[Linux VM の作成に進んでください](virtual-machines-linux-quick-create-cli.md)。

<!---HONumber=AcomDC_0914_2016-->