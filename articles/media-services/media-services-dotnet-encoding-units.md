---
title: エンコード ユニットの追加方法
description: .NET を使用して、エンコード ユニットを追加する方法を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: juliako;milangada;gtrifonov

---
# .NET SDK を使用してエンコードを拡張する方法
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST ()](https://msdn.microsoft.com/library/azure/dn859236.aspx)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## Overview
> [!IMPORTANT]
> メディア処理のスケール設定の詳細については、[概要](media-services-scale-media-processing-overview.md)に関するトピックを必ず確認してください。
> 
> 

.NET SDK を使用して予約ユニットの種類とエンコード予約ユニットの数を変更するには、以下の手順に従います。

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## サポート チケットを開く
既定で、各 Media Services アカウントは最大 25 個のエンコードと 5 個のオンデマンド ストリーミング予約ユニットを設定できます。サポート チケットを開くと、上限の拡大を要求できます。

### サポート チケットを開く
サポート チケットを開くには、以下の手順を実行します。

1. [[サポートの要求]](https://manage.windowsazure.com/?getsupport=true) をクリックします。ログインしていない場合は、資格情報を入力するように求められます。
2. サブスクリプションを選択します。
3. サポートの種類として [技術] を選択します。
4. [チケットの作成] をクリックします。
5. 次のページに示される製品一覧で [Azure Media Services] を選択します。
6. 問題に適した「問題の種類」を選択します。
7. [続行] をクリックして続行します。
8. 次のページの指示に従って、問題に関する詳細を入力します。
9. [送信] をクリックして、チケットを開きます。

## Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->