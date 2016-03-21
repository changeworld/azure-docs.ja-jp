<properties
	pageTitle="PowerApps Enterprise またはロジック アプリに Microsoft Translator を追加する | Microsoft Azure"
	description="Microsoft Translator API と REST API パラメーターの概要"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Microsoft Translator API の概要
Microsoft Translator に接続して、テキストの翻訳、言語の検出などを行います。Microsoft Translator API は次のツールから使用できます。

- PowerApps 
- Logic Apps 

Microsoft Translator では、次の操作を実行できます。

- Microsoft Translator から取得したデータに基づいてビジネス フローを構築できます。 
- アクションを使用して、テキストの翻訳、言語の検出などを行うことができます。また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。たとえば、Dropbox で新しいファイルが作成されたときに、Microsoft Translator を使用してファイル内のテキストを別の言語に翻訳することができます。
- PowerApps Enterprise に Microsoft Translator API を追加できます。追加すると、ユーザーはアプリ内で API を使用できるようになります。 

PowerApps Enterprise に API を追加する方法については、「[Microsoft 管理の API または IT 管理の API を登録する](../power-apps/powerapps-register-from-available-apis.md)」をご覧ください。

ロジック アプリに操作を追加する方法については、「[SaaS サービスを接続する新しいロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)」をご覧ください。

## トリガーとアクション
Microsoft Translator には、次のアクションがあります。トリガーはありません。

トリガー | アクション
--- | ---
なし | <ul><li>言語を検出する</li><li>テキストを音声に変換する</li><li>テキストを翻訳する</li><li>言語を取得する</li><li>音声の言語を取得する</li></ul>

すべての API は、JSON および XML 形式のデータに対応します。

## Microsoft Translator への接続を作成する

### PowerApps に構成を追加する
Microsoft Translator を PowerApps Enterprise に追加するときに、Microsoft Translator アプリケーションの **クライアント ID** と**クライアント シークレット**の値を入力します。Translator アプリケーションがない場合は、次の手順で作成できます。

1. [Azure Data Market 開発者ページ][5]に移動し、Microsoft アカウントでサインインします。 

2. **[アプリケーションの登録]** を選択します。

	1. **[クライアント ID]** の値を入力します。
	2. アプリケーションの**名前**を入力します。
	3. **リダイレクト URL** のダミー値を入力します。たとえば、「 *https://contosoredirecturl* 」と入力します。
	4. **説明**を入力します。
	5. **[作成]** を選択します。  

	![アプリケーションの登録][6]

**クライアント ID** と**クライアント シークレット**の値をコピーし、Azure ポータルの Translator 構成に貼り付けます。


## Swagger REST API リファレンス
適用されるバージョン: 1.0。

### 言語を検出する    
指定したテキストのソース言語を検出します。```GET: /Detect```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|query|string|○|query|なし |言語を特定するテキスト|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### テキストを音声に変換する    
指定したテキストを wave 形式のオーディオ ストリームとして音声に変換します。```GET: /Speak```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|query|string|○|query|なし |変換するテキスト|
|言語|string|○|query|なし |音声を生成する言語コード (例: 'ja-JP')|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### テキストを翻訳する    
Microsoft Translator を使用して、指定した言語にテキストを翻訳します。```GET: /Translate```

| 名前| データ型|必須|場所|既定値|説明|
| ---|---|---|---|---|---|
|query|string|○|query|なし |翻訳するテキスト|
|languageTo|string|○|query| なし|ターゲット言語コード (例: "fr")|
|languageFrom|string|×|query|なし |ソース言語。指定しない場合、Microsoft Translator で自動検出が試行されます (例: en)。|
|カテゴリ|string|×|query|全般 |翻訳のカテゴリ (既定値: 'general')|

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 言語を取得する    
Microsoft Translator がサポートするすべての言語を取得します。```GET: /TranslatableLanguages```

この呼び出しには、パラメーターはありません。

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|


### 音声の言語を取得する    
音声合成に使用できる言語を取得します。```GET: /SpeakLanguages```

この呼び出しには、パラメーターはありません。

#### Response
|名前|説明|
|---|---|
|200|OK|
|default|操作に失敗しました。|

## オブジェクト定義

#### Language: Microsoft Translator で翻訳できる言語の言語モデル

| 名前 | データ型 | 必須|
|---|---|---|
|コード|string|×|
|名前|string|×|


## 次のステップ
Microsoft Translator API を PowerApps Enterprise に追加したら、この API をアプリで使用するための[アクセス許可をユーザーに付与](../power-apps/powerapps-manage-api-connection-user-access.md)します。

[ロジック アプリを作成](../app-service-logic/app-service-logic-create-a-logic-app.md)します。


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0302_2016-->

