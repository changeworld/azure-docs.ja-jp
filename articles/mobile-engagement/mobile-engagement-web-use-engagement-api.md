<properties
	pageTitle="Azure Mobile Engagement Web SDK API | Microsoft Azure"
	description="Web SDK for Azure Mobile Engagement の最新の情報と手順"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />

# Web アプリケーションから Engagement API を使用する方法

このドキュメントは、ドキュメント「[Web アプリケーションに Engagement を統合する方法](mobile-engagement-web-integrate-engagement.md)」の追加ドキュメントです。エンゲージメント API を使用してアプリケーションの統計情報を報告する方法についての詳細を提供しています。

Engagement API は、`engagement.agent` オブジェクトによって提供されます。`engagement` は Engagement SDK の既定のエイリアスであり、SDK の構成から再定義することができます。

## エンゲージメントの概念

次のパートは、Web プラットフォームの一般的な [Mobile Engagement の概念](mobile-engagement-concepts.md)を改善するものです。

### `Session` と `Activity`

2 つの*アクティビティ*間で数秒の休止が発生する場合、*アクティビティ*のシーケンスは 2 つの別個の*セッション*に分割されます。この数秒間のことを "*セッション タイムアウト*" と呼びます。

ユーザー アクティビティの終了が、Web アプリケーション側で (`engagement.agent.endActivity` 関数の呼び出しによって) 宣言されていない場合は、アプリケーション ページを閉じてから 3 分以内に、ユーザー セッションの有効期限が Engagement サーバーによって自動的に終了されます。この動作をサーバーの*セッション タイムアウト*といいます。

### `Crash`

キャッチされなかった JavaScript の例外を自動的にレポートする機構はありません。ただし、`sendCrash` 関数を使用してクラッシュを手動でレポートすることはできます (以下参照)。

## アクティビティを報告する

### ユーザーが新しいアクティビティを開始する

	engagement.agent.startActivity("MyUserActivity");

ユーザー アクティビティが変更されるたびに `startActivity()` を呼び出す必要があります。この関数の最初の呼び出しで、新しいユーザー セッションが開始します。

### ユーザーが現在のアクティビティを終了する

	engagement.agent.endActivity();

ユーザーが最後のアクティビティを終了する際には、`endActivity()` を少なくとも 1 回呼び出す必要があります。これにより、ユーザーが現在休止状態にあり、セッション タイムアウトの期限が終了したタイミングでユーザー セッションを閉じる必要があることを Engagement SDK に通知します (セッション タイムアウトの期限が終了する前に `startActivity()` を呼び出すと、そのセッションが再開されます)。

ユーザー アクティビティの終了を Web 環境内でキャッチすることは通常、困難であるか不可能です (ナビゲーター ウィンドウを閉じたときの呼び出しを確実に行う手段がありません)。そのため、アプリケーション ページを閉じてから 3 分以内に、Engagement サーバーによってユーザー セッションの有効期限が自動的に終了されます。

## イベントを報告する

### セッション イベント

通常、セッション イベントは、セッション中にユーザーによって実行されるアクションの報告に使用されます。

**余分なデータがない例:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**余分なデータがある例:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### スタンドアロン イベント

セッション イベントとは逆に、スタンドアロン イベントはセッション外でも発生する場合があります。

そちらについては、``engagement.agent.sendSessionEvent`` ではなく ``engagement.agent.sendEvent`` を使用してください。

## エラーの報告

### セッション エラー

通常、セッション エラーは、セッション中にユーザーに影響するエラーの報告に使用されます。

**余分なデータがない例:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**余分なデータがある例:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### スタンドアロン エラー

セッション エラーとは反対に、スタンドアロン エラーはセッションのコンテキストの外で発生します。

そちらについては、`engagement.agent.sendSessionError` ではなく `engagement.agent.sendError` を使用してください。

## ジョブを報告する

### 例

Ajax 要求を監視する例を次に示します。
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### ジョブの実行中に発生したエラーの報告

エラーは、現在のユーザー セッションに関連付ける代わりに、実行中のジョブに関連付けることができます。

**例:**

Ajax 要求が失敗した場合のエラー報告の例を次に示します。

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### ジョブ中のイベントに関するレポートを作成する

現在のユーザー セッションではなく、実行中のジョブに関連付けることができます。それを可能にするのが `engagement.agent.sendJobEvent` 関数です。

この関数は、`engagement.agent.sendJobError` とまったく同じように動作します。

### クラッシュを報告する

クラッシュを手動で報告するには、`sendCrash` 関数を使用します。

`crashid` 引数には、クラッシュの種類を識別するための文字列を指定します。一方、`crash` 引数には通常、クラッシュのスタック トレースを文字列として指定します。

	engagement.agent.sendCrash(crashid, crash);

## 追加のパラメーター

任意のデータをイベント、エラー、アクティビティ、ジョブにアタッチできます。

このデータには、任意の JSON オブジェクトを使用できます (配列やプリミティブ型は不可)。

**例**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります。

	^[a-zA-Z][a-zA-Z_0-9]*

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### 値

値は、string、number、boolean のいずれかの型に限られます。

#### サイズ

追加は、1 回の呼び出しにつき **1024** 文字に制限されます (SDK によって JSON でエンコードされた場合)。

## アプリケーションの情報を報告する

`sendAppInfo()` 関数を使用して、追跡情報 (または他のアプリケーション固有の情報) を手動で報告できます。

これらの情報は段階的に送信される可能性があることにご注意ください。特定のキーの最新の値のみが特定のデバイスに保持されます。

イベントの追加と同様に、アプリケーション情報の抽出には JSON オブジェクトが使用され、配列やサブオブジェクトは (JSON シリアル化を使用して) フラットな文字列として扱われるのでご注意ください。

### 例

ユーザーの性別や誕生日を送信するサンプル コードはこちらです。

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### 制限

#### 構成する

オブジェクト内の各キーは、次の正規表現と一致する必要があります。

	^[a-zA-Z][a-zA-Z_0-9]*

キーは、文字、数字、アンダー スコア (\_) が後に続く、少なくとも 1 つの文字で始まる必要があることを意味します。

#### サイズ

アプリケーション情報は、1 回の呼び出しにつき **1024** 文字に制限されます (SDK により JSON でエンコードされた場合)。

前の例では、サーバーに送信される JSON は 44 文字です。

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->