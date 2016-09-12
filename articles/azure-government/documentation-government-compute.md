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


#  Azure 政府機関向けコンピューティング

##  Virtual Machines


以下の情報は、Azure Virtual Machines に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VM 内で入力、保存、処理されるデータには、輸出規制対象データが含まれていてもかまいません。Azure Virtual Machines 内で実行されるバイナリ。静的な認証コード (Azure のプラットフォーム コンポーネントにアクセスするためのパスワード、スマートカードの PIN など)。Azure のプラットフォーム コンポーネントを管理する際に使用する証明書の秘密キー。SQL 接続文字列。その他、Azure サービスに格納される各種セキュリティ情報/シークレット (証明書、暗号化キー、マスター キー、ストレージ キーなど)。 | メタデータに、輸出規制対象データを含めることは許可されません。このメタデータには、Azure Virtual Machines を作成したり管理したりする際に入力するあらゆる構成データが含まれます。テナント ロール名、リソース グループ、デプロイ名、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。  

その他の情報については、<a href=https://azure.microsoft.com/documentation/services/virtual-machines/windows/> Azure Virtual Machines のパブリック ドキュメント</a>を参照してください。

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。

<!---HONumber=AcomDC_0831_2016-->