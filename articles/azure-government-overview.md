---
title: "Azure Government の概要 | Microsoft Docs"
description: "この記事では、Azure Government のクラウド機能の概要と、連邦、州、地方の各統治機関や関連組織に対して適用されるコンプライアンスをサポートする安定した設計やセキュリティの概要について説明します。 "
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: c579ece3-a46c-4cdc-b650-453fd36b211a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1b1ecd41a22a2aafbf02a44d423242fe273a8bcf


---
# <a name="welcome-to-azure-government"></a>Azure Government へようこそ
## <a name="overview"></a>概要
Microsoft Azure Government は、[セキュリティの根本原則、プライバシーとコントロール、コンプライアンス、透明性](http://azure.com/gov)に基づいて構築されたクラウド プラットフォームを実現します。 公的機関は、アーキテクチャに構築されたすべてのシステムとアプリケーションについて、米国政府にとって重要な世界規模のセキュリティや[コンプライアンス サービス](https://azure.microsoft.com/support/trust-center/compliance/)を採用する、Microsoft Azure の物理的に分離されたインスタンスを受け取ります。 これらのサービスには、FedRAMP および DoD コンプライアンス認証、CJIS の州レベルの契約、HIPAA 関係機関の契約を発行する機能、IRS 1075 のサポートなどがあります。 選別された米国国民が運用する Azure Government では、オンプレミスまたはクラウドにソリューションを構築およびデプロイするための複数のハイブリッド シナリオがサポートされています。 また、公的機関は迅速なスケール変更や、超大規模なクラウド サービスの保証されたアップタイムを活用できます。

Azure Government には、Infrastructure-as-a-Service (IaaS) や Platform-as-a-Service (PaaS)、Software-as-a-Service (SaaS) の主要なコンポーネントが含まれています。  主要コンポーネントには、インフラストラクチャ、ネットワーク、ストレージ、データ管理、ID 管理、その他多くのサービスが含まれます。

Azure Government は、geo 同期データ複製や自動スケーリングなど、Azure で採用されているものと同じほとんどの機能をサポートしています。 

* 一般公開されている最新のサービスについては、[リージョンに関するページ](https://azure.microsoft.com/regions/#services)を参照してください。
* [サインアップして試用](https://azuregov.microsoft.com/trial/azuregovtrial)

クラウド サービスに関心を示す米国政府機関によって、Azure Government がその発展していくニーズにあわせて非常に大きな規模に対応し、厳格なセキュリティを持っていることが認識されています。

## <a name="azure-government-documentation"></a>Azure Government のドキュメント
このサイトでは、 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) サービスの機能について説明すると共に、すべてのお客様に該当する一般的な指針を提供しています。 明示的に規制されたデータを Azure Government サブスクリプションで扱う場合は、あらかじめ、Azure Government の機能を十分に理解し、不明点があればアカウント チームに問い合わせるようにしてください。

特定の適格性認定や規制に対応している Azure Government サービスについては、[Microsoft Azure セキュリティ センター コンプライアンス ページ](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)を参照し、最新情報を確認してください。 その他の Microsoft サービスが利用できる可能性もありますが、Azure Government によって保護されるサービスの範囲には含まれておらず、本ドキュメントでは取り上げません。 また、Azure Government サービスでは、その他さまざまなリソース、アプリケーション、サービスを使用することが認められている場合があります。そうしたリソースやアプリケーション、サービスは、サード パーティまたは Microsoft が、独立した使用条件とプライバシー ポリシーの下で提供するものであり、本ドキュメントの対象外となります。 そうした "付加的" サービス (Marketplace サービスなど) については、付随する条項をご自身の責任ですべて確認し、コンプライアンスに関する要件を確実に満たしてください。

Azure Government は、扱うデータが特定の政府規制や要件 (NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4、CJIS など) の対象となる事業体で利用できます。そのような事業体では、規制に準拠するために Azure Government が必要となります。 Azure Government をご利用のお客様は、適格性検証の対象となります。

Azure Government の適格性に関してご不明な点がある場合は、担当のアカウント チームにお問い合わせください。

## <a name="general-guidance-for-customers"></a>お客様向けの一般的なガイダンス
現在利用可能な技術的なコンテンツのほとんどは、Microsoft Azure Government ではなく、グローバル サービス向けにアプリケーションを開発中であることを前提としているため、開発者は、Azure Government でホストされるように開発されたアプリケーションの主な相違点を確実に意識することが重要です。

* まず、サービスと機能に相違点があります。これは、グローバル サービスの特定のリージョンにある特定の機能は、Azure Government では使用できないことを意味します。
* 第 2 に、Azure Government で提供されている機能の場合、グローバル サービスとは構成上の相違点があります。  そのため、サンプル コード、構成、手順を確認し、Azure Government クラウド サービス環境内で確実に構築、実行するようにしてください。
* また、こちらのサイトで提供されている Azure Government のテクニカル サービス ドキュメントで、Azure Government の機能領域を把握し、ユーザーの規制対象データの指針とベスト プラクティスについての情報をご覧いただく必要があります。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。

Azure Government に関心をお持ちの場合、以下のページで詳細をご確認ください。

* **[サインアップして試用](https://azuregov.microsoft.com/trial/azuregovtrial)**
* **[Azure Government の入手とアクセス](http://azure.com/gov)**
* **[Microsoft セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)**




<!--HONumber=Nov16_HO3-->


