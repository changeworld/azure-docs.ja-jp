<properties 
	pageTitle="Media Services のクォータと制限" 
	description="このトピックでは、Microsoft Azure Media Services に関連付けられているクォータと制限について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"  
	ms.author="juliako"/>


#クォータと制限

このトピックでは、Microsoft Azure Media Services に関連付けられているクォータと制限について説明します。

## クォータと制限

[AZURE.INCLUDE [azure-mediaservices-limits](../../includes/azure-mediaservices-limits.md)]

##その他の制限事項

Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。サービスが「サービスを利用できません」(503) HTTP 状態コードを返すことがある。詳細については、「[Azure Media Services エラー コード](http://msdn.microsoft.com/library/azure/dn168949.aspx)」トピックの 503 エラーの説明をご覧ください。

##<a id="request_higher_limit"></a>新可能なクォータの上限の拡大を要求する方法

###更新可能なクォータ

サポート チケットを開き、次のクォータの制限の範囲を更新を要求できます。

- エンコード ユニット

- ライブ チャネル (停止状態と実行状態)
 
- ストリーミング エンドポイント (停止状態と実行状態)
 
- ストリーミング ユニット

###サポート チケットを開く

サポート チケットを開くには、以下の手順を実行します。

1. [[サポートの要求]](https://manage.windowsazure.com/?getsupport=true) をクリックします。ログインしていない場合は、資格情報を入力するように求められます。

1. サブスクリプションを選択します。
 
1. サポートの種類として [技術] を選択します。
 
1. [チケットの作成] をクリックします。
 
1. 次のページに示される製品一覧で [Azure Media Services] を選択します。
 
1. 問題に適した「問題の種類」を選択します。
 
1. [続行] をクリックして続行します。
 
1. 次のページの指示に従って、問題に関する詳細を入力します。
 
1. [送信] をクリックして、チケットを開きます。
  

<!---HONumber=August15_HO7-->