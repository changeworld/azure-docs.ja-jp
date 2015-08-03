<properties 
	pageTitle="タイル ギャラリーの関連リソースとリンク済みリソース" 
	description="Azure プレビュー ポータルのタイル ギャラリーに表示される関連リソースとリンク済みリソースについて説明します。" 
	services="azure-portal" 
	documentationCenter="" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="adamab"/>

# タイル ギャラリーの関連リソースとリンク済みリソース

タイル ギャラリーを使用すると、特定のリソースのタイルを見つけて、現在のブレードにドラッグできます。タイル ギャラリーを使用して、リソースにまたがる管理ビューを作成できます。特定のリソースでは、関連リソースには、そのリソースと同じリソース グループを共有するすべてのリソースと、相互にリンクする任意のリソースが含まれています。

## Azure リソース マネージャーのリンク済みリソース

リンクは、Azure リソース マネージャーの機能です。同じリソース グループに存在していない場合でも、リソース間のリレーションシップを宣言できます。リンクがリソースのランタイム、課金、ロール ベースのアクセスに影響を与えることはありません。これは、タイル ギャラリーなどのツールが豊富な管理機能を提供できるように、単にリレーションシップを表すために使用できるメカニズムです。このツールは、API へのリンクを使用してリンクを検査し、カスタムのリレーションシップ管理機能も提供できます。

## リソースをリンクする方法

ポータル経由、または Azure PowerShell や Azure CLI からテンプレートをデプロイすることで、リソースを作成すると、リンクが一部の依存リソースで自動的に作成されます。また、[リンク済みリソースの REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx) を使用するか、テンプレートでリレーションシップを宣言することで、プログラムを使用してリソースをリンクすることもできます。リンク済みリソースの使用の詳細については、「[Azure リソース マネージャーでのリソースのリンク](../resource-group-link-resources.md)」をご覧ください。

## 次のステップ

- Azure リソース マネージャーのテンプレートの作成の概要については、「[テンプレートの作成](../resource-group-authoring-templates.md)」をご覧ください。
- リソース間のリンクの作成方法の詳細については、「[Azure リソース マネージャーでのリソースのリンク](../resource-group-link-resources.md)」をご覧ください。
- プレビュー ポータルからリソース グループを使用する方法の詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](resource-group-portal.md)」をご覧ください。

<!---HONumber=July15_HO4-->