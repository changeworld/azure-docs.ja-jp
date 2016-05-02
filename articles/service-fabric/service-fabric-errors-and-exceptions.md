<properties
   pageTitle="一般的にスローされる FabricClient 例外 | Microsoft Azure"
   description="FabricClient API でアプリケーションやクラスターの管理操作を実行しているときにスローされる可能性のある一般的な例外とエラーについて説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2016"
   ms.author="ryanwi"/>

# FabricClient API の操作時に発生する一般的な例外とエラー
Service Fabric のアプリケーションやサービス、クラスターに対する管理タスクは、クラスター アドミニストレーターやアプリケーション管理者が [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) API を使って実行できます。管理タスクの例としては、アプリケーションのデプロイ、アップグレード、削除、クラスターの正常性のチェック、サービスのテストといった作業が挙げられます。アプリケーション開発者やクラスター アドミニストレーターは、FabricClient API を使用して、Service Fabric クラスターや Service Fabric アプリケーションを管理するためのツールを開発できます。

FabricClient を使用して実行できる操作の種類は多岐にわたります。FabricClient のメソッドは、誤った入力やランタイム エラー、一過性のインフラストラクチャの問題などに起因するエラーによって例外をスローします。特定のメソッドでどのような例外がスローされるかについては、API リファレンス ドキュメントを参照してください。ただし、数ある [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) API に共通してスローされる例外もいくつかあります。以下の表は、各種の FabricClient API に共通する例外を一覧にしたものです。

|例外| スローされるタイミング|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) オブジェクトがクローズ済みであるとき。使用中の [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) オブジェクトを破棄したうえで、新しい [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) オブジェクトをインスタンス化してください。 |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|操作がタイムアウトしたとき。操作の完了前に MaxOperationTimeout に達すると、[OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) が返されます。|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/ja-JP/library/system.unauthorizedaccessexception.aspx)|操作のアクセス チェックに失敗したとき。E\_ACCESSDENIED が返されます。|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|操作の実行中にランタイム エラーが発生したとき。FabricClient のいずれのメソッドも、[FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx) をスローする可能性があります。例外の正確な原因は [ErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx) プロパティによって示されます。エラー コードは、[FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) 列挙体として定義されています。|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|なんらかの一過性のエラー状態が原因で操作に失敗したとき。たとえばレプリカのクォーラムが一時的に到達不能状態に陥ったことが原因で操作に失敗する可能性があります。一過性の例外によって失敗した操作は、再試行することができます。|

[FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx) で返される可能性のある一般的な [FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) エラーを次に示します。

|エラー| 条件|
|---------|:-----------|
|CommunicationError|操作の失敗の原因は通信エラーです。操作をやり直してください。|
|InvalidCredentialType|資格情報の種類が無効です。|
|InvalidX509FindType|X509FindType が無効です。|
|InvalidX509StoreLocation|X509 ストアの場所が無効です。|
|InvalidX509StoreName|X509 ストアの名前が無効です。|
|InvalidX509Thumbprint|X509 証明書の拇印の文字列が無効です。|
|InvalidProtectionLevel|保護レベルが無効です。|
|InvalidX509Store|X509 証明書ストアを開くことができません。|
|InvalidSubjectName|サブジェクト名が無効です。|
|InvalidAllowedCommonNameList|共通名リスト文字列の形式が無効です。コンマ区切りで指定する必要があります。|

<!---HONumber=AcomDC_0420_2016-->