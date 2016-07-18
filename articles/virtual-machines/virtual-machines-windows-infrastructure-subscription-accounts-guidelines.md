<properties
	pageTitle="サブスクリプションとアカウントのガイドライン | Microsoft Azure"
	description="Azure サブスクリプションとアカウントに関する主要な設計と実装のガイドラインについて説明します。"
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="iainfou"/>

# サブスクリプションとアカウントのガイドライン

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

この記事は、環境とユーザー ベースの拡大にあわせたサブスクリプションとアカウント管理の方法について説明します。


## サブスクリプションとアカウントに関する実装ガイドライン

決めること:

- IT ワークロードやインフラストラクチャをホストするために必要なサブスクリプションとアカウントのセット
- 階層を組織に合わせてどのように分類するか

タスク:

- サブスクリプション レベルから管理する論理的な組織階層を定義する
- 必要なアカウントと、論理階層に一致した各アカウントに対するサブスクリプションを定義する
- 名前付け規則を使用してサブスクリプションとアカウントのセットを作成します。


## サブスクリプションとアカウント

Azure を使用するには、1 つまたは複数の Azure サブスクリプションが必要です。仮想マシン (VM) や仮想ネットワークなどのリソースは、サブスクリプションに存在します。

- 企業のお客様は、通常、エンタープライズ加入契約を使用します。これは、階層内の最上位のリソースであり、1 つまたは複数のアカウントに関連付けられています。
- エンタープライズ加入契約を持たないコンシューマーおよび顧客の最上位のリソースはアカウントです。
- サブスクリプションはアカウントに関連付けられており、アカウントごとに 1 つまたは複数のサブスクリプションを使用できます。Azure はサブスクリプション レベルで課金情報を記録します。

アカウント/サブスクリプションの関係での 2 つの階層レベルの制限のため、アカウントとサブスクリプションの名前付け規則を課金のニーズに合わせることが重要です。たとえば、世界規模の企業が Azure を使用する場合、リージョンごとに 1 つのアカウントを使用し、リージョン レベルでサブスクリプションを管理できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

たとえば、次のような構造を使用できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

同じ例について、リージョンで特定のグループに複数のサブスクリプションを関連付ける場合は、名前付け規則に追加のアカウントまたはサブスクリプション名をエンコードする方法が組み込まれている必要があります。次の組織では、請求レポートの間にメッセージ請求データで新しいレベルの階層を生成できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

組織は次のようになります。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

マイクロソフトは、単一アカウントまたはエンタープライズ アグリーメントのすべてのアカウントについて、ダウンロード可能なファイルで詳細な課金を提供します。たとえば、Excel を使用するなどしてこのファイルを処理できます。このプロセスは、データを取り込み、階層の複数のレベルをエンコードするリソースを異なる列に分割し、ピボット テーブルまたは PowerPivot を使用して動的なレポート機能を提供します。


## 次のステップ

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->