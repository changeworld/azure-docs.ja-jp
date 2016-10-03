<properties
   pageTitle="Linux 上の Azure Service Fabric | Microsoft Azure"
   description="Service Fabric クラスターは Linux と Java をサポートするので、Java で記述された Service Fabric アプリケーションを Linux にデプロイし、ホストすることができます。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Linux 上の Service Fabric

[このブログ投稿](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/)で発表したように、プレビューは 9 月 26 日に一般公開されます。Linux 上の Service Fabric を使用すると、Windows 上と同じように、Linux で可用性とスケーラビリティの高いアプリケーションを構築、デプロイ、および管理できます。さらに、高度な Service Fabric フレームワーク (Reliable Services と Reliable Actors) を、Linux 上の Java で使用できるようになりました。

> [AZURE.VIDEO service-fabric-linux-preview]

## サポートされるオペレーティング システムとプログラミング言語

制限付きプレビューは、Ubuntu Server 16.04 が実行されている Azure でマルチマシン クラスターのほか、ワンボックス開発クラスターとの作成をサポートします。

任意の言語またはフレームワークで[ゲスト実行可能サービス](service-fabric-deploy-existing-app.md)を構築できます。Java または C# を使用して、Docker コンテナーの調整のほか、Reliable Services フレームワークや Reliable Actors フレームワークに基づくサービスを構築することもできます。

>[AZURE.NOTE] Reliable Collection は Linux ではまだサポートされていません。

Linux 上の Service Fabric は、概念的には Windows 上の Service Fabric と同じです (OS の詳細およびプログラミング言語のサポートを除く)。したがって、[既存のドキュメント](http://aka.ms/servicefabricdocs)のほとんどがこちらにも適用されるため、テクノロジを理解するうえで役立ちます。

## 次のステップ

[Reliable Actors](service-fabric-reliable-actors-introduction.md) および [Reliable Services](service-fabric-reliable-services-introduction.md) プログラミング フレームワークを理解します。

<!---HONumber=AcomDC_0921_2016-->