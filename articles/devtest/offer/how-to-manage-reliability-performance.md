---
title: Azure Dev/Test サブスクリプションを使用した信頼性とパフォーマンスの管理
description: Dev/Testサブスクリプションを使用して、アプリケーションに信頼性を構築します。
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: fe34eb3b5fff32ab59c72c90041b095a82806e9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092758"
---
# <a name="reliability-management"></a>信頼性の管理  

実稼働サービスはDev/Testサブスクリプションに含まれませんが、Azure Dev/Testサブスクリプションで他のステージを使用して、運用環境での信頼性を確保することができます。  

組織のDev/Testサブスクリプションを使用する場合、どのように使用するかを定める必要があります。  

- 制御データ  
- セキュリティとアクセスの制御  
- 運用システムの稼働時間の管理  

一般的に、本番環境に入る前には、共有、QA、統合、ステージング、フェイルオーバーなど、様々な段階があります。 これらのステージをどのように定義するかによって、組織のDev/Test用サブスクリプションの使用方法が変わる可能性があります。  

顧客向けアプリケーションなどのミッションクリティカルなサービスを実行している場合は、Dev/Testサブスクリプションを使用しないでください。 開発/テスト用のサブスクリプションには、財政的に裏付けられたSLAはありません。 これらのサブスクリプションは、実稼働前のテストと開発用です。  

## <a name="site-reliability-engineering-sre"></a>サイト信頼性エンジニアリング (SRE)  

エンジニアリングの信頼性と管理の詳細については、サイトの信頼性管理に関するページを参照してください。組織がシステム、サービス、および製品に対して適切な信頼性を維持することを可能とするエンジニアリングの規範です。  

SREとDevOpsの違いについては、現在検討中です。 いくつかの主な違いは次のとおりです。  

- SRE は、信頼性に重点を置いたエンジニアリング規範です。 DevOpsは、開発組織と運用組織の間に存在するサイロを取り払おうという動きから生まれた文化的なムーブメントです。  
- SREは、「私はサイトの信頼性のあるエンジニアです (SRE)」のような役割名となります。 DevOps できません。  
- SRE は規範となる傾向があります。 DevOps は意図的には行われません。 継続的インテグレーション、継続的デリバリー、アジャイルの原則は、世界的に導入されており、最も近い DevOps になります。  

SRE のプラクティスの詳細については、次のリンクを確認してください。  

- [コンテキスト内の SRE](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context.md)  
- [主要な SRE の原則とプラクティス: 好循環サイクル](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles.md)  
- [主要な SRE の原則とプラクティス: SREの人間的側面](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre.md)  
- [SREを始めましょう](/learn/modules/intro-to-site-reliability-engineering/6-getting-started.md)  

## <a name="service-level-agreements"></a>サービス レベル アグリーメント  

Enterprise Dev/Testは、アプリケーションの開発とテスト専用です。 サブスクリプションの使用には、経済的に裏付けされたSLA はありません。  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>様々な種類のDev/Testサブスクリプションを使用する方法について説明します  

Visual Studio サブスクライバー、 [Enterprise Dev/Test サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0148p/)、または[開発テスト用の従量課金制プランサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0023p/)(PAYG)[に対して月単位の Azureクレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)のいずれを必要としている場合でも、個人またはチーム適したプランを簡単に見つけることができます。  

## <a name="managing-individual-credit-subscriptions"></a>個人のクレジットサブスクリプション登録の管理  

Visual StudioAzureクレジットは、個人の開発/テストおよび内部ループ開発の個別のベネフィットです。 開発者間でクレジットをプールすることはできません。 クレジットサブスクリプションはまだ Azure サブスクリプションであることに変わりはありませんが、特定のAzureプランです。 他の Azure サブスクリプションを管理するのと同じ方法でクレジットサブスクリプションを管理し、グループやチーム内で作業できるようにします。 クレジットカードを使用して個人の使用制限を削除したり、Enterprise Dev/Testサブスクリプションが会社の選択した調達方式も選択できます。  

開発者の内部ループ活動では、殆どの場合クレジットを使用しますが、企業または組織のAzure Dev/Testサブスクリプション (従量課金制など) に切り替えます。 このように DevOps プロセスに従うと、個人のクレジットサブスクリプションで内部ループを実行できます。 DevOps の外部ループでは、実稼働環境以外のターゲットは、Enterprise Dev/Testの対象になります。  

クレジットサブスクリプション、Enterprise dev/testサブスクリプション、PAYG サブスクリプションを管理し、それぞれが一意の階層を持つ [管理グループ](../../governance/management-groups/how-to/protect-resource-hierarchy.md) を使用して開発者をセグメント化します。  

## <a name="using-your-organization-azure-devtest-offers"></a>あなたの組織のAzure Dev/Testプランの使用  

組織の Azure Dev/Testサブスクリプションが必要な場合は、2つのプランから選択できます。  

- [従量課金制(PAYG)のDev/Test(0023P)](https://azure.microsoft.com/offers/ms-azr-0023p/-)  
- [Enterprise Dev/Test (Ms-azr-0148p)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

それぞれに独自の割引セットが付属しており、Visual Studioサブスクリプションが必要です。  

各サブスクリプションプランでは、事前に構成された仮想マシンを使用して、クラウド内のDev/Test環境でチームを稼働させることができます。 複数の Azure サブスクリプションを作成し、1つのアカウントから管理します。 分離された環境を維持し、異なるプロジェクトやチームに対して個別の請求を行うことができます。  

Enterprise Dev/Testサブスクリプションには、エンタープライズ契約 (EA) が必要です。 開発テスト用の従量課金制プランサブスクリプションには EA は必要ありませんが、エンタープライズ契約アカウントで使用できます。  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>PAYG プランと Enterprise Dev/Test プランを使用するのはなぜですか?  

PAYG Dev/Testプランは、Visual Studio サブスクライバーとして使用するのに適している場合があります。 個別使用のクレジットサブスクリプションとは異なり、PAYG プランはチーム開発に適しており、1つのサブスクリプション内に複数のユーザーを割り当てることができます。 次のような場合に、PAYG Dev/Testプランが適しています。  

- エンタープライズ契約をされていない方。 この場合、Visual Studio ライセンスを持つ PAYG アカウントのみを作成できます。  
- エンタープライズ契約を作成していますが、組織の契約を使用しないサブスクリプションを設定する必要があります。 独自のサブスクリプションを必要とする一意のプロジェクトがある場合や、プロジェクトまたはチームに対して個別に課金される分離環境を作成する場合があります。  
- IDを分けておくことをお勧めします。 データ、リソース、アプリへのアクセスを保護するために、特定のIDを他のユーザーとは別のものとして保持する必要がある場合があります。  
