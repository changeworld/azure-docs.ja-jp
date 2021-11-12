---
title: Azure Healthcare APIs とは何ですか?
description: この記事では、Azure Healthcare API の概要について説明します。
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: f243f43e50c4739cda36ee8d2374772df436d9f7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370386"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Azure Healthcare API (プレビュー) とは

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

Azure Healthcare API は、オープンな標準とフレームワークに基づく一連のマネージド API サービスであり、ワークフローで医療を改善し、スケーラブルで安全な医療ソリューションを提供できます。 医療業界専用の一連のマネージド API サービスとフレームワークを使用することが重要であり、有益です。これは、患者と医療コンシューマーから収集された医療データが、複数のシステム、デバイスの種類、データ形式にまたがって断片化される可能性があるためです。 健康データから分析情報を得るのは、人口と個人の健康と全体的な理解を維持するための最大の障壁の 1 つです。 現在、システム、ワークフロー、および正常性データを一緒にまとめる方が重要です。 正常性データへのアクセス、標準化、傾向のキャプチャに対する統合および一致したアプローチにより、運用および臨床の分析情報を検出できます。 新しいデバイス アプリケーションを接続するプロセスを効率化し、新しい研究プロジェクトを有効にできます。 Azure Healthcare API をスケーラブルで安全な医療ソリューションとして使用すると、保護された正常性情報 (PHI) データセットをまとめて機械学習、分析、AI 用のツールでエンドツーエンドに接続することで検出された分析情報を通じて、ワークフローで医療を改善できます。 

Azure Healthcare API には、次の利点があります。
* データを一貫した方法で 1 か所で収集し、アクセス可能にすることで、新しいワークロードが PHI を活用できます。
* 機械学習、分析、AI 用のツールを使用して、一部の PHI を組み合わせてエンドツーエンドで接続することで、新しい分析情報を発見します。
* 保護された正常性情報の管理、保存、および利用可能な方法に自信を持って信頼できるクラウド上に構築します。
新しい Microsoft Azure Healthcare API は、FHIR に加えて、DICOM などの他の医療業界のデータ標準をサポートし、医療データの相互運用性を拡張します。 ビジネス モデルとインフラストラクチャ プラットフォームは、さまざまな医療データ標準と将来の医療データ標準の拡張と導入に対応するように再設計されました。 お客様は、同じコンプライアンスの下で、医療標準全体でさまざまな種類の医療データを使用できます。 マネージド サービスには、レガシ形式またはデバイス独自の形式から FHIR にデータを変換できるツールが組み込まれています。 これらのツールの一部は、以前に開発され、オープンソース化されています。その他は新規です。

Azure Healthcare APIs を使用すると、以下を行うことができます。 
* 構造化、画像、デバイスのデータなど、さまざまな健康データ ソースと形式をすばやく関連付け、クラウド内で永続化するために正規化します。
* データを変換して FHIR に取り込みます。 たとえば、健康データを、HL7v2 や CDA などのレガシ形式から、またはデバイス独自の形式の高頻度 IoT データから、FHIR に変換することができます。
* Healthcare APIs に格納されているデータを、Azure エコシステム全体のサービス (Synapse など) や Microsoft 全体の製品 (Teams など) に接続し、分析と機械学習を通じて新しい分析情報を導き出し、新しいワークフローと、SMART on FHIR アプリケーションへの接続を可能にします。
* 信頼性、スケーラビリティ、セキュリティを提供するエンタープライズ機能を使用して高度なワークロードを管理し、データが確実に保護され、医療業界で求められるプライバシーとコンプライアンスの認定を満たすようにします。


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Azure Healthcare API と Azure Healthcare API の主な違いは何Azure API for FHIR。

**リンクされたサービス**

Azure Healthcare API では、構造化データを交換する複数の正常性データ標準がサポートされます。 Azure Healthcare API の 1 つのコレクションを使用すると、異なるサービスの種類 (FHIR サービス、DICOM サービス、および IoT Connector) の複数のインスタンスをデプロイして、シームレスに連携することができます。

**DICOM サービスの紹介**

Azure Healthcare API に DICOM サービスのサポートが含まれる。 DICOM を使用すると、イメージ データとその関連メタデータを安全に交換できます。 DICOM は、医療画像情報の送信、保存、取得、印刷、処理、表示を行う国際標準であり、医療全体で受け入れられる主要な医療画像標準です。 DICOM サービスの詳細については、「DICOM の概要 [」を参照してください](./dicom/dicom-services-overview.md)。

**FHIR サービスの増分変更**

FHIR データの安全な交換のために、医療 API には、アプリケーションで使用できないいくつかの増分機能Azure API for FHIR。 
* トランザクションのサポート: 医療 API では、FHIR サービスはトランザクション バンドルをサポートします。 トランザクション バンドルの詳細については、トランザクションの詳細に関するページ HL7.org、バッチとトランザクションの相互作用に関するページを参照してください。
* チェーン検索の機能強化: チェーン検索&予約チェーン検索は、サブクエリごとに 100 項目に制限されなくなりました。


## <a name="next-steps"></a>次の手順

Azure Healthcare API の使用を開始するには、5 分間のクイック スタートに従ってワークスペースをデプロイします。

> [!div class="nextstepaction"]
> [ワークスペースをワークスペースにデプロイAzure portal](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [ワークスペースの概要](workspace-overview.md)
