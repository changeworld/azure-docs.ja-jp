---
title: データの保存場所
description: データ所在地と Azure Arc 対応サーバーに関する情報。
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856451"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc 対応サーバー:データの保存場所

この記事では、データ所在地の概念と、それが Azure Arc 対応サーバーにどのように適用されるかについて説明します。

Azure Arc 対応サーバーは、**米国、ヨーロッパ、英国、オーストラリア、アジア太平洋**で **[使用できます](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** 。

## <a name="data-residency"></a>データの保存場所

Azure Arc 対応サーバーでは、[Azure VM 拡張機能](manage-vm-extensions.md)の構成設定 (プロパティ値) が格納されます。これらの構成設定は、接続されたマシンで有効にしようとする前に拡張機能に対して指定する必要があります。 たとえば、Log Analytics VM 拡張機能を有効にすると、Log Analytics の**ワークスペース ID** と**主キー**が要求されます。

接続されているマシンに関するメタデータ情報も収集されます。 具体的な内容は次のとおりです。

* オペレーティング システムの名前とバージョン
* コンピューター名
* コンピューターの完全修飾ドメイン名 (FQDN)
* Connected Machine エージェントのバージョン

Arc 対応サーバーでは、データが格納されるリージョンを指定できます。 Microsoft は、データの回復性を確保するために他のリージョンにレプリケートを行う場合がありますが、地理的外部のデータのレプリケートまたは移動が Microsoft によって行われることはありません。 このデータは、Azure Arc マシン リソースが構成されているリージョンに格納されます。 たとえば、マシンが米国東部リージョンの Arc に登録されている場合、このデータは米国リージョンに格納されます。

リージョンの回復性とコンプライアンス対応のサポートの詳細については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

## <a name="next-steps"></a>次の手順

[Azure の回復性](/azure/architecture/reliability/architect)の設計について詳細を確認する。