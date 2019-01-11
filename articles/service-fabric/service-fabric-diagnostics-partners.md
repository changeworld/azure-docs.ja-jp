---
title: Azure Service Fabric 監視パートナー | Microsoft Docs
description: パートナーの監視ソリューションを使用して Azure Service Fabric を監視する方法について説明します
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: f7bf5d521f4bcb5672ff1d710a08bed2e0872545
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974405"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric 監視パートナー

この記事では、いくつかのパートナー ソリューションを使って、Service Fabric アプリケーション、クラスター、インフラストラクチャを監視する例を紹介しています。 Microsoft は、以下で紹介する各パートナーと連携して、Service Fabric 向けの統合プランを作成しています。

## <a name="dynatrace"></a>Dynatrace

Microsoft は、Dynatrace との統合を通じて、Service Fabric クラスターを監視するための、すぐに使える機能を数多く提供しています。 ご利用の VMSS インスタンスに Dynatrace OneAgent をインストールすることで、Service Fabric 環境からアプリ レベルまで対象にしたパフォーマンス カウンターとトポロジが得られます。 オンプレミスの監視用途でも、Dynatrace は優れた選択肢となります。 詳しい機能は、[アナウンス](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) ページでご覧いただけます。ご利用のクラスターで Dynatrace を有効にする手順については、[こちら](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/)をご覧ください。 

## <a name="datadog"></a>Datadog

Datadog には、Windows と Linux の両方のインスタンス向けに VMSS 用の拡張機能があります。 Datadog を使用すると、Windows イベント ログを収集することができ、ひいては Service Fabric プラットフォーム イベントを Windows で収集することができます。 診断データを Datadog に送信する方法については、[こちら](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)の手順を参照してください。

## <a name="appdynamics"></a>AppDynamics

Service Fabric と AppDynamics は、アプリケーション レベルで統合されます。 環境変数を更新し、App Dynamics の NuGet を使用することによって、アプリケーション テレメトリを AppDynamics に送信することができます。 .NET Service Fabric アプリケーションを AppDynamics に統合する方法については、こちらの[手順](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric)を参照してください。

## <a name="new-relic"></a>New Relic

Service Fabric アプリケーションとの統合の点で親和性の高い Application Performance Management ツールとしてはもう 1 つ、New Relic があります。 New Relic NuGet パッケージをインストールし、特定の環境変数をマニフェスト ファイルに追加することによって、アプリケーション テレメトリを New Relic に送信することができます。 .NET Service Fabric アプリケーションの New Relic テレメトリを有効にする方法については、こちらの[手順](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric)を参照してください。

## <a name="elk"></a>ELK 

ELK スタックは、各オープンソース テクノロジ (Elasticsearch、Logstash、Kibana) を組み合わせたものです。 これらを組み合わせて使用することで、Service Fabric の監視データと診断データを収集、格納、分析することができます。 これを Service Fabric のネイティブ Java アプリケーションで行うチュートリアルについては、[こちら](service-fabric-tutorial-java-elk.md)を参照してください。 


## <a name="next-steps"></a>次の手順

* Service Fabric における[監視と診断の概要](service-fabric-diagnostics-overview.md)を把握する
* Microsoft のファースト パーティ ツールを使用して[一般的なシナリオを診断](service-fabric-diagnostics-common-scenarios.md)する方法を知る
