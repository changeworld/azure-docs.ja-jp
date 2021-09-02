---
title: Speech サービスで仮想ネットワーク サービス エンドポイントを使用する
titleSuffix: Azure Cognitive Services
description: この記事では、Azure 仮想ネットワーク サービス エンドポイント経由で Speech サービスを使用する方法について説明します。
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490216"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>仮想ネットワーク サービス エンドポイント経由で Speech サービスを使用する

[Azure 仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) [サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)では、Azure のバックボーン ネットワーク上で最適化されたルートを介して、Azure サービスに安全に直接接続できます。 エンドポイントを使用することで、重要な Azure サービスのリソースを仮想ネットワークのみに限定してセキュリティで保護することができます。 サービス エンドポイントを使用すると、仮想ネットワーク上のパブリック IP アドレスを必要とせずに、仮想ネットワーク内のプライベート IP アドレスを Azure サービスのエンドポイントに接続できます。

この記事では、Azure Cognitive Services の Speech サービスで仮想ネットワーク サービス エンドポイントを設定して使用する方法について説明します。

> [!NOTE]
> 始める前に、[Cognitive Services で仮想ネットワークを使用する方法](../cognitive-services-virtual-networks.md)に関する記事を確認してください。

この記事では、[後で仮想ネットワーク サービス エンドポイントを削除しても引き続き Speech リソースを使用する方法](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks)についても説明します。

仮想ネットワーク サービス エンドポイントのシナリオ用に Speech リソースを設定するには、次のことを行う必要があります。
1. [Speech リソース のカスタム ドメイン名を作成します](#create-a-custom-domain-name)。
1. [Speech リソース の仮想ネットワークとネットワーク設定を構成します](#configure-virtual-networks-and-the-speech-resource-networking-settings)。
1. [既存のアプリケーションとソリューションを調整します](#adjust-existing-applications-and-solutions)。

> [!NOTE]
> Speech サービスのための仮想ネットワーク サービス エンドポイントの設定と使用は、プライベート エンドポイントの設定と使用に似ています。 この記事では、手順が同じ場合、[プライベート エンドポイントの使用に関する記事](speech-services-private-link.md)の対応するセクションを参照します。

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

この記事では、Speech サービスで仮想ネットワーク サービス エンドポイントを使用する方法について説明します。 プライベート エンドポイントの詳細については、「[プライベート エンドポイント経由で Speech サービスを使用する](speech-services-private-link.md)」を参照してください。

## <a name="create-a-custom-domain-name"></a>カスタム ドメイン名を作成する

仮想ネットワーク サービス エンドポイントを使用するには、[Cognitive Services のカスタム サブドメイン名](../cognitive-services-custom-subdomains.md)が必要です。 プライベート エンドポイントに関する記事の[ガイダンス](speech-services-private-link.md#create-a-custom-domain-name)に従って、カスタム ドメインを作成します。 また、このセクションのすべての警告は、仮想ネットワーク サービス エンドポイントにも適用されます。

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>仮想ネットワークと Speech リソースのネットワーク設定を構成する

サービス エンドポイント経由のアクセスが許可されているすべての仮想ネットワークを Speech リソースのネットワーク プロパティに追加する必要があります。

> [!NOTE]
> 仮想ネットワーク サービス エンドポイント経由で Speech リソースにアクセスするには、仮想ネットワークの必要なサブネットに対して `Microsoft.CognitiveServices` サービス エンドポイントの種類を有効にする必要があります。 これにより、Cognitive Services に関連するすべてのサブネットのトラフィックがプライベート バックボーン ネットワーク経由でルーティングされます。 同じサブネットから他の Cognitive Services リソースにアクセスする計画の場合は、これらのリソースが仮想ネットワークを許可するように構成されていることを確認します。 
>
> Speech リソースのネットワーク プロパティで "*許可されている*" 仮想ネットワークが追加されていない場合、`Microsoft.CognitiveServices` サービス エンドポイントが仮想ネットワークに対して有効になっている場合でも、サービス エンドポイントを介して Speech リソースにアクセスすることはできません。 また、サービス エンドポイントが有効になっているが仮想ネットワークが許可されていない場合、Speech リソースの他のネットワーク セキュリティ設定に関係なく、Speech リソースでパブリック IP アドレスを使用して仮想ネットワークにアクセスすることはできません。 これは、`Microsoft.CognitiveServices` エンドポイントを有効にすると、Cognitive Services に関連するトラフィックはすべてプライベート バックボーン ネットワーク経由でルーティングされ、この場合は仮想ネットワークにリソースへのアクセスを明示的に許可する必要があるためです。 このガイダンスは、Speech リソースだけでなく、Cognitive Services のすべてのリソースに適用されます。  
  
1. [Azure portal](https://portal.azure.com/) に移動し、ご自分の Azure アカウントにサインインします。
1. Speech リソースを選択します。
1. 左側のペインの **[リソース管理]** グループで、 **[ネットワーク]** を選択します。
1. **[ファイアウォールと仮想ネットワーク]** タブで、 **[選択したネットワークとプライベート エンドポイント]** を選択します。 

   > [!NOTE]
   > 仮想ネットワーク サービス エンドポイントを使用するには、 **[選択したネットワークとプライベート エンドポイント]** ネットワーク セキュリティ オプションを選択する必要があります。 他のオプションはサポートされていません。 シナリオで **[すべてのネットワーク]** オプションを選択する必要がある場合は、3 つのネットワーク セキュリティ オプションすべてをサポートする[プライベート エンドポイント](speech-services-private-link.md)を使用することを検討してください。

5. **[既存の仮想ネットワークの追加]** または **[新しい仮想ネットワークの追加]** を選択し、必要なパラメーターを指定します。 既存の仮想ネットワークの場合は **[追加]** を選択し、新しい仮想ネットワークの場合は **[作成]** を選択します。 既存の仮想ネットワークを追加すると、選択したサブネットに対して `Microsoft.CognitiveServices` サービス エンドポイントが自動的に有効になります。 この操作は最大 15 分かかることがあります。 このセクションの冒頭にある注意を参照してください。

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>既存の仮想ネットワークのサービス エンドポイントを有効にする 

前のセクションで説明したように、Speech リソースに対して "*許可されている*" 仮想ネットワークを構成すると、`Microsoft.CognitiveServices` サービス エンドポイントが自動的に有効になります。 後で無効にした場合は、手動で再び有効にして、Speech リソース (および他の Cognitive Services リソース) へのサービス エンドポイントのアクセスを復元する必要があります。

1. [Azure portal](https://portal.azure.com/) に移動し、ご自分の Azure アカウントにサインインします。
1. 仮想ネットワークを選択します。
1. 左側のペインの **[設定]** グループで、 **[サブネット]** を選択します。
1. 必要なサブネットを選択します。
1. ウィンドウの右側に新しいパネルが表示されます。 このパネルの **[サービス エンドポイント]** セクションの `Microsoft.CognitiveServices`[サービス]**の一覧で** を選択します。
1. **[保存]** を選択します。

## <a name="adjust-existing-applications-and-solutions"></a>既存のアプリケーションとソリューションを調整する

カスタム ドメインが有効にされた Speech リソースでは、別の方法で Speech サービスとやりとりします。 これは、サービス エンドポイントが構成されているかどうかに関係なく、カスタム ドメイン対応の Speech リソースに当てはまります。 このセクションの情報は、両方のシナリオに該当します。

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>カスタム ドメイン名を持ち、仮想ネットワークが許可されている Speech リソースを使用する 

このシナリオでは、Speech リソースのネットワーク設定で **[選択されたネットワークとプライベート エンドポイント]** オプションが選択され、少なくとも 1 つの仮想ネットワークが許可されます。 このシナリオは、[カスタム ドメイン名とプライベート エンドポイントが有効になっている Speech リソースを使用する](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint)場合と同じです。


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>カスタム ドメイン名を持つが仮想ネットワークが許可されていない Speech リソースを使用する

このシナリオでは、プライベート エンドポイントは有効にされておらず、次のいずれかの状況に該当します。

- Speech リソースのネットワーク設定で **[選択されたネットワークとプライベート エンドポイント]** オプションが選択されているが、許可されている仮想ネットワークが構成されていない。
- Speech リソースのネットワーク設定で **[すべてのネットワーク]** オプションが選択されている。

このシナリオは、[カスタム ドメイン名を持つがプライベート エンドポイントを持たない Speech リソースを使用する](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints)場合と同じです。


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>詳細情報

* [プライベート エンドポイントを経由したインデクサー接続](speech-services-private-link.md)
* [Azure 仮想ネットワーク サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Speech to Text REST API](rest-speech-to-text.md)
* [Text to Speech REST API](rest-text-to-speech.md)
