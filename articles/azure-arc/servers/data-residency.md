---
title: データの保存場所
description: データ所在地と Azure Arc 対応サーバーに関する情報です。
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 36aa97dc55879e61caf2b7c19aac21cae4091433
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832182"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Azure Arc 対応サーバー: データ所在地

この記事では、データ所在地の概念と、それが Azure Arc 対応サーバーにどのように適用されるかについて説明します。

Azure Arc 対応サーバーは、**米国、ヨーロッパ、英国、オーストラリア、アジア太平洋** で **[使用できます](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** 。

## <a name="data-residency"></a>データの保存場所

Azure Arc 対応サーバーでは、[Azure VM 拡張機能](manage-vm-extensions.md)の構成設定 (プロパティ値) が格納されます。これらの構成設定は、接続されたマシンで拡張機能を有効にしようとする前に、拡張機能に対して指定する必要があります。 たとえば、Log Analytics VM 拡張機能を有効にすると、Log Analytics の **ワークスペース ID** と **主キー** が要求されます。

接続されているマシンに関するメタデータ情報も収集されます。 具体的な内容は次のとおりです。

* オペレーティング システムの名前、種類、バージョン
* コンピューター名
* コンピューターの完全修飾ドメイン名 (FQDN)
* Connected Machine エージェントのバージョン
* Active Directory と DNS の完全修飾ドメイン名 (FQDN)
* UUID (BIOS ID)
* Connected Machine エージェントのハートビート
* Connected Machine エージェントのバージョン
* マネージド ID の公開キー
* ポリシーのコンプライアンスの状態と詳細 (ゲスト構成ポリシーを使用している場合)

Azure Arc 対応サーバーでは、データが格納されるリージョンを指定できます。 Microsoft は、データの回復性を確保するために他のリージョンにレプリケートを行う場合がありますが、地理的外部のデータのレプリケートまたは移動が Microsoft によって行われることはありません。 このデータは、Azure Arc マシン リソースが構成されているリージョンに格納されます。 たとえば、マシンが米国東部リージョンの Arc に登録されている場合、このデータは米国リージョンに格納されます。

> [!NOTE] 
> 東南アジアの場合、データはこのリージョンの外にはレプリケートされません。 

リージョンの回復性とコンプライアンス対応のサポートの詳細については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

## <a name="next-steps"></a>次の手順

[Azure の回復性](/azure/architecture/reliability/architect)の設計について詳細を確認する。
