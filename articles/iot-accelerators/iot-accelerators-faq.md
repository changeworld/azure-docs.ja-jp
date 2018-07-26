---
title: Azure IoT ソリューション アクセラレータに関する FAQ | Microsoft Docs
description: IoT ソリューション アクセラレータに関してよく寄せられる質問
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: c5621d5e16e31104ee28cc521386a5c0ca290a8b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187701"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT ソリューション アクセラレータに関してよく寄せられる質問

[接続済みファクトリ固有の FAQ](iot-accelerators-faq-cf.md) および [リモート監視に固有の FAQ](iot-accelerators-faq-rm-v2.md) も参照してください。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>ソリューション アクセラレータのソース コードはどこで入手できますか。

ソース コードは、次の GitHub リポジトリに格納されています。

* [リモート監視ソリューション アクセラレータ (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [リモート監視ソリューション アクセラレータ (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [予測メンテナンスのソリューション アクセラレータ](https://github.com/Azure/azure-iot-predictive-maintenance)
* [接続済みファクトリ ソリューション アクセラレータ](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>ソリューション アクセラレータ用のデバイス クライアントの開発では、どの SDK を使用できますか?

[Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub リポジトリで、異なる言語 (C、.NET、Java、Node.js、Python) IoT デバイス SDK へのリンクを見つけることができます。

DevKit デバイスを使用する場合は、[IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub リポジトリで、リソースとサンプルを見つけることができます。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>3 つのソリューション アクセラレータすべてに対して利用可能な新しいマイクロサービス アーキテクチャはありますか?

現時点では、最も広範なシナリオを対象としているリモートの監視ソリューションのみがマイクロサービス アーキテクチャを使用しています。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>新しい更新プログラムで新しいオープン ソース マイクロサービス ベースのアーキテクチャによって提供される利点はどのようなものですか?

過去 2 年間、クラウド アーキテクチャは大幅に進化してきました。 マイクロサービスは、開発速度を犠牲にすることなく、拡張性と柔軟性を達成する素晴らしいパターンとして登場してきました。 いくつかの Microsoft のサービスは、このアーキテクチャ パターンを内部的に使用し、優れた信頼性とスケーラビリティの成果を出しています。 私たちは、お客様がメリットを得られるように、この学習を実践に移しました。

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>既存のソリューションと同じ地理的リージョンで、新しいソリューション アクセラレータを利用できますか?

はい、新しいリモート監視は同じ地理的リージョンで利用可能です。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Azure Portal 上でのリソース グループの削除と、azureiotsuite.com の ソリューション アクセラレータで削除をクリックすることの違いは何ですか?

* [azureiotsuite.com](https://www.azureiotsolutions.com/) でソリューション アクセラレータを削除すると、ソリューション アクセラレータの作成時にプロビジョニングしたすべてのリソースが削除されます。 リソース グループにリソースを追加してある場合、それらも削除されます。
* [Azure Portal](https://portal.azure.com) でリソース グループを削除した場合は、そのリソース グループ内のリソースのみが削除されます。 さらに、ソリューション アクセラレータに関連付けられている Azure Active Directory アプリケーションを削除する必要があります。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Azure IoT ソリューション アクセラレータで既存の投資の活用を続行できますか?

はい。 現在存在しているすべてのソリューションは、Azure サブスクリプションで動作を続行し、ソース コードは GitHub で利用可能であり続けます。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる IoT Hub インスタンスはいくつですか?

既定では、[サブスクリプションごとに 10 個の IoT Hub](../azure-subscription-service-limits.md#iot-hub-limits) をプロビジョニングできます。 この上限は、[Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成して引き上げることができます。 このため、ソリューション アクセラレータごとに新しい IoT ハブをプロビジョニングするため、特定のサブスクリプションでプロビジョニングできるソリューション アクセラレータの数は最大 10 個になります。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>サブスクリプションには何個の Azure Cosmos DB インスタンスをプロビジョニングできますか?

50 個です。 [Azure サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を作成してこの上限を引き上げることができますが、既定では、サブスクリプションごとにプロビジョニングできる Cosmos DB インスタンスは 50 個のみです。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>サブスクリプションでプロビジョニングできる無料の Bing マップ API の数はいくつですか?

2 個です。 1 つの Azure サブスクリプションでは、内部トランザクション レベル 1 のエンタープライズ向け Bing 地図プランを最大で 2 個作成できます。 リモート監視ソリューションは、既定では内部トランザクション レベル 1 のプランでプロビジョニングされます。 その結果、変更なしでサブスクリプションにプロビジョニングできるリモート監視ソリューションは最大 2 個です。

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Microsoft Azure for DreamSpark があれば、ソリューション アクセラレータを作成できますか?

> [!NOTE]
> Microsoft Azure for DreamSpark は現在、学生向けの Microsoft Imagine として知られています。

現時点では、[Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) アカウントを使用して、ソリューション アクセラレータを作成することはできません。 ただし、[Azure の無料試用版アカウント](https://azure.microsoft.com/free/)を数分で作成して、ソリューション アクセラレータを作成できるようにすることは可能です。

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>クラウド ソリューション プロバイダー (CSP) のサブスクリプションがある場合は、ソリューション アクセラレータを作成できますか?

現時点では、クラウド ソリューション プロバイダー (CSP) のサブスクリプションで、ソリューション アクセラレータを作成することはできません。 ただし、[Azure の無料試用版アカウント](https://azure.microsoft.com/free/)を数分で作成して、ソリューション アクセラレータを作成できるようにすることは可能です。

### <a name="how-do-i-delete-an-aad-tenant"></a>AAD テナントはどうすれば削除できますか?

[Azure AD テナントの削除方法](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)に関する Eric Golpe のブログ記事を参照してください。

### <a name="next-steps"></a>次の手順

IoT ソリューション アクセラレータの他の機能についても学習できます。

* [リモート監視ソリューション アクセラレータの機能について知る](quickstart-remote-monitoring-deploy.md)
* [予測メンテナンスのソリューション アクセラレータの概要](iot-accelerators-predictive-overview.md)
* [接続済みファクトリ ソリューション アクセラレータの展開](quickstart-connected-factory-deploy.md)
* [徹底的な IoT セキュリティ](/azure/iot-fundamentals/iot-security-ground-up)
