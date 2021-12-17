---
title: 使用量の測定、課金、価格
description: Azure Logic Apps における使用量の測定、課金、および各価格モデルのしくみについて説明します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 11/10/2021
ms.openlocfilehash: 90e0703313db99bee1a7d06e9c1bc9d0efac135f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372968"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>Azure Logic Apps の使用量の測定、課金、各価格モデル

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して、スケーリング可能な自動化された統合ワークフローをクラウドに作成して実行できます。 この記事では、Azure Logic Apps および関連リソースにおける、測定、課金、および各価格モデルのしくみについて説明します。 具体的な価格、コスト計画、さまざまなホスティング環境などの情報については、次のコンテンツを確認してください。

* [Azure Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)
* [Azure Logic Apps のコストを計画および管理する](plan-manage-costs.md)
* [シングルテナントとマルチテナント、および統合サービス環境](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>従量課金 (マルチテナント)

マルチテナント Azure Logic Apps では、ロジック アプリとそのワークフローの価格と課金は、[**従量課金** プラン](https://azure.microsoft.com/pricing/details/logic-apps)に従います。 このようなロジック アプリは、さまざまな方法で作成します。たとえば、**ロジック アプリ (従量課金)** リソースの種類を選択するとき、Visual Studio Code で **Azure Logic Apps (従量課金)** 拡張機能を使用するとき、または [自動化タスク](create-automation-tasks-azure-resources.md)を作成するときなどです。

次の表は、マルチテナント Azure Logic Apps のロジック アプリとワークフローで次のコンポーネントを使用した場合に、従量課金モデルでそれらに対して測定と課金がどのように処理されるかをまとめたものです。

| コンポーネント | メータリングと課金 |
| ----------|----------------------|
| トリガーとアクションの操作 | 従量課金モデルには、ワークフローを実行できる、Azure サブスクリプションごとの無料の組み込み操作の "*初期数*" が含まれています。 この数を超えると、"*実行ごと*" に測定が適用され、[従量課金プランの "*アクション*" の価格](https://azure.microsoft.com/pricing/details/logic-apps)に従って課金されます。 その他の操作の種類 (マネージド コネクタなど) の場合は、[従量課金プランの "*標準*" または "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps)に従って課金されます。 詳細については、「[従量課金モデルでのトリガーとアクションの操作](#consumption-operations)」を参照してください。 |
| ストレージ操作 | 測定は、ワークフローの実行履歴からの入力と出力の保存など、"*データ保持に関連するストレージ消費量にのみ*" 適用されます。 課金は[従量課金プランのデータ保持価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 詳細については、「[ストレージ操作](#storage-operations)」を参照してください。 |
| 統合アカウント | 測定は、ロジック アプリで作成して使用する統合アカウントの種類に基づいて適用されます。 課金は、お使いのロジック アプリが [統合サービス環境 (ISE)](#ise-pricing) でデプロイおよびホストされていない限り、["*統合アカウント*" の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 詳細については、「[統合アカウント](#integration-accounts)」を参照してください。 |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>従量課金モデルでのトリガーとアクションの操作

ワークフローを実行できる Azure サブスクリプションごとの無料の組み込み操作実行の初期数を除き、従量課金モデルでは、ワークフロー全体が正常に実行、完了、またはインスタンス化されるかどうかに関係なく、"*実行ごと*" に操作が測定されて課金されます。 [操作で再試行が有効になっていない限り](#other-operation-behavior)、通常、1 つの操作で 1 回の実行が行われます。 同様に、[大量のデータを取得するために操作でチャンクまたは改ページがサポートされている場合を除き](logic-apps-handle-large-messages.md)、通常、1 回の実行で 1 回の呼び出しが行われます。 チャンクまたは改ページが有効になっている場合、1 回の操作の実行で複数の呼び出しが必要な場合があります。 従量課金モデルでは、"*呼び出しごとではなく、実行ごとに*" 操作が測定されて課金されます。

たとえば、ワークフローが、エンドポイントへの送信呼び出しを定期的に行ってレコードを取得するポーリング トリガーから始まるとします。 送信呼び出しは、トリガーが起動されたかスキップされたかにかかわらず、1 回の実行として測定されて課金されます。たとえば、トリガーによってエンドポイントが調べられたが、データやイベントが見つからない場合などです。 ワークフロー インスタンスを作成して実行するかどうかは、トリガーの状態によって制御されます。 今度は、この操作でチャンクまたは改ページもサポートされていて、有効になっているとします。 すべてのデータの取得を完了するために、操作で 10 回の呼び出しを行う必要がある場合、この操作は複数の呼び出しを行っているにもかかわらず、"*1 回の実行*" として測定され、課金されます。

次の表は、マルチテナント Azure Logic Apps のロジック アプリとワークフローでこれらの操作の種類を使用した場合に、従量課金モデルでそれらの操作に対して測定と課金がどのように処理されるかをまとめたものです。

| 演算の種類 | 説明 | メータリングと課金 |
|----------------|-------------|----------------------|
| "[*組み込み*](../connectors/built-in.md)" | これらの操作は、Azure Logic Apps ランタイムで直接およびネイティブに実行されます。 デザイナーでは、これらの操作は **[組み込み]** ラベルの下にあります。 <p>たとえば、HTTP トリガーと要求トリガーは組み込みトリガーです。 HTTP アクションと応答アクションは組み込みアクションです。 その他の組み込み操作には、ループや条件などのワークフロー制御アクション、データ操作、バッチ操作などが含まれます。 | 従量課金モデルには、ワークフローを実行できる、Azure サブスクリプションごとの "*無料の組み込み操作の初期数*" が含まれています。 この数を超えると、組み込みの操作の実行は、["*アクション*" の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 <p><p>**注**: 一部のマネージド コネクタ操作は、初期の無料操作に含まれる組み込み操作として "*も*" 使用できます。 初期の無料操作を超えると、["*標準*" または "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps/)ではなく、["*アクション*" の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従って課金されます。 |
| [*マネージド コネクタ*](../connectors/managed.md) | これらの操作は Azure で個別に実行されます。 デザイナーでは、これらの操作は **[標準]** または **[エンタープライズ]** ラベルの下にあります。 | これらの操作の実行は、["*標準*" または "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 <p><p>**注**: プレビューのエンタープライズ コネクタ操作の実行は、[従量課金の "*標準*" コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 |
| [*カスタム コネクタ*](../connectors/apis-list.md#custom-apis-and-connectors) | これらの操作は Azure で個別に実行されます。 デザイナーでは、これらの操作は **[カスタム]** ラベルの下にあります。 コネクタの数、スループット、タイムアウトの制限については、[Azure Logic Apps におけるカスタム コネクタの制限](logic-apps-limits-and-config.md#custom-connector-limits)に関するセクションを参照してください。 | これらの操作の実行は、["*標準*" コネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 |
||||

従量課金モデルが、ループなどの他の操作内で実行される操作、配列などの複数の項目の処理、および再試行ポリシーでどのように動作するかについて詳しくは、「[その他の操作の動作](#other-operation-behavior)」で確認してください。

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>従量課金モデルのコスト見積もりに関するヒント

以下のヒントを確認すると、より正確な消費コストの見積もりに役立ちます。

* ポーリング間隔にのみ基づいて計算するのではなく、それぞれの日に到着する可能性のあるメッセージまたはイベントの数を考慮します。

* イベントまたはメッセージがトリガー条件を満たしている場合、多くのトリガーは、条件を満たす任意のすべての他の待機イベントまたはメッセージを直ちに読み取ろうとします。 この動作は、長いポーリング間隔を選択した場合でも、ワークフロー開始の対象となる待機イベントまたはメッセージの数に基づいてトリガーが起動することを意味します。 この動作に続くトリガーには、Azure Service Bus、Azure Event Hub などがあります。

  たとえば、毎日エンドポイントをチェックするトリガーを設定したとします。 トリガーはエンドポイントを確認し、条件を満たす 15 のイベントを検出した場合、トリガーが起動し、対応するワークフローを 15 回実行します。 Logic Apps サービスでは、トリガー要求を含め、これら 15 のワークフローによって実行されるすべてのアクションが従量課金されます。

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>標準 (シングルテナント)

シングルテナント Azure Logic Apps では、ロジック アプリとそのワークフローの価格と課金は、[**標準** プラン](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 このようなロジック アプリは、さまざまな方法で作成します。たとえば、**ロジック アプリ (標準)** リソースの種類を選択するときや、Visual Studio Code で **Azure Logic Apps (標準)** 拡張機能を使用するときなどです。 この価格モデルでは、ロジック アプリでホスティング プランと価格レベルを使用する必要があります。これは、予約容量と専用リソースに対して、それらを使用しているかどうかに関係なく課金されるという点で、従量課金プランとは異なります。

リソースの種類を **ロジック アプリ (Standard)** にして新しいロジック アプリを作成またはデプロイするとき、すべての Azure リージョンでワークフロー Standard ホスティング プランを使用できます。 既存の **App Service Environment v3** リソースを選択することもできますが、このオプションは App Service プランでのみ利用できます。 このオプションを選択した場合、App Service プランで使用されたインスタンスと、ロジック アプリ ワークフローの実行に対して課金されます。 その他の料金は適用されません。

> [!IMPORTANT]
> Azure リージョンでは現在、プランまたはリソースである Functions Premium、App Service Environment v1、App Service Environment v2 は、リソース タイプ **Logic App (Standard)** のパブリック リリースで利用できなくなっています。あるいはサポートされていません。 ASEv3 を除き、App Service プランは利用できず、サポートされていません。

次の表は、シングルテナント Azure Logic Apps のロジック アプリとワークフローで次のコンポーネントを使用した場合に、標準モデルでそれらに対して測定と課金がどのように処理されるかをまとめたものです。

| コンポーネント | メータリングと課金 |
|-----------|----------------------|
| 仮想 CPU (vCPU) とメモリ | 標準モデルでは、ロジック アプリで **ワークフロー標準** ホスティング プランと価格レベルを使用する "*必要があります*"。これにより、コンピューティングとメモリ容量に適用されるリソース レベルと価格が決まります。 詳細については、「[標準モデルの価格レベル](#standard-pricing-tiers)」を参照してください。 |
| トリガーとアクションの操作 | 標準モデルには、ワークフローで実行できる "*無制限の数*" の無料の組み込み操作が含まれています。 <p>ワークフローでマネージド コネクタ操作を使用する場合、それらの操作の測定は "*各呼び出し*" に適用されますが、課金は [従量課金プランと同じ "*標準*" または "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps)に従います。 詳細については、「[標準モデルでのトリガーとアクションの操作](#standard-operations)」を参照してください。 |
| ストレージ操作 | 測定は、Azure Logic Apps によって実行されるすべてのストレージ操作に適用されます。 たとえば、サービスがワークフローの実行履歴からの入力と出力を保存するときに実行されるストレージ操作などです。 課金は、お客様が選択した[価格レベル](#standard-pricing-tiers)に従います。 詳細については、「[ストレージ操作](#storage-operations)」を参照してください。 |
| 統合アカウント | ロジック アプリで使用する統合アカウントを作成する場合、測定は作成した統合アカウントの種類に基づいて行われます。 課金は、["*統合アカウント*" の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 詳細については、「[統合アカウント](#integration-accounts)」を参照してください。 |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>標準モデルの価格レベル

ロジック アプリの測定と課金に選択する価格レベルには、仮想 CPU (vCPU) とメモリ リソースの特定の量のコンピューティングが含まれます。 現在、**ロジック アプリ (標準)** リソースの種類で利用できるのは **ワークフロー標準** ホスティング プランのみで、次の価格レベルが提供されています。

| Pricing tier | 仮想 CPU (vCPU) | メモリ (GB) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3.5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> 次の例は説明のみを目的としており、価格レベルのしくみを一般的に示すためのサンプルの見積もりを提供します。 
> Azure Logic Apps が利用可能な特定のリージョンに基づく具体的な vCPU とメモリの価格については、[Azure Logic Apps の価格ページで選択したリージョンの標準プラン](https://azure.microsoft.com/pricing/details/logic-apps/)を確認してください。
>
> サンプルのリージョンでは、次のリソースの 1 時間あたりの価格が次のようになっているとします。
>
> | リソース | 1 時間あたりの価格 (サンプルのリージョン) |
> |----------|-----------------------------|
> | **vCPU** | vCPU あたり 0.192 ドル |
> | **[メモリ]** | 1 GB あたり 0.0137 ドル |
> |||
>
> 次の計算により、推定月額料金が算出されます。
>
> <*月額料金*> = 730 時間 (1 か月あたり) * [(<*vCPU の数*> * <*vCPU の 1 時間あたりの価格*>) + (<*メモリの GB 数*> * <*メモリ (GB) の 1 時間あたりの価格*>)]
>
> 次の表には、前の情報に基づいて、各価格レベルの推定月額料金と、その価格レベルのリソースが示されています。
>
> | Pricing tier | 仮想 CPU (vCPU) | メモリ (GB) | 月額料金 (サンプルのリージョン) |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3.5 | 175.16 ドル |
> | **WS2** | 2 | 7 | 350.33 ドル |
> | **WS3** | 4 | 14 | 700.65 ドル |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>標準モデルでのトリガーとアクションの操作

ワークフローを実行できる無制限の無料の組み込み操作を除き、標準モデルでは、ワークフロー全体が正常に実行、完了、またはインスタンス化されるかどうかに関係なく、"*呼び出しごと*" に操作が測定されて課金されます。 [操作で再試行が有効になっていない限り](#other-operation-behavior)、通常、1 つの操作で 1 回の実行が行われます。 同様に、[大量のデータを取得するために操作でチャンクまたは改ページがサポートされている場合を除き](logic-apps-handle-large-messages.md)、通常、1 回の実行で 1 回の呼び出しが行われます。 チャンクまたは改ページが有効になっている場合、1 回の操作の実行で複数の呼び出しが必要な場合があります。 標準モデルでは、"*実行ごとではなく、呼び出しごとに*" 操作が測定されて課金されます。

たとえば、ワークフローが、エンドポイントへの送信呼び出しを定期的に行ってレコードを取得するポーリング トリガーから始まるとします。 この送信呼び出しは、トリガーが起動またはスキップされたかどうかに関係なく、測定され、課金されます。 ワークフロー インスタンスを作成して実行するかどうかは、トリガーの状態によって制御されます。 今度は、この操作でチャンクまたは改ページもサポートされていて、有効になっているとします。 操作ですべてのデータの取得を完了するために 10 回の呼び出しを行う必要がある場合、その操作は "*呼び出しごと*" に測定され課金されます。

次の表は、シングルテナント Azure Logic Apps のロジック アプリとワークフローで次の操作の種類を使用した場合に、標準モデルでそれらに対して測定と課金がどのように処理されるかをまとめたものです。

| 演算の種類 | 説明 | メータリングと課金 |
|----------------|-------------|----------------------|
| "[*組み込み*](../connectors/built-in.md)" | これらの操作は、Azure Logic Apps ランタイムで直接およびネイティブに実行されます。 デザイナーでは、これらの操作は **[組み込み]** ラベルの下にあります。 <p>たとえば、HTTP トリガーと要求トリガーは組み込みトリガーです。 HTTP アクションと応答アクションは組み込みアクションです。 その他の組み込み操作には、ループや条件などのワークフロー制御アクション、データ操作、バッチ操作などが含まれます。 | 標準モデルには、無制限の無料の組み込み操作が含まれています。 <p><p>**注**: 一部のマネージド コネクタ操作は、組み込み操作として "*も*" 使用できます。 組み込み操作は無料ですが、標準モデルでは引き続き、[従量課金モデルと同じ "*標準*" または "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps/)を使用して、マネージド コネクタ操作が測定され課金されます。 |
| [*マネージド コネクタ*](../connectors/managed.md) | これらの操作は Azure で個別に実行されます。 デザイナーでは、これらの操作は結合された **[Azure]** ラベルの下にあります。 | 標準モデルでは、[従量課金モデルと同じ "*標準*" および "*エンタープライズ*" のコネクタ価格](https://azure.microsoft.com/pricing/details/logic-apps/)に基づいてマネージド コネクタ操作が測定され課金されます。 <p><p>**注**: プレビューのエンタープライズ コネクタ操作は、[従量課金の "*標準*" コネクタの価格](https://azure.microsoft.com/pricing/details/logic-apps/)に従います。 |
| [*カスタム コネクタ*](../connectors/apis-list.md#custom-apis-and-connectors) | 現在、シングルテナント ベースのロジック アプリ ワークフローでは、[カスタムの組み込みコネクタ操作](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)のみを作成して使用できます。 | 標準モデルには、無制限の無料の組み込み操作が含まれています。 スループットとタイムアウトの制限については、[Azure Logic Apps におけるカスタム コネクタの制限](logic-apps-limits-and-config.md#custom-connector-limits)に関するセクションを参照してください。 |
||||

標準モデルが、ループなどの他の操作内で実行される操作、配列などの複数の項目の処理、および再試行ポリシーでどのように動作するかについて詳しくは、「[その他の操作の動作](#other-operation-behavior)」で確認してください。

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>統合サービス環境 (ISE)

**ロジック アプリ (従量課金)** のリソースの種類を使用してロジック アプリを作成し、専用の [ "*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) にデプロイすると、そのロジック アプリとそのワークフローは、[統合サービス環境プラン](https://azure.microsoft.com/pricing/details/logic-apps)の価格に従って課金されます。 この価格モデルは、ご利用の [ISE レベルまたは *SKU*](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に依存しており、予約容量と専用リソースに対して、それらを使用しているかどうかに関係なく課金されるという点で、従量課金プランとは異なります。

次の表は、ISE レベルまたは SKU に基づいて、ISE モデルで容量および他の専用リソースの測定と課金がどのように処理されるかをまとめたものです。

| ISE SKU | メータリングと課金 |
|---------|----------------------|
| **Premium** | 基本単位は[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)であり、[Premium SKU の 1 時間あたりの料金で課金されます](https://azure.microsoft.com/pricing/details/logic-apps)。 さらにスループットが必要な場合は、ISE の作成時または作成後に[スケール ユニットをさらに追加](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)できます。 スケール ユニットごとに、[基本単位の料金のおよそ半額となる 1 時間あたりの料金で課金](https://azure.microsoft.com/pricing/details/logic-apps)されます。 <p><p>容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 |
| **開発者** | 基本単位は[固定容量](logic-apps-limits-and-config.md#integration-service-environment-ise)であり、[Developer SKU の 1 時間あたりの料金で課金されます](https://azure.microsoft.com/pricing/details/logic-apps)。 ただし、この SKU には、サービス レベル アグリーメント (SLA)、スケールアップ機能、リサイクル中の冗長性がありません。これは、遅延やダウンタイムが発生する可能性があることを意味します。 バックエンドの更新により、サービスが断続的に中断する場合があります。 <p><p>**重要**:この SKU は、必ず探索、実験、開発、テストにのみ使用し、運用環境やパフォーマンス テストには使用しないでください。 <p><p>容量と制限については、[Azure Logic Apps での ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)に関する記事を参照してください。 |
|||

次の表は、次のコンポーネントを ISE のロジック アプリとワークフローで使用した場合に、ISE モデルでこれらがどのように処理されるかをまとめたものです。

| コンポーネント | 説明 |
|-----------|-------------|
| トリガーとアクションの操作 | ISE モデルには、ワークフローで実行できる無料の組み込みのマネージド コネクタとカスタム コネクタの操作が含まれていますが、[Azure Logic Apps における ISE の制限](logic-apps-limits-and-config.md#integration-service-environment-ise)と [Azure Logic Apps におけるカスタム コネクタの制限](logic-apps-limits-and-config.md#custom-connector-limits)が適用されます。 詳細については、「[ISE モデルでのトリガーとアクションの操作](#integration-service-environment-operations)」を参照してください。 |
| ストレージ操作 | ISE モデルには、データ保持などの無料のストレージ使用量が含まれています。 詳細については、「[ストレージ操作](#storage-operations)」を参照してください。 |
| 統合アカウント | ISE モデルには、選択した ISE SKU に基づく、無料の統合アカウント レベルが 1 つ含まれています。 [追加料金](https://azure.microsoft.com/pricing/details/logic-apps/)で、ISE の統合アカウントをさらに作成して[合計の ISE 制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)の上限まで使用することができます。 詳細については、「[統合アカウント](#integration-accounts)」を参照してください。 |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>ISE モデルでのトリガーとアクションの操作

次の表は、次の操作の種類を ISE のロジック アプリとワークフローで使用した場合に、ISE モデルでこれらがどのように処理されるかをまとめたものです。

| 演算の種類 | 説明 | メータリングと課金 |
|----------------|-------------|----------------------|
| "[*組み込み*](../connectors/built-in.md)" | これらの操作は、お使いのロジック アプリ ワークフローと同じ Azure Logic Apps ランタイムで直接およびネイティブで実行されます。 デザイナーでは、これらの操作は **[組み込み]** ラベルの下にありますが、各操作には **[CORE]** ラベルも表示されます。 <p>たとえば、HTTP トリガーと要求トリガーは組み込みトリガーです。 HTTP アクションと応答アクションは組み込みアクションです。 その他の組み込み操作には、ループや条件などのワークフロー制御アクション、データ操作、バッチ操作などが含まれます。 | ISE モデルにはこれらの操作が "*無料で*"含まれていますが、[Azure Logic Apps における ISE 制限](logic-apps-limits-and-config.md#integration-service-environment-ise)が適用されます。 |
| [*マネージド コネクタ*](../connectors/managed.md) | "*標準*" または "*エンタープライズ*" のどちらの場合でも、マネージド コネクタ操作は、コネクタまたは操作に **[ISE]** ラベルが表示されているかどうかに基づいて、ISE またはマルチテナント Azure のいずれかで実行されます。 <p><p>-  **[ISE]** ラベルあり: これらの操作は、ロジック アプリと同じ ISE で実行され、[オンプレミス データ ゲートウェイ](#data-gateway)を必要とせずに機能します。 <p><p>- **[ISE]** ラベルなし: これらの操作はマルチテナント Azure で実行されます。 | ISE モデルには、 **[ISE]** ラベルありと **[ISE]** ラベルなしの両方の操作が "*無料で*" 含まれていますが、[Azure Logic Apps における ISE 制限](logic-apps-limits-and-config.md#integration-service-environment-ise)が適用されます。 |
| [*カスタム コネクタ*](../connectors/apis-list.md#custom-apis-and-connectors) | デザイナーでは、これらの操作は **[カスタム]** ラベルの下にあります。 | ISE モデルにはこれらの操作が "*無料で*" 含まれていますが、[Azure Logic Apps におけるカスタム コネクタの制限](logic-apps-limits-and-config.md#custom-connector-limits)が適用されます。 |
||||

ISE モデルが、ループなどの他の操作内で実行される操作、配列などの複数の項目の処理、および再試行ポリシーでどのように動作するかについて詳しくは、「[その他の操作の動作](#other-operation-behavior)」で確認してください。

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>その他の操作の動作

次の表は、従量課金、標準、ISE の各モデルでの、ループなどの他の操作内で実行される操作、配列などの複数の項目の処理、および再試行ポリシーの処理方法をまとめたものです。

| 操作 | 説明 | 従量課金 | Standard | ISE |
|-----------|-------------|-------------|----------|-----|
| [ループ アクション](logic-apps-control-flow-loops.md) | **For each** や **Until** ループなどのループ アクションには、各ループ サイクル中に実行される他のアクションを含めることができます。 | 含まれている組み込み操作の初期数を除き、ループ アクションとループ内の各アクションは、ループ サイクルが実行されるたびに測定されます。 リストや配列など、コレクション内の項目をアクションで処理する場合、項目数も測定計算に使用されます。 <p><p>たとえば、リストを処理するアクションが含まれた **For each** ループがあるとします。 サービスによって、リスト項目の数がループ内のアクションの数と乗算され、ループを開始するアクションが加算されます。 したがって、10 項目のリストの場合の計算は (10 * 1) + 1 になり、結果は 11 個のアクション実行になります。 <p><p>料金は、操作の種類が組み込み、標準、またはエンタープライズのいずれであるかに基づきます。 | 含まれている組み込み操作以外は、従量課金モデルと同じです。 | 測定も課金もされません。 |
| [再試行ポリシー](logic-apps-exception-handling.md#retry-policies) | サポートされている操作では、[再試行ポリシー](logic-apps-exception-handling.md#retry-policies)を設定することにより、基本的な例外およびエラー処理を実装できます。 | 組み込み操作の初期数を除き、元の実行と再試行された各実行が測定されます。 たとえば、5 回の再試行を含む 1 つのアクションを実行すると、6 回の実行として測定され、課金されます。 <p><p>料金は、操作の種類が組み込み、標準、またはエンタープライズのいずれであるかに基づきます。 | 組み込みで含まれている操作以外は、従量課金モデルと同じです。 | 測定も課金もされません。 |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>ストレージ操作

Azure Logic Apps では、トリガー操作のスケジュールにキューを使用したり、ワークフローの状態を格納するためにテーブルと BLOB を使用したりするなど、必要なストレージ トランザクションに [Azure Storage](../storage/index.yml) が使用されます。 ワークフロー内の操作に基づいて、ストレージのコストは異なります。これは、トリガー、アクション、およびペイロードが異なると、ストレージの操作とニーズも異なるためです。 また、このサービスでは、ロジック アプリ リソースの[実行履歴のリテンション期間の制限](logic-apps-limits-and-config.md#run-duration-retention-limits)に基づいて、ワークフローの実行履歴からの入力と出力の保存と格納も行われます。 このリテンション期間の制限は、ワークフロー レベルではなく、ロジック アプリのリソース レベルで管理できます。

次の表は、従量課金、標準、ISE の各モデルでのストレージ操作の測定と課金の処理方法をまとめたものです。

| モデル | 説明 | メータリングと課金 |
|-------|-------------|----------------------|
| 従量課金 (マルチテナント) | ストレージ リソースと使用状況は、ロジック アプリ リソースにアタッチされます。 | 測定と課金は、"*データ保持に関連するストレージ消費量にのみ適用*" され、[従量課金プランのデータ保持価格](https://azure.microsoft.com/pricing/details/logic-apps)に従います。 |
| 標準 (シングルテナント) | 独自の Azure [ストレージ アカウント](../azure-functions/storage-considerations.md#storage-account-requirements)を使用できます。これにより、ワークフローのデータをより細かく制御し、柔軟性を高めることができます。 |  測定と課金は、[Azure Storage の価格モデル](https://azure.microsoft.com/pricing/details/storage/)に従います。 ストレージ コストは、Azure の請求書に個別に表示されます。 <p><p>**ヒント**: ワークフローで実行される可能性のあるストレージ操作の数とそのコストに対する理解を深めるには、[Logic Apps ストレージ計算ツール](https://logicapps.azure.com/calculator)を使用してみてください。 サンプル ワークフローを選択するか、既存のワークフロー定義を使用します。 最初の計算では、ワークフロー内のストレージ操作の数を推定します。 その後、これらの数値を使用して、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用して考えられるコストを見積もることができます。 詳細については、[シングルテナントの Azure Logic Apps でのワークフローのストレージ ニーズとコストの推定](estimate-storage-costs.md)に関する記事を参照してください。 |
| 統合サービス環境 (ISE) | ストレージ リソースと使用状況は、ロジック アプリ リソースにアタッチされます。 | 測定も課金もされません。 |
||||

詳細については、次のドキュメントを確認してください。

* [実行とストレージ消費のメトリックを表示する](plan-manage-costs.md#monitor-billing-metrics)
* [Azure Logic Apps の制限](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>オンプレミスのデータ ゲートウェイ

[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-install.md)は、特定のゲートウェイでサポートされているコネクタを使用してロジック アプリ ワークフローからオンプレミス データにアクセスできるように作成する、独立した Azure リソースです。 ゲートウェイ リソース自体には料金は発生しませんが、ゲートウェイを介して実行される操作には、お使いのロジック アプリで使用されている価格と課金モデルに基づいて料金が発生します。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>統合アカウント

[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)は、取引先、契約、スキーマ、マップなどの企業間 (B2B) 成果物を定義して格納するためのコンテナーとして作成する別個の Azure リソースです。 このアカウントを作成してこれらの成果物を定義したら、このアカウントをお使いのロジック アプリにリンクして、ワークフローでこれらの成果物とさまざまな B2B 操作を使用して、[EDI](logic-apps-enterprise-integration-b2b.md) および [XML 処理](logic-apps-enterprise-integration-xml.md)機能を使用する統合ソリューションを探索、構築、およびテストできるようにします。

次の表は、従量課金、標準、ISE の各モデルでの統合アカウントの測定と課金の処理方法をまとめたものです。

| モデル | メータリングと課金 |
|-------|----------------------|
| 従量課金 (マルチテナント) | 測定と課金には、使用するアカウント レベルに基づいて、[統合アカウントの価格](https://azure.microsoft.com/pricing/details/logic-apps/)が使用されます。 |
| 標準 (シングルテナント) | 測定と課金には、使用するアカウント レベルに基づいて、[統合アカウントの価格](https://azure.microsoft.com/pricing/details/logic-apps/)が使用されます。 |
| ISE | このモデルには、お使いの ISE SKU に基づいて、1 つの統合アカウントが含まれます。 [追加料金](https://azure.microsoft.com/pricing/details/logic-apps/)で、ISE の統合アカウントをさらに作成して[合計の ISE 制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)の上限まで使用することができます。 |
|||

詳細については、次のドキュメントを確認してください。

* [統合アカウントを作成および管理する](logic-apps-enterprise-integration-create-integration-account.md)
* [Azure Logic Apps における統合アカウントの制限](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>測定および課金されないその他の項目

すべての価格モデルで、次の項目は測定も課金もされません。

* 完了前にワークフローが停止したために実行されなかったアクション
* 無効なロジック アプリまたはワークフロー。これらが無効になっている間は新しいインスタンスを作成できないからです。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps のコストを計画および管理する](plan-manage-costs.md)
