---
title: LUIS でオーサリング キーとランタイム キーを使用する方法
titleSuffix: Azure Cognitive Services
description: LUIS では、モデルを作成するためのオーサリング キーと、予測エンドポイントにユーザーの発話を照会するためのランタイム キーの 2 つのキーが使用されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969335"
---
# <a name="authoring-and-runtime-keys"></a>オーサリング キーとランタイム キー

Language Understanding (LUIS) には、次の 2 つのサービスと API セットがあります。 

* オーサリング (以前は_プログラム_と呼ばれていました)
* 予測ランタイム

使用するサービスとその操作方法に応じて、いくつかのキーの種類があります。

## <a name="non-azure-resources-for-luis"></a>LUIS 用の Azure 以外のリソース

### <a name="starter-key"></a>スターター キー

最初に LUIS の使用を開始すると、**スターター キー**が自動的に作成されます。 このリソースでは次のものが提供されます。

* LUIS ポータルまたは API (SDK を含む) を使用した無料のオーサリング サービスの要求
* ブラウザー、API、または SDK を使用して 1 か月あたり 1000 件の無料の予測エンドポイント要求

## <a name="azure-resources-for-luis"></a>LUIS 用の Azure リソース

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS では、次の 3 種類の Azure リソースを使用できます。 
 
|Key|目的|コグニティブ サービス `kind`|コグニティブ サービス `type`|
|--|--|--|--|
|[オーサリング キー](#programmatic-key)|作成、トレーニング、発行、テストを使用して、アプリケーションのデータにアクセスして管理します。 プログラムを使用して LUIS アプリを作成する場合は、LUIS オーサリング キーを作成します。<br><br>`LUIS.Authoring` キーの目的は、次のことを可能にすることです。<br>* トレーニングや発行など、Language Understanding のアプリとモデルをプログラムで管理する<br> * [共同作成者ロール](#contributions-from-other-authors)にユーザーを割り当てることで、オーサリング リソースへのアクセス許可を制御する|`LUIS.Authoring`|`Cognitive Services`|
|[予測キー](#prediction-endpoint-runtime-key)| 予測エンドポイント要求に対してクエリを実行します。 クライアント アプリでスターター リソースによって提供される 1000 件を超えて予測を要求する前に、LUIS 予測キーを作成します。 |`LUIS`|`Cognitive Services`|
|[Cognitive Service マルチサービスのリソース キー](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS およびその他のサポートされている Cognitive Services と共有される予測エンドポイント要求に対するクエリを実行します。|`CognitiveServices`|`Cognitive Services`|

リソースの作成プロセスが完了したら、LUIS ポータルでアプリに[キーを割り当てます](luis-how-to-azure-subscription.md)。

発行およびクエリを行う[リージョン](luis-reference-regions.md#publishing-regions)内で LUIS アプリを作成することが重要です。

> [!CAUTION]
> 便宜上、サンプルの多くでは[スターター キー](#starter-key)が使用されますが、これは、いくつかの無料エンドポイント呼び出しが[クォータ](luis-boundaries.md#key-limits)に提供されるためです。  


### <a name="query-prediction-resources"></a>予測リソースに対するクエリの実行

* ランタイム キーは、ご使用のすべての LUIS アプリまたは特定の LUIS アプリで使用できます。 
* LUIS アプリの作成にはランタイム キーを使用しないでください。 

LUIS ランタイム エンドポイントには、2 つのクエリ スタイルを使用できます。いずれも予測エンドポイントのランタイム キーを使用しますが、場所は異なります。

ランタイムにアクセスするために使用されるエンドポイントでは、次の表の `{region}` で示すように、リソースの領域に固有のサブドメインを使用します。 

## <a name="assignment-of-the-key"></a>キーの割り当て

ランタイム キーは、[LUIS ポータル](https://www.luis.ai)または対応する API を介して[割り当てる](luis-how-to-azure-subscription.md)ことができます。 

## <a name="key-limits"></a>キーの制限

サブスクリプションごとに、各リージョンで最大 10 個のオーサリング キーを作成できます。 

[キーの制限](luis-boundaries.md#key-limits)と [Azure リージョン](luis-reference-regions.md)に関するページを参照してください。 

公開リージョンは、オーサリング リージョンとは異なります。 クライアント アプリケーションを配置する公開リージョンに対応する オーサリング リージョンにアプリを作成してください。

## <a name="key-limit-errors"></a>キーの制限に関するエラー
1 秒あたりのトランザクション数 (TPS) のクォータを超えると、HTTP 429 エラーが発生します。 1 か月あたりのトランザクション数 (TPM) のクォータを超えると、HTTP 403 エラーが発生します。 

## <a name="contributions-from-other-authors"></a>他の作成者からの投稿

**[オーサリング リソースが移行された](luis-migration-authoring.md)アプリの場合**: オーサリング リソースの "_共同作成者_" は、 **[アクセス制御 (IAM)]** ページを使用して、Azure portal で管理されます。 コラボレーターのメール アドレスと "_共同作成者_" ロールを使用して、[ユーザーを追加する方法](luis-how-to-collaborate.md)を参照してください。 

**まだ移行されていないアプリの場合**: すべての "_コラボレーター_" は、LUIS ポータルの **[管理] -> [コラボレーター]** ページから管理されます。

## <a name="move-transfer-or-change-ownership"></a>所有権の移動、移転、または変更

アプリは、Azure リソースによって定義され、所有者のサブスクリプションによって決まります。 

LUIS アプリを移行できます。 Azure portal または Azure CLI では、次のドキュメント リソースを使用します。

* [LUIS のオーサリング リソース間でアプリを移行する](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [新しいリソース グループまたはサブスクリプションにリソースを移行する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [同じサブスクリプション内またはサブスクリプション間でリソースを移行する](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

サブスクリプションの[所有権](../../cost-management-billing/manage/billing-subscription-transfer.md)を移転するには 

**移行したユーザーの場合 - [移行されたオーサリング リソース](luis-migration-authoring.md) アプリ**: リソースの所有者として、`contributor` を追加できます。

**まだ移行していないユーザーの場合**: アプリを JSON ファイルとしてエクスポートします。 別の LUIS ユーザーがアプリをインポートすると、アプリの所有者になります。 新しいアプリには別のアプリ ID が割り当てられます。  

## <a name="access-for-private-and-public-apps"></a>プライベート アプリとパブリック アプリのアクセス

**プライベート** アプリの場合、所有者と共同作成者がランタイム アクセスを利用できます。 **パブリック** アプリについては、ランタイム アクセスは、自分の Azure [Cognitive Service](../cognitive-services-apis-create-account.md) または [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ランタイム リソースを持ち、パブリック アプリの ID を持つすべてのユーザーが利用できます。 

現在、パブリック アプリのカタログはありません。

### <a name="authoring-access"></a>オーサリング アクセス
[LUIS](luis-reference-regions.md#luis-website) ポータルまたは[オーサリング API](https://go.microsoft.com/fwlink/?linkid=2092087) からのアプリへのアクセスは、Azure オーサリング リソースによって制御されます。 

所有者とすべての共同作成者は、アプリを作成できるアクセス権を持っています。 

|オーサリング アクセスに含まれるもの|メモ|
|--|--|
|エンドポイント キーの追加または削除||
|バージョンのエクスポート||
|エンドポイント ログのエクスポート||
|バージョンのインポート||
|アプリの公開|アプリが公開されている場合、オーサリング キーまたはエンドポイント キーを持つすべてのユーザーがアプリにクエリを実行できます。|
|モデルの変更|
|発行|
|[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)のためのエンドポイントの発話の確認|
|トレーニング|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>予測エンドポイントのランタイム アクセス

予測エンドポイントをクエリするためのアクセスは、 **[管理]** セクションの **[アプリケーション情報]** ページの設定によってによって制御されます。 

|[プライベート エンドポイント](#runtime-security-for-private-apps)|[パブリック エンドポイント](#runtime-security-for-public-apps)|
|:--|:--|
|所有者および共同作成者が利用可能|所有者、共同作成者、およびアプリ ID を知っている他の任意のユーザーが利用可能|

LUIS ランタイム キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出されて認証され、承認が確認されたら、呼び出しを LUIS に渡します。 この戦略では中間者攻撃を防ぐことはできませんが、ユーザーに対してキーとエンドポイントの URL を難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。

#### <a name="runtime-security-for-private-apps"></a>プライベート アプリのランタイム セキュリティ

プライベート アプリのランタイムは、以下にのみ使用できます。

|キーとユーザー|説明|
|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーター/共同作成者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|作成者またはコラボレーター/共同作成者によって LUIS に割り当てられた任意のキー|キー使用法のレベルに基づく|

#### <a name="runtime-security-for-public-apps"></a>パブリック アプリのランタイム セキュリティ

アプリをいったんパブリックとして構成すると、キーでエンドポイントのクォータを使い切ってない場合に限り、"_すべての_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーでアプリにクエリを実行できます。

所有者または共同作成者ではないユーザーは、アプリ ID が与えられている場合にのみ、パブリック アプリのランタイムにアクセスできます。 LUIS には、パブリックな "_マーケット_" やパブリック アプリを検索する他の方法がありません。  

パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

## <a name="transfer-of-ownership"></a>所有権の移転

LUIS には、リソースの所有権を移転するという概念はありません。 

## <a name="securing-the-endpoint"></a>エンドポイントのセキュリティ保護 

LUIS 予測ランタイム エンドポイント キーを表示できるユーザーを制御するには、サーバー間環境でキーを呼び出します。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この戦略では man-in-the-middle (中間者) 攻撃を防ぐことはできませんが、ユーザーからエンドポイントを難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。  

## <a name="next-steps"></a>次のステップ

* [バージョン管理](luis-concept-version.md)の概念を理解します。 
* [キーの作成方法](luis-how-to-azure-subscription.md)を確認します。
