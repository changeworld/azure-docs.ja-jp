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
   ms.date="05/13/2016"
   ms.author="SubramaR"/>

# Linux 上の Service Fabric

現在、Service Fabric は Linux 上で制限付きプレビューとして利用できるため、Windows 上と同様の環境で可用性とスケーラビリティの高いアプリケーションを構築、デプロイ、管理できます。さらに、高度な Service Fabric フレームワーク (Reliable Services と Reliable Actors) を Java で構築できるようになりました。

## サポートされるオペレーティング システムとプログラミング言語

制限付きプレビューは、Ubuntu Server 15.10 が実行されている Azure でのワンボックス開発クラスターとマルチマシン クラスターの作成をサポートします。

任意の言語またはフレームワークで[ゲスト実行可能サービス](service-fabric-deploy-existing-app.md)を構築できます。また、Java を使用して、Reliable Services フレームワークや Reliable Actors フレームワークに基づくサービスを構築することもできます。

>[AZURE.NOTE] Reliable Collection は Java ではまだサポートされていません。

## プレビューへの参加

制限付きプレビュー プログラムの対象となることに関心がある場合は、ご使用のシナリオと要件について Microsoft が理解を深めることができるように、[アンケートにご記入ください](http://aka.ms/sflinuxsurvey)。プレビューは最初は非常に小規模ですが、徐々に拡張される予定です。

Linux 上の Service Fabric は、Windows 上で利用できるものと概念的に同じである (OS の仕様とプログラミング言語のサポートを除く) ことに注意してください。そのため、[既存のドキュメント](http://aka.ms/servicefabricdocs)のほとんどが適用されるので、このテクノロジについて理解するのに役立ちます。

## 次のステップ

[Reliable Actors](service-fabric-reliable-actors-introduction.md) および [Reliable Services](service-fabric-reliable-services-introduction.md) プログラミング フレームワークを理解します。

<!---HONumber=AcomDC_0518_2016-->