<properties 
	pageTitle="エンコード ユニットの追加方法" 
	description=".NET を使用して、エンコード ユニットを追加する方法を説明します。"  
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




#.NET SDK を使用してエンコード ユニットを追加する方法

この記事は、「[Media Services Video on Demand workflow (メディア サービスのビデオ オンデマンド ワークフロー)](../media-services-video-on-demand-workflow)」シリーズの一部です。
  
##概要

メディア サービス アカウントは、エンコード ジョブを処理する速度を決定する占有ユニットの種類に関連付けられます。占有ユニットの種類には、Basic、Standard、Premium を選択できます。たとえば、同じエンコード ジョブを実行した場合、Standard 占有ユニットの方が Basic 占有ユニットに比べ高速です。詳細については、[Milan Gada](http://azure.microsoft.com/blog/author/milanga/)  "エンコード占有ユニットの種類" に関するブログをご覧ください。

占有ユニットの種類を指定するだけでなく、エンコード占有ユニットを使用して、アカウントをプロビジョニングすることも指定できます。用意したエンコード占有ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。たとえば、アカウントの占有ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに占有ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

.NET SDK を使用して占有ユニットの種類とエンコード占有ユニットの数を変更するには、以下の手順に従います。

	IEncodingReservedUnit encodingBasicReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingBasicReservedUnit.ReservedUnitType = ReservedUnitType.Basic;
	encodingBasicReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingBasicReservedUnit.ReservedUnitType);
	
	encodingBasicReservedUnit.CurrentReservedUnits = 2;
	encodingBasicReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingBasicReservedUnit.CurrentReservedUnits);

##サポート チケットを開く

既定で、各メディア サービス アカウントは最大 25 個のエンコードと 5 個のオンデマンド ストリーミング占有ユニットを設定できます。サポート チケットを開くと、上限の拡大を要求できます。

サポート チケットを開くには、以下の手順を実行します。 

1. [管理ポータル](http://manage.windowsazure.com)の Azure アカウントにログインします。
2. [サポート](http://azure.microsoft.com/support/contact/)に移動します。
3. [サポートの要求] をクリックします。
4. サブスクリプションを選択します。
5. サポートの種類として [技術] を選択します。
6. [チケットの作成] をクリックします。
7. 次のページに示される製品一覧で [Azure メディア サービス] を選択します。
8. [問題の種類] として [メディア処理] を選択し、カテゴリとして [占有ユニット] を選択します。
9. [続行] をクリックして続行します。
10. 次のページにある指示に従って、必要なエンコードかオンデマンド ストリーミング占有ユニットの数を入力します。
11. [送信] をクリックして、チケットを開きます。


<!--HONumber=45--> 
