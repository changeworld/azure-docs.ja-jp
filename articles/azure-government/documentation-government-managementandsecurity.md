<properties
	pageTitle="Azure Government のドキュメント | Microsoft Azure"
	description="Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>


#  Azure Government の管理とセキュリティ

##  Key Vault

以下の情報は、Azure Key Vault に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Key Vault キーで暗号化されるすべてのデータには、規制対象データが含まれていてもかまいません。 | Azure Key Vault のメタデータに、輸出規制対象データを含めることは許可されません。このメタデータには、Key Vault を作成したり管理したりする際に入力するあらゆる構成データが含まれます。リソース グループ名、Key Vault 名、サブスクリプション名の各フィールドには規制対象データを入力しないでください。 |

Key Vault は、Azure Government で一般提供されています。パブリックと同様、拡張機能は存在しません。Key Vault の利用は、PowerShell と CLI のみとなります。

詳細については、[Azure Key Vault のパブリック ドキュメント](/key-vault-get-started)を参照してください。

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。

<!---HONumber=AcomDC_0831_2016-->