---
title: Azure IoT Suite に関する FAQ | Microsoft Docs
description: IoT スイートに関してよく寄せられる質問
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT スイートに関してよく寄せられる質問

[コネクテッド ファクトリ固有の FAQ](iot-suite-faq-cf.md) および[リモート監視に固有の FAQ](iot-suite-faq-rm-v2.md) もご覧ください。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>事前構成済みソリューションのソース コードはどこで入手できますか?

ソース コードは、次の GitHub リポジトリに格納されています。

* [リモート監視の事前構成済みソリューション (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [リモート監視の事前構成済みソリューション (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [予測メンテナンスの構成済みソリューション](https://github.com/Azure/azure-iot-predictive-maintenance)
* [コネクテッド ファクトリの事前構成済みソリューション](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>構成済みのソリューション用のデバイス クライアントの開発では、どの SDK を使用できますか?

[Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub リポジトリで、異なる言語 (C、.NET、Java、Node.js、Python) IoT デバイス SDK へのリンクを見つけることができます。

DevKit デバイスを使用する場合は、[IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub リポジトリで、リソースとサンプルを見つけることができます。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>3 つの事前構成済みソリューションのすべてに対して利用可能な新しいマイクロサービス アーキテクチャがありますか?

現時点では、最も広範なシナリオを対象としているリモートの監視ソリューションのみがマイクロサービス アーキテクチャを使用しています。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>新しい更新プログラムで新しいオープン ソース マイクロサービス ベースのアーキテクチャによって提供される利点はどのようなものですか?

過去 2 年間、クラウド アーキテクチャは大幅に進化してきました。 マイクロサービスは、開発速度を犠牲にすることなく、拡張性と柔軟性を達成する素晴らしいパターンとして登場してきました。 いくつかの Microsoft のサービスは、このアーキテクチャ パターンを内部的に使用し、優れた信頼性とスケーラビリティの成果を出しています。 私たちは、お客様がメリットを得られるように、この学習を実践に移しました。

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>既存のソリューションと同じ地理的リージョンで、新しい事前構成済みのソリューションを利用できますか?

はい、新しいリモート監視は同じ地理的リージョンで利用可能です。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Azure ポータルでのリソース グループの削除と、azureiotsuite.com の構成済みソリューションで削除をクリックすることの違いは何ですか?

* [azureiotsuite.com](https://www.azureiotsuite.com/) で構成済みのソリューションを削除すると、構成済みソリューションの作成時にプロビジョニングしたすべてのリソースが削除されます。 リソース グループにリソースを追加してある場合、それらも削除されます。
* [Azure Portal](https://portal.azure.com) でリソース グループを削除した場合は、そのリソース グループ内のリソースのみが削除されます。 さらに、構成済みソリューションに関連付けられている Azure Active Directory アプリケーションを削除する必要があります。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Azure IoT Suite で 既存の投資の活用を続行できますか?

はい。 現在存在しているすべてのソリューションは、Azure サブスクリプションで動作を続行し、ソース コードは GitHub で利用可能であり続けます。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる IoT Hub インスタンスはいくつですか?

既定では、[サブスクリプションごとに 10 個の IoT Hub](../azure-subscription-service-limits.md#iot-hub-limits) をプロビジョニングできます。 この上限は、[Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成して引き上げることができます。 よって、構成済みソリューションごとに新しい IoT Hub をプロビジョニングするため、特定のサブスクリプションでプロビジョニングできる構成済みソリューションの数は最大 10 個になります。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>サブスクリプションには何個の Azure Cosmos DB インスタンスをプロビジョニングできますか?

50 個です。 [Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成してこの上限を引き上げることができますが、既定では、サブスクリプションごとにプロビジョニングできる Cosmos DB インスタンスは 50 個のみです。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる無料の Bing マップ API の数はいくつですか?

2 個です。 1 つの Azure サブスクリプションでは、内部トランザクション レベル 1 のエンタープライズ向け Bing 地図プランを最大で 2 個作成できます。 リモート監視ソリューションは、既定では内部トランザクション レベル 1 のプランでプロビジョニングされます。 その結果、変更なしでサブスクリプションにプロビジョニングできるリモート監視ソリューションは最大 2 個です。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Microsoft Azure for DreamSpark があれば、構成済みソリューションを作成できますか?

> [!NOTE]
> Microsoft Azure for DreamSpark は現在、学生向けの Microsoft Imagine として知られています。

現時点では、[Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) アカウントを使用して構成済みソリューションを作成することはできません。 ただし、[Azure の無料試用版アカウント](https://azure.microsoft.com/free/)を数分で作成して、構成済みソリューションを作成できるようにすることはできます。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>クラウド ソリューション プロバイダー (CSP) のサブスクリプションがある場合、構成済みのソリューションを作成できますか?

現在、クラウド ソリューション プロバイダー (CSP) のサブスクリプションで構成済みのソリューションを作成することはできません。 ただし、[Azure の無料試用版アカウント](https://azure.microsoft.com/free/)を数分で作成して、構成済みソリューションを作成できるようにすることはできます。

### <a name="how-do-i-delete-an-aad-tenant"></a>AAD テナントはどうすれば削除できますか?

[Azure AD テナントの削除方法](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)に関する Eric Golpe のブログ記事を参照してください。

### <a name="next-steps"></a>次の手順

IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

* [リモート監視の構成済みソリューションについて知る](iot-suite-remote-monitoring-explore.md)
* [予測メンテナンスの構成済みソリューションの概要](iot-suite-predictive-overview.md)
* [コネクテッド ファクトリ事前構成済みソリューションの概要](iot-suite-connected-factory-overview.md)
* [徹底的な IoT セキュリティ](securing-iot-ground-up.md)