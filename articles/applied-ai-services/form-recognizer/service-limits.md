---
title: Form Recognizer のクォータと制限
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer サービスのクォータと制限に関するクイック リファレンス、詳細な説明、およびベスト プラクティス
services: cognitive-services
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: vikurpad
ms.openlocfilehash: 8a1b57571acdbfd8dce29e45218cfd096b0906f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477873"
---
# <a name="form-recognizer-service-quotas-and-limits"></a>Azure Form Recognizer サービスのクォータと制限

この記事には、すべての [価格レベル](https://azure.microsoft.com/pricing/details/form-recognizer/)の Azure Form Recognizer サービスのクォータと制限に関するクイック リファレンスおよび **詳細な説明** が記載されています。 また、要求のスロットリングを回避するためのベスト プラクティスについても説明します。 

[Form Recognizer SDK](quickstarts/try-v3-csharp-sdk.md)、[Form Recognizer REST API](quickstarts/try-v3-rest-api.md)、[Form Recognizer Studio](quickstarts/try-v3-form-recognizer-studio.md)、および[サンプル ラベル付けツール](https://fott-2-1.azurewebsites.net/)での使用。

| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **同時要求の制限** | 1 | 15 (既定値) |
| 調整可能 | いいえ<sup>2</sup> | はい<sup>2</sup> |
| **Compose モデルの制限** | 5 | 100 (既定値) |


<sup>1</sup> **Free (F0)** 価格レベルについては、[価格ページ](https://azure.microsoft.com/pricing/details/form-recognizer/)で月額料金に関するページを参照してください。
<sup>2</sup> [ベスト プラクティス](#example-of-a-workload-pattern-best-practice)および[調整手順](#create-and-submit-support-request)に関するセクションを参照してください。

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>詳細な説明、クォータの調整、およびベスト プラクティス
クォータの引き上げを要求する前に (該当する場合)、それが必要であることを確認します。 Form Recognizer サービスでは、自動スケーリングを用いて必要なコンピューティング リソースを " オンデマンド " で提供すると同時に、顧客のコストを低く抑えるために、過剰なハードウェア容量を維持しないことで未使用のリソースをプロビジョニング解除します。 ワークロードが定義された制限内であるにもかかわらず、アプリケーションで応答コード 429 ("要求が多すぎます") を受け取る場合 ([クォータと制限のクイック リファレンス](#form-recognizer-service-quotas-and-limits)のセクションを参照)、最も可能性がある説明は、サービスによって需要に対応するためのスケールアップが行われている最中であり、まだ必要なスケールに達していないため、要求に対応するのに十分なリソースを今すぐ用意できないということです。 この状態は通常一時的なものであり、長くは続かないはずです。

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>自動スケーリング時のスロットリングを緩和するための一般的なベスト プラクティス
スロットリングに関連する問題 (応答コード 429) を最小限に抑えるには、次の手法を使用することをお勧めします。
- アプリケーションで再試行ロジックを実装します。
- ワークロードが急激に変化しないようにします。 ワークロードは徐々に増やします <br/>
*例:* アプリケーションでは、Form Recognizer が使用されており、現在のワークロードは 10 TPS (1 秒あたりのトランザクション数) です。 次の 1 秒間で、負荷を 40 TPS (4 倍以上) に増やしたとします。 この新しい負荷に対応するため、サービスでは直ちにスケールアップが開始されますが、おそらく 1 秒以内に処理することはできないため、一部の要求では応答コード 429 が返されます。

次のセクションでは、クォータを調整する特定のケースについて説明します。
[Form Recognizer: 同時要求の制限を引き上げる](#create-and-submit-support-request)方法に移動

### <a name="increasing-transactions-per-second-request-limit"></a>1 秒あたりのトランザクション数に関する要求の制限を引き上げる
既定では、同時要求の数は、Form Recognizer リソースに対して 1 秒あたり 15 トランザクションに制限されます。 Standard 価格レベルでは、この数を増やすことができます。 要求を送信する前に、[こちらのセクション](#detailed-description-quota-adjustment-and-best-practices)の資料について理解していること、およびこれらの[ベスト プラクティス](#example-of-a-workload-pattern-best-practice)を把握していることを確認してください。

同時要求の上限を上げても、コストに直接影響することは **ありません**。 Form Recognizer サービスでは、"使用した分だけ支払う" モデルを使用しています。 この制限によって、要求のスロットリングが開始される前に、サービスがどの程度スケーリングされるかが定義されます。

同時要求の上限パラメーターの既存の値は、Azure portal、コマンドライン ツール、または API 要求では表示 **されません**。 既存の値を確認するには、Azure サポート リクエストを作成します。

#### <a name="have-the-required-information-ready"></a>以下の必要な情報を準備します。
- Form Recognizer リソース ID
- Region

- **情報を取得する方法 (ベース モデル)** :
  - [Azure portal](https://portal.azure.com/) に移動します
  - トランザクションの制限を引き上げる Form Recognizer リソースを選択します
  - *プロパティ* (*リソース管理* グループ) を選択します
  - 次のフィールドの値をコピーして保存しておきます。
    - **リソース ID**
    - **場所** (エンドポイントのリージョン)

#### <a name="create-and-submit-support-request"></a>サポート リクエストの作成と送信
サポート リクエストを送信して、リソースの 1 秒あたりのトランザクション数 (TPS) の制限を引き上げる手順を開始します。

- [必要な情報](#have-the-required-information-ready)があることを確認します
- [Azure portal](https://portal.azure.com/) に移動します
- TPS の制限を引き上げる Form Recognizer リソースを選択します
- *[新しいサポート リクエスト]* ( *[サポート + トラブルシューティング]* グループ) を選択します
- Azure サブスクリプションと Azure リソースに関する情報が自動的に入力された新しいウィンドウが表示されます
- "*概要*" を入力します (たとえば、"Form Recognizer の TPS 制限の引き上げ")
- *[問題の種類]* で、[Quota or usage validation]\(クォータまたは使用状況の検証\) を選択します
- *[次へ: ソリューション]* をクリックします
- 要求の作成を進めます
- *[詳細]* タブの *[説明]* フィールドに以下を入力します:
  - この要求が **Form Recognizer** のクォータに関するものであることを示すメモ
  - TPS に関して希望するスケーリングの目標値を指定します    
  - [前に収集](#have-the-required-information-ready)した Azure リソース情報
  - 必要な情報を入力して、 *[確認と作成]* タブの *[作成]* ボタンをクリックします
  - Azure portal 通知のサポート リクエスト番号をメモしておきます。 後続の処理のための連絡が間もなくして届きます

## <a name="example-of-a-workload-pattern-best-practice"></a>ワークロード パターンの例のベスト プラクティス
この例では、[自動スケーリングが進行中である](#detailed-description-quota-adjustment-and-best-practices)ことによって発生する可能性がある要求のスロットリングを軽減するために、次のようなアプローチを推奨しています。 これは "正確なレシピ" ではなく、必要に応じて従い、調整するテンプレートにすぎません。

Form Recognizer リソースに既定の制限が設定されているとします。 ワークロードを開始して、分析要求を送信します。 応答コード 429 で頻繁にスロットリングが発生している場合は、応答分析取得要求をバックオフすることから始めて、2-3-5-8 パターンを使用して再試行します。 一般に、対応する POST 要求に対して 2 秒に 1 回以上応答分析取得を呼び出さないことをお勧めします。 

送信されるドキュメントの POST 要求の数が調整されていることがわかった場合は、要求間に遅延を追加することを検討してください。 ワークロードでより高度な同時処理が必要な場合は、サポート リクエストを作成して、1 秒あたりのトランザクションのサービス制限を引き上げる必要があります。

一般に、運用環境に移行する前にワークロードとワークロード パターンをテストしておくことを強くお勧めします。

