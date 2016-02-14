<properties 
	pageTitle="エンコード ユニットの追加方法" 
	description=".NET を使用して、エンコード ユニットを追加する方法を説明します。"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako,milangada,gtrifonov" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016"
	ms.author="juliako"/>


#.NET SDK を使用してエンコードを拡張する方法


> [AZURE.SELECTOR]
- [Portal](media-services-portal-encoding-units.md)
- [.NET](media-services-dotnet-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##概要

Media Services アカウントは、エンコード ジョブを処理する速度を決定する予約ユニットの種類に関連付けられます。予約ユニットの種類は、S1、S2、S3 から選択できます。たとえば、同じエンコード ジョブを実行した場合、Standard 予約ユニットの方が Basic 予約ユニットに比べ高速です。詳細については、[Milan Gada](https://azure.microsoft.com/blog/author/milanga/) によるエンコード予約ユニットの種類に関するブログを参照してください。

予約ユニットの種類を指定するだけでなく、エンコード予約ユニットを使用して、アカウントをプロビジョニングすることも指定できます。用意したエンコード予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

.NET SDK を使用して予約ユニットの種類とエンコード予約ユニットの数を変更するには、以下の手順に従います。

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##サポート チケットを開く

既定で、各 Media Services アカウントは最大 25 個のエンコードと 5 個のオンデマンド ストリーミング予約ユニットを設定できます。サポート チケットを開くと、上限の拡大を要求できます。

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
 


##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0204_2016-->