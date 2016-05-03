<properties
	pageTitle="ロジック アプリをスキーマ バージョン 2015-08-01-preview に移行する方法 | Microsoft Azure App Service"
	description="ロジック アプリは、簡単な手順で最新のスキーマ バージョンに移行できます。必要な作業は、これらの手順に従うだけです。"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="deonhe"/>

# ロジック アプリをスキーマ バージョン 2015-08-01-preview に移行する方法

既存のロジック アプリを新しいスキーマに移行するには、次の手順に従います。
1. Azure ポータルでロジック アプリを開きます。  
2. [スキーマの更新] をクリックします。

 ![API アイコン][step1] [スキーマの更新] ページが開き、新しいスキーマの改良点について詳しく書かれたドキュメントへのリンクが表示されます。 ![API アイコン][step2]

>[AZURE.NOTE] **[スキーマの更新]** を選択すると、自動的に移行ステップが実行され、必要な出力コードが提供されます。このコードを使って既存の定義を更新できます。ただし、以下の「**ベスト プラクティス**」セクションで取り上げる適切なコーディング手法に確実に従ってください。

## ロジック アプリを最新のスキーマ バージョンに移行するうえでのベスト プラクティス  

- 移行したスクリプトを新しいロジック アプリにコピーします。テストが完了して、移行後のアプリが正しく動作することを確認するまでは、以前のロジック アプリを上書きしないでください。
- 運用環境に展開する**前に**ロジック アプリをテストします。
- 移行が完了したら、できるだけ[マネージ API](./apis-list.md) を使用するようにロジック アプリを更新します。たとえば、DropBox v1 が使われている箇所はすべて Dropbox v2 を使用するように変更します。


## 参照トピック
-  [ロジック アプリを手動で移行する方法について](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!---HONumber=AcomDC_0427_2016-->