---
title: Azure Healthcare APIs での FHIR サービスに関する FAQ
description: FHIR APIs におけるデータの保存場所やバージョン サポートなど、FHIR サービスについてよく寄せられる質問とその回答を示します。
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 7e36e91125e721d8ce5ed4dbc2603109838ca531
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780013"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>FHIR サービスに関してよく寄せられる質問

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このセクションでは、Azure Healthcare API FHIR サービス (ここでは FHIR サービスと呼ばれる) についてよく寄せられる質問について説明します。

## <a name="fhir-service-the-basics"></a>FHIR サービス: 基本

### <a name="what-is-fhir"></a>FHIR とは何でしょうか?

高速ヘルスケア相互運用性リソース (Fast Healthcare Interoperability Resources) は、略して FHIR ("ファイア" と発音) と呼ばれ、さまざまな医療システム間で医療データの交換を可能にする相互運用性標準です。 この標準は HL7 組織によって策定され、世界各国の医療機関で採用されています。 FHIR の最新バージョンは R4 (Release 4) です。 FHIR サービスでは R4 がサポートされるほか、以前のバージョンの STU3 (Standard for Trial Use 3) もサポートされています。 FHIR の詳細については、[HL7.org](http://hl7.org/fhir/summary.html) のページを参照してください。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API のデータは Azure に格納されるのですか?

はい。データは Azure のマネージド データベースに格納されます。 Azure Healthcare APIs の FHIR サービスでは、基になるデータ ストアへの直接アクセスは提供されていません。

### <a name="what-identity-provider-do-you-support"></a>どの ID プロバイダーがサポートされますか?

現在当社は、ID プロバイダーとして Microsoft Azure Active Directory をサポートしています。

### <a name="what-fhir-version-do-you-support"></a>どのバージョンの FHIR がサポートされますか?

バージョン 4.0.0 と 3.0.1 がサポートされています。

詳細については、「[サポートされている機能](fhir-features-supported.md)」を参照してください。 FHIR バージョン間 (つまり STU3 から R4) の変更点については、[HL7 FHIR のバージョン履歴](https://hl7.org/fhir/R4/history.html)に関するページを参照してください。

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>Azure API for FHIR と、Healthcare APIs の FHIR サービスの違いは何ですか?

FHIR サービスは、Azure Healthcare APIs に含まれる FHIR 仕様の実装であり、1 つのワークスペースに FHIR サービスと DICOM サービスを含めることができます。 Azure API for FHIR は、最初の GA 製品であり、スタンドアロン製品としてまだ利用できます。 機能の主な違いは次のとおりです。

* FHIR サービスは、上限が 4 TB で、パブリック プレビュー段階です。一方、Azure API for FHIR は、4 TB を超える量をサポートしており、GA になっています。
* FHIR サービスでは、[トランザクション バンドル](https://www.hl7.org/fhir/http.html#transaction)がサポートされています。
* Azure API for FHIR には、Azure Healthcare APIs の FHIR サービスでまだ利用できない多くのプラットフォーム機能 (プライベート リンク、カスタマー マネージド キー、ログ記録など) があります。 これらの機能の詳細については、GA で提供されます。

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>"Azure Healthcare APIs での FHIR サービス" とオープンソースの "FHIR サーバー" の違いは何ですか?

Azure Healthcare APIs での FHIR サービスは、オープンソースの Microsoft FHIR Server for Azure のホストされたマネージド バージョンです。 マネージド サービスでは、メンテナンスや更新プログラムがすべて Microsoft によって提供されます。

FHIR Server for Azure を実行するときに、基になるサービスに直接アクセスできますが、サーバーのメンテナンスや更新、PHI データを格納する場合はすべての必要なコンプライアンス作業も担当します。

### <a name="in-which-regions-is-the-fhir-service-available"></a>FHIR サービスは、どのリージョンで利用できますか?

お客様の要求に基づいて、Healthcare APIs のグローバルなフットプリントは継続的に拡張されています。 現在、FHIR サービスは、2 つの米国政府機関リージョンを含む 25 のリージョンで利用できます: オーストラリア東部、ブラジル南部 *、カナダ中部、インド中部*、東アジア *、米国東部 2、米国東部、ドイツ北部 **、ドイツ中西部、ドイツ北部**、東日本、西日本*、韓国中部*、米国中北部、北ヨーロッパ、南アフリカ北部、米国中南部、東南アジア、スイス北部、英国南部、英国西部、米国中西部、西ヨーロッパ、米国西部 2、USGov バージニア、USGov アリゾナ。

\* は、プライベート プレビュー リージョンを示します。** は、ディザスター リカバリー リージョンのみを示します。

詳細については、[geo リージョンのサポート](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all)に関するページを参照してください。

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>FHIR サービスにリリースされる内容はどこで確認できますか?

FHIR サービスにリリースされている内容の一部については、オープンソース FHIR サーバーの[リリース](https://github.com/microsoft/fhir-server/releases)を参照してください。 項目を管理サービスにリリースし、通常、オープンソースのリリース ページにそれらが掲載された 2 週間後に利用できるようにする場合に、FHIR-Service でそれらにタグ付けするようにしました。 また、独自の環境でテストする場合に、[ここに](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md)ビルドをテストする方法に関する手順を掲載しました。 追加の管理サービスの更新を共有するための最適な方法を評価しています。

### <a name="what-is-smart-on-fhir"></a>SMART on FHIR とは何でしょうか?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR は、FHIR サーバーやその他の医療 IT システム (電子カルテ、医療情報交換など) にパートナー アプリケーションを統合するための一連のオープン仕様です。 SMART on FHIR アプリケーションを作成することにより、多数の異なるシステムから確実にそのアプリケーションにアクセスして活用することができます。
SMART の詳細については、[SMART Health IT](https://smarthealthit.org/) に関するページを参照してください。

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>データベースで実行されている FHIR のバージョンは、どこで確認できますか?

機能ステートメントで公開されている正確な FHIR のバージョンは、"fhirVersion" プロパティ (FHIR URL/メタデータ) で確認できます

## <a name="fhir-implementations-and-specifications"></a>FHIR の実装と仕様

### <a name="can-i-create-a-custom-fhir-resource"></a>カスタム FHIR リソースを作成できますか?

カスタム FHIR リソースは使用できません。 カスタム FHIR リソースが必要な場合、拡張機能を使用して[基本的なリソース](http://www.hl7.org/fhir/basic.html)の上にカスタム リソースを構築できます。 

### <a name="are-extensions-supported-on-the-fhir-service"></a>[拡張機能](https://www.hl7.org/fhir/extensibility.html)は、FHIR サービスでサポートされていますか?

有効な FHIR JSON データをサーバーに読み込むことができます。 拡張機能を定義する構造定義を格納する場合は、これを構造定義リソースとして保存できます。 拡張機能を検索するには、[独自の検索パラメーターを定義する](how-to-do-custom-search.md)必要があります。 

### <a name="what-is-the-limit-on-_count"></a>_count にはどのような制限がありますか?

_count の現在の上限は 1000 です。 _count を 1000 より大きい値に設定すると、バンドルに 1000 レコードのみが表示されるという警告が表示されます。

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>FHIR サービスにバンドルを投稿することはできますか?

現在、FHIR サービスでは、[バッチ バンドル](https://www.hl7.org/fhir/valueset-bundle-type.html)の投稿と、[トランザクション バンドル](https://www.hl7.org/fhir/http.html#transaction)の投稿がサポートされています。

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>FHIR サービスで 1 人の患者のすべてのリソースを取得するにはどうすればよいですか?

1 人の患者に関連するすべてのデータを取得する [$patient-everything 操作](patient-everything.md)がサポートされています。 

## <a name="using-the-fhir-service"></a>FHIR サービスの使用

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>ワークフロー内での FHIR サービスの使用例は、どこで確認できますか?

[正常性アーキテクチャ GitHub ページ](https://github.com/microsoft/health-architectures)に使用可能な参照アーキテクチャのコレクションがあります。

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>Web アプリケーションを FHIR サービスに接続する例は、どこで確認できますか?

アプリケーションとシナリオの例を含む、[正常性アーキテクチャの GitHub ページ](https://aka.ms/health-architectures)があります。 そこでは、Web アプリケーションを FHIR サービスに接続する方法が示されています。
