---
title: Microsoft Azure Data Box Disk の概要 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるクラウド ソリューションである Azure Data Box Disk について説明します。
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2018
ms.author: alkohli
ms.openlocfilehash: ecdf604cf15ec68875b67f2a4c990103b3375243
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009741"
---
# <a name="azure-data-box-disk-security-and-data-protection-preview"></a>Azure Data Box Disk のセキュリティとデータ保護 (プレビュー)

この記事では、Azure Data Box Disk のセキュリティに注目し、Azure Data Box ソリューションの各コンポーネントとそこに格納されるデータを保護する働きを持った機能について説明します。 

> [!IMPORTANT]
> Data Box Disk はプレビュー段階にあります。 このソリューションを展開する前に、[プレビューに関する Azure のサービス利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。

## <a name="data-flow-through-components"></a>各コンポーネントを通るデータのフロー

Microsoft Azure Data Box ソリューションは、互いに連携し合う 4 つのメイン コンポーネントで構成されています。

- **Azure でホストされる Azure Data Box サービス** - ディスクを発注して構成し、その注文を最後まで追跡するための管理サービス。
- **Data Box Disk** - オンプレミスのデータを Azure にインポートするためにお客様に発送される物理ディスク。 
- **ディスクに接続されるクライアント/ホスト** – お客様のインフラストラクチャにおいて保護の対象となるデータを格納しているクライアント。USB 経由で Data Box Disk に接続されます。
- **クラウド ストレージ** – Azure クラウド内のデータの保存先となる場所。 通常は、お客様が作成した Azure Data Box リソースにリンクされたストレージ アカウントです。

次の図は、Azure Data Box Disk ソリューションを通じてオンプレミスから Azure に向かうデータのフローを示します。

![Data Box Disk のセキュリティ](media/data-box-disk-security/data-box-disk-security-1.png)

## <a name="security-features"></a>セキュリティ機能

Data Box Disk は、承認済みのエンティティ以外データの閲覧、変更、削除ができないよう徹底することによって、データ保護のための安全なソリューションを実現しています。 このソリューションのセキュリティ機能は、ディスクおよび関連するサービスを対象としたものであり、そこに格納されるデータのセキュリティを確保するものです。 

### <a name="data-box-disk-protection"></a>Data Box Disk の保護

Data Box Disk は、次の機能によって保護されます。

- ディスクは、BitLocker の AES-128 ビット方式によって常時暗号化されます。
- 安全なディスク更新機能が使用されます。
- ディスクはロックされた状態で出荷され、ロックの解除は Data Box Disk のロック解除ツールでしか行えません。 ロック解除ツールは Data Box Disk のサービス ポータルで利用できます。

### <a name="data-box-disk-data-protection"></a>Data Box Disk のデータ保護

Data Box Disk に対する入出力データは、次の機能によって保護されます。

- データは常時 BitLocker で暗号化されます。 
- Azure へのアップロードが完了したデータは、ディスクから確実に消去されます。 データの消去は、NIST 800-88r1 標準に準拠しています。

### <a name="data-box-service-protection"></a>Data Box サービスの保護

Data Box サービスは、次の機能によって保護されます。

- Data Box Disk サービスにアクセスするには、Data Box Disk を含む Azure サブスクリプションを会社が保有している必要があります。 Azure ポータルから利用できる機能は、保有するサブスクリプションによって決まります。
- Data Box サービスは Azure でホストされるため、Azure のセキュリティ機能によって保護されます。 Microsoft Azure のセキュリティ機能の詳細については、「 [Microsoft Azure セキュリティ センター](https://www.microsoft.com/TrustCenter/Security/default.aspx)」をご覧ください。 
- このサービスでは、ディスクのロック解除にパスキーが使用されます。ディスクのパスキーは、Data Box Disk に格納されます。 
- Data Box Disk サービスは、その注文の詳細と状態を格納します。 この情報は、注文が削除されると削除されます。 


## <a name="managing-personal-data"></a>個人データの管理

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Azure Data Box Disk は、同サービスにおける次の主要なインスタンスに個人情報を収集して表示します。

- **通知設定** - お客様は、発注の際、ユーザーのメール アドレスを通知設定で構成することになります。 この情報は管理者が閲覧できます。 この情報は、ジョブが最終状態に達するか、お客様が注文を削除すると、サービスによって削除されます。

- **注文の詳細** - 発注後、ユーザーの配送先住所、メール、連絡先情報が Azure portal に格納されます。 たとえば、次の情報が保存の対象となります。

    - 連絡先の名前
    - 電話番号
    - 電子メール
    - 番地
    - City
    - 郵便番号
    - State
    - 国/地方/地域
    - ドライブ ID
    - 運送業者のアカウント番号
    - 配送追跡番号

    注文の詳細は、ジョブが完了するか、お客様が注文を削除すると、Data Box サービスによって削除されます。

- **配送先住所** - Data Box サービスは、注文を受けると、サード パーティの運送業者 (UPS、DHL など) に配送先住所を提供します。 

詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。


## <a name="next-steps"></a>次の手順

- [Data Box Disk の要件](data-box-disk-system-requirements.md)を確認する。
- [Data Box Disk の制限](data-box-disk-limits.md)を理解する。
- Azure portal で [Azure Data Box Disk](data-box-disk-quickstart-portal.md) を迅速に展開する。
