---
title: Microsoft Sentinel コンテンツとソリューションについて | Microsoft Docs
description: この記事では、Microsoft Sentinel のコンテンツとソリューションについて説明します。データ コネクタと一緒にパッケージ化されているデータ分析ツールを見つけるために利用できます。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5e4f129c53ac26c66a6ca26dbe70781803809a3
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711649"
---
# <a name="about-microsoft-sentinel-content-and-solutions"></a>Microsoft Sentinel コンテンツとソリューションについて

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft Sentinel **コンテンツ ハブ** とソリューションは現在 **プレビュー版** です。個別のソリューション パッケージもすべてプレビュー版です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Microsoft Sentinel "*コンテンツ*" は、Microsoft Sentinel のさまざまな製品、プラットフォーム、サービスに対してデータの取り込み、監視、アラート、監視、調査、応答、接続を行うことができるようにするセキュリティ情報イベント管理 (SIEM) のコンテンツです。

Microsoft Sentinel のコンテンツには、次のいずれかの種類が含まれます。

- **[データ コネクタ](connect-data-sources.md)** によって、ログが、さまざまなソースから Microsoft Sentinel に取り込まれます
- **[パーサー](normalization-about-parsers.md)** では、ログの書式設定または [ASIM](normalization.md) 形式への変換が提供され、Microsoft Sentinel コンテンツのさまざまな種類とシナリオでの使用に対応しています
- **[ブック](get-visibility.md)** によって、Microsoft Sentinel のデータの監視、視覚化、対話機能が提供され、ユーザーにとって意味のある分析情報が強調表示されます
- **[分析ルール](detect-threats-built-in.md)** では、インシデントを介して関連する SOC アクションを指すアラートが提供されます。
- **[ハンティング クエリ](hunting.md)** は、SOC チームが、Microsoft Sentinel で脅威を事前にハンティングするために使用します
- **[Notebook](notebooks.md)** は、SOC チームが Jupyter と Azure Notebooks の高度なハンティング機能を使用するのに役立ちます
- **[ウォッチリスト](watchlists.md)** は、脅威の検出を強化し、アラート疲れを減らすために、特定のデータの取り込みをサポートします
- **[プレイブックと Azure Logic Apps カスタム コネクタ](automate-responses-with-playbooks.md)** の機能を使用して、Microsoft Sentinel で自動的な調査、修復、対応のシナリオを実現します

Microsoft Sentinel "*ソリューション*"は、Microsoft Sentinel コンテンツまたは Microsoft Sentinel API 統合のパッケージであり、Microsoft Sentinel のエンドツーエンドの製品、ドメイン、または業界の垂直シナリオを実現します。

> [!TIP]
> 独自のニーズに合わせてすぐに使えるコンテンツをカスタマイズすることも、コミュニティ内の他のユーザーと共有するコンテンツを含む独自のソリューションを作成することもできます。 詳細については、ソリューションの作成と発行に関する [Microsoft Sentinel ソリューションのビルド ガイド](https://aka.ms/sentinelsolutionsbuildguide)を参照してください。
>
## <a name="discover-and-manage-microsoft-sentinel-content"></a>Microsoft Sentinel コンテンツを検出して管理する

Microsoft Sentinel **コンテンツ ハブ** を使用して、すぐに使える (組み込みの) コンテンツを一元的に検出してインストールします。

Microsoft Sentinel コンテンツ ハブを使用すると、Microsoft Sentinel のエンドツーエンドの製品、ドメイン、すぐに利用できる垂直のソリューションとコンテンツを、製品内で簡単に検出し、ワンステップでデプロイして、有効にすることができます。

- **コンテンツ ハブ** で、[カテゴリ](#microsoft-sentinel-out-of-the-box-content-and-solution-categories)とその他のパラメーターでフィルター処理するか、強力なテキスト検索を使用して、組織のニーズに最も適したコンテンツを見つけます。 **コンテンツ ハブ** は、コンテンツの各部分に適用される [サポート モデル](#microsoft-sentinel-out-of-the-box-content-and-solution-support-models)も示しています。一部のコンテンツは Microsoft によって管理されており、他のコンテンツはパートナーまたはコミュニティによって管理されています。

    Microsoft Sentinel **コンテンツ ハブ** を使用して [すぐに使えるコンテンツの更新](sentinel-solutions-deploy.md#install-or-update-a-solution)を管理し、 **[リポジトリ]** ページを使用してカスタム コンテンツの更新を管理します。

- 独自のニーズに合わせてすぐに使用するコンテンツをカスタマイズしたり、分析ルール、ハンティング クエリ、ノートブック、ブックなどのカスタム コンテンツを作成したりできます。 カスタム コンテンツは、[Microsoft Sentinel API](/rest/api/securityinsights/) を介して Microsoft Sentinel ワークスペースで直接管理するか、Microsoft Sentinel の [[リポジトリ]](ci-cd.md) ページを介して独自のソース管理リポジトリで管理します。

### <a name="why-content-hub-and-solutions"></a>コンテンツ ハブとソリューションを使用する理由

Microsoft Sentinel "*ソリューション*" は、1 つ以上のドメインまたは垂直のシナリオについて、エンドツーエンドの製品価値を提供するパッケージ コンテンツまたは統合です。

ソリューションのエクスペリエンスは、ソリューションの見つけやすさとデプロイのために [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) によって強化されています。 詳細については、ソリューションの作成と発行に関する [Microsoft Sentinel ソリューションのビルド ガイド](https://aka.ms/sentinelsolutionsbuildguide)を参照してください。

Microsoft Sentinel ソリューションを使用すると、Microsoft Sentinel のエンドツーエンドの製品、ドメイン、垂直のソリューションを、製品内で簡単に検出し、ワンステップでデプロイして、有効にすることができます。 ソリューションの検索、デプロイ、有効化は Azure Marketpace で、ソリューションの作成と公開は Microsoft パートナー センター で行います。

- **パッケージ化されたコンテンツ** は、データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、ウォッチリスト、パーサーなど、1 つまたは複数の Microsoft Sentinel コンテンツのコレクションです。

- **統合** には、Microsoft Sentinel または Azure Log Analytics API を使用して構築されたサービスまたはツールが含まれ、Azure と既存の顧客アプリケーション間の統合をサポートしたり、データやクエリなどをアプリケーションから Microsoft Sentinel に移行したりすることができます。

ソリューションを使用して、すぐに使えるコンテンツのパッケージを 1 回の手順でインストールすることもできます。インストール後は、多くの場合、コンテンツをすぐに使用できます。 プロバイダーとパートナーは、統合された製品、ドメイン、または垂直の値を提供することにより、ソリューションを使用して投資を行うことができます。

コンテンツ ハブを使用すると、シナリオに基づく方法でソリューションとすぐに使えるコンテンツを一元的に検出してデプロイできます。

詳細については、次を参照してください。

- [Microsoft Sentinel のすぐに使えるコンテンツとソリューションを一元的に検出してデプロイする](sentinel-solutions-deploy.md)
- [Microsoft Sentinel コンテンツ ハブ カタログ](sentinel-solutions-catalog.md)

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-categories"></a>Microsoft Sentinel のすぐに使えるコンテンツとソリューションのカテゴリ

Microsoft Sentinel のすぐに使えるコンテンツは、次の 1 つまたは複数のカテゴリで適用できます。 **コンテンツ ハブ** で、表示するカテゴリを選択して、表示されるコンテンツを変更します。

### <a name="domain-categories"></a>ドメイン カテゴリ

| カテゴリ名  | 説明 |
| ---------- | ----------------------- |
| **Application**  | Web、サーバーベース、SaaS、データベース、通信、または生産性のワークロード          |
| **クラウド プロバイダー**  | クラウド サービス|
| **コンプライアンス**   | 準拠製品、サービス、プロトコル  |
| **DevOps**       | 開発オペレーション ツールとサービス    |
| **ID**     | ID サービス プロバイダーと統合     |
| **モノのインターネット (IoT)**    | IoT、OT デバイスとインフラストラクチャ、工業制御サービス                   |
| **IT 運用**| IT 管理を行う製品とサービス   |
| **移行**    | 移行の有効化製品、サービス、および               |
| **ネットワーク**   | ネットワーク製品、サービス、ツール    |
| **プラットフォーム**     | Microsoft Sentinel の汎用またはフレームワークのコンポーネント、クラウド インフラストラクチャ、プラットフォーム|
| **セキュリティ - その他**   | その他の明確なカテゴリを持たない他のセキュリティ製品とサービス           |
| **セキュリティ - 脅威インテリジェンス**  | 脅威インテリジェンスのプラットフォーム、フィード、製品、サービス        |
| **セキュリティ - 脅威からのデータ保護**   | 脅威からの保護、電子メール保護、XDR およびエンドポイント保護製品とサービス     |
| **セキュリティ - ゼロデイの脆弱性**   | [Nobelium](../security/fundamentals/recover-from-identity-compromise.md) などのゼロデイ攻撃に特化したソリューション |
| **セキュリティ - Automation (SOAR)**   | セキュリティ自動化、SOAR (セキュリティ操作と自動化された応答)、セキュリティ操作、インシデント対応の製品とサービス。   |
| **セキュリティ - クラウド セキュリティ** | CASB (クラウド アクセス セキュリティ ブローカー)、CWPP (クラウド ワークロード保護プラットフォーム)、CSPM (クラウド セキュリティの状態管理)、その他のクラウド セキュリティ製品およびサービス |
| **セキュリティ - Azure Information Protection**   | 情報保護とドキュメント保護の製品およびサービス|
| **セキュリティ - 内部関係者による脅威**  | セキュリティ製品およびサービスの内部関係者による脅威およびユーザー/エンティティ行動分析 (UEBA)                |
| **セキュリティ - ネットワーク**    | セキュリティ ネットワーク デバイス、ファイアウォール、NDR (ネットワークの検出と応答)、NIDP (ネットワークの侵入と検出の防止)、ネットワーク パケット キャプチャ   |
| **セキュリティ - 脆弱性管理** | 脆弱性管理の製品とサービス                    |
| **Storage**      | ファイル ストアとファイル共有の製品とサービス                 |
| **トレーニングとチュートリアル**  | トレーニング、チュートリアル、オンボード資産 |
| **ユーザーの行動 (UEBA)**    | ユーザー動作分析の製品とサービス|
| | |

### <a name="industry-vertical-categories"></a>業界の垂直カテゴリ

| カテゴリ名  | 説明 |
| ---------- | ----------------------- |
| **航空**  | 航空業界に固有の製品、サービス、コンテンツ |
| **Education**    | 教育業界に固有の製品、サービス、コンテンツ   |
| **ファイナンス**      | 金融業界に固有の製品、サービス、コンテンツ     |
| **医療**   | 医療業界に固有の製品、サービス、コンテンツ  |
| **Manufacturing** | 製造業界固有の製品、サービス、コンテンツ |
| **小売**       | 小売業界に固有の製品、サービス、コンテンツ       |
| | |

## <a name="microsoft-sentinel-out-of-the-box-content-and-solution-support-models"></a>Microsoft Sentinel のすぐに使えるコンテンツおよびソリューションのサポート モデル

Microsoft と他の組織の両方が、Microsoft Sentinel のすぐに使えるコンテンツとソリューションを作成しています。 すぐに使えるコンテンツまたはソリューションの各部分には、次のいずれかのサポートの種類があります。

| サポート モデル  | 説明 |
| ---------- | ----------------------- |
| **Microsoft によるサポート**| 適用対象: <br>- Microsoft がデータ プロバイダー、関連、作成者であるコンテンツまたはソリューション。 <br> - Microsoft 以外のデータ ソース用に Microsoft が作成した一部のデータ コネクタ。 <br><br>    Microsoft では、[Microsoft Azure サポート プラン](https://azure.microsoft.com/support/options/#overview)に従って、このサポート モデルでのコンテンツまたはソリューションをサポートし、管理しています。 <br>パートナーまたはコミュニティでは、Microsoft 以外の任意のパーティーによって作成されたコンテンツまたはソリューションをサポートしています。|
|**パートナーによるサポート** | Microsoft 以外のパーティーによって作成されたコンテンツまたはソリューションに適用されます。  <br><br>   パートナー企業は、これらのコンテンツまたはソリューションのサポートまたはメンテナンスを提供しています。 パートナー企業には、独立系ソフトウェア ベンダー、マネージド サービス プロバイダー (MSP/MSSP)、システム インテグレーター (SI)、その一部のコンテンツまたはソリューションについて Microsoft Sentinel ページに連絡先情報が記載されている組織があります。<br><br>    パートナーがサポートするソリューションに関する問題については、指定されたサポートの連絡先にお問い合わせください。|
|**コミュニティによるサポート** |Microsoft または、Microsoft Sentinel にサポートとメンテナンスのための連絡先が記載されていないパートナー開発者が作成したコンテンツまたはソリューションに適用されます。<br><br>    これらのソリューションについてご質問または問題がある場合は、[Microsoft Sentinel GitHub コミュニティ](https://aka.ms/threathunters)で[問題を報告](https://github.com/Azure/Azure-Sentinel/issues/new/choose)してください。 |
| | |

## <a name="next-steps"></a>次のステップ

Microsoft Sentinel コンテンツについて学習した後、Microsoft Sentinel ワークスペースでコンテンツとソリューションの管理を開始してください。

Microsoft Sentinel **コンテンツ ハブ** からソリューションを検出してインストールします。 詳細については、次を参照してください。

- [すぐに使えるコンテンツとソリューションを一元的に検出してデプロイする (パブリック プレビュー)](sentinel-solutions-deploy.md)
- [Microsoft Sentinel コンテンツ ハブ カタログ](sentinel-solutions-catalog.md)
- [Microsoft Sentinel データ コネクタ](connect-data-sources.md)
- [Microsoft Azure Sentinel データ コネクタを見つける](data-connectors-reference.md)