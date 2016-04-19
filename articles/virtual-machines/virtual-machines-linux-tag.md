<properties
   pageTitle="Linux 仮想マシンにタグを付ける方法 | Microsoft Azure"
   description="リソース マネージャーのデプロイメント モデルを使用して Azure で作成した Linux 仮想マシンのタグ付けについて説明します。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="iainfour;memccror"/>

# Azure で Linux 仮想マシンにタグを付ける方法

この記事では、Azure Resource Manager で Azure で Linux 仮想マシンにタグを付けるさまざまな方法について説明します。タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。現在、Azure では、1 つのリソースまたはリソース グループにつき最大 15 個のタグを付けることができます。タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。タグは、Azure リソース マネージャー経由で作成されたリソースでのみサポートされます。

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Azure CLI を使用してタグを付ける

Azure CLI で既に作成されているリソースにも、タグを付けることができます。この場合は、[Azure CLI 環境][]を設定します。Azure CLI でサブスクリプションにログインし、ARM モードに切り替えます (`azure config mode arm`)。

このコマンドを使用すると、タグを含め、指定した仮想マシンのすべてのプロパティを表示できます。

        azure vm show -g MyResourceGroup -n MyVM

Azure CLI を使用して新しい VM タグを追加するには、タグ パラメーター **-t** を指定して `azure vm set` コマンドを実行できます。

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

すべてのタグを削除するには、`azure vm set` コマンドで **-T** パラメーターを使用できます。

        azure vm set – g MyResourceGroup –n MyVM -T


ここでは、Azure CLI およびポータルを使用してリソースにタグを適用しました。次は、課金ポータルの使用量の詳細でタグを確認してみましょう。




## 次のステップ

* Azure リソースへのタグ付けの詳細については、「[Azure Resource Manager の概要][]」および「[タグを使用した Azure リソースの整理][]」を参照してください。
* タグが Azure リソースの使用の管理にどのように役立つかを確認するには、「[Microsoft Azure の課金内容の確認][]」および「[Microsoft Azure リソースの消費を把握する][]」を参照してください。





[Azure CLI 環境]: ./xplat-cli-azure-resource-manager.md
[Azure Resource Manager の概要]: ../resource-group-overview.md
[タグを使用した Azure リソースの整理]: ../resource-group-using-tags.md
[Microsoft Azure の課金内容の確認]: ../billing-understand-your-bill.md
[Microsoft Azure リソースの消費を把握する]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0406_2016-->