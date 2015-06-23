<properties 
	pageTitle="エンコード占有ユニットの規模の設定方法" 
	description="アカウントにプロビジョニングする [オンデマンド ストリーミング占有ユニット] と [エンコード占有ユニット] の数を指定することでMedia Servicesの規模を設定する方法について説明します。" 
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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# エンコードの規模の設定方法

この記事は、「[Media Services ビデオ オン デマンド ワークフロー](media-services-video-on-demand-workflow.md)」シリーズの一部です。

## 概要

Media Services アカウントは、エンコード ジョブを処理する速度を決定する占有ユニットの種類に関連付けられます。占有ユニットの種類には、**Basic**、**Standard**、**Premium** を選択できます。たとえば、同じエンコード ジョブを実行した場合、**Standard** 占有ユニットの方が **Basic** 占有ユニットに比べ高速です。詳細については、「[Encoding Reserved Unit Types (エンコード占有ユニットの種類)](http://azure.microsoft.com/blog/author/milanga)」をご覧ください。

占有ユニットの種類を指定するだけでなく、エンコード占有ユニットを使用して、アカウントをプロビジョニングすることも指定できます。用意したエンコード占有ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。たとえば、アカウントの占有ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに占有ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

占有ユニットの種類とエンコード占有ユニットの数を変更するには、以下の手順に従います。

1. [管理ポータル](https://manage.windowsazure.com/)で、**[Media Services]** をクリックします。次に、Media Services の名前をクリックします。

2. **[エンコード]** ページを選択します。 

	**[占有ユニットの種類]** を変更するには、[BASIC]、[STANDARD]、[PREMIUM] のいずかを押します。 

	選択した占有ユニットの種類の占有ユニット数を変更するには、**エンコード** スライダーを使用します。 
	
	
	![[プロセッサ] ページ](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

	  
	>[Azure.Note] シンガポール、香港、大阪、北京、上海のデータ センターでは、Premium 占有ユニットが提供されません。

3. [保存] ボタンを押して、変更を保存します。

	新しいエンコード占有ユニットは、[保存] をクリックするとすぐに割り当てられます。

	>[Azure.Note] コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。

## クォータと制限

クォータと制限の詳細、サポート チケットを開く方法については、「[クォータと制限](media-services-quotas-and-limitations.md)」をご覧ください。






<!--HONumber=52--> 