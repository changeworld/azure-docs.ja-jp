---
title: "Azure DocumentDB のファイアウォール サポートと IP アクセス制御 | Microsoft Docs"
description: "Azure DocumentDB データベース アカウントで IP アクセス制御ポリシーを使用したファイアウォールを実現する方法について説明します。"
keywords: "IP アクセス制御, ファイアウォール サポート"
services: documentdb
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 08cac64a6b08266f78bca03f1139a13e9686ebc3
ms.openlocfilehash: 819602cda932ea698287724e307ebbd73f1af988


---
# <a name="documentdb-firewall-support"></a>DocumentDB のファイアウォール サポート
Azure DocumentDB データベース アカウントに格納されているデータを保護するために、DocumentDB は、強力なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの[承認モデル](https://msdn.microsoft.com/library/azure/dn783368.aspx)を備えています。 シークレット ベースの承認モデルに加え、DocumentDB は現在、ポリシーに基づく IP ベースのアクセス制御を使った受信ファイアウォールに対応しています。 このモデルは、従来型データベース システムのファイアウォール規則によく似ていますが、DocumentDB データベース アカウントに対するセキュリティ水準をさらに引き上げることができます。 このモデルによって今後は、承認した一連のコンピューターやクラウド サービスからのみアクセスできるように DocumentDB データベース アカウントを構成することができます。 ただし承認されている一連のコンピューターやサービスから DocumentDB リソースにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

## <a name="ip-access-control-overview"></a>IP アクセス制御の概要
既定では、有効な承認トークンと共に要求が送信されれば、DocumentDB データベース アカウントにパブリック インターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式でユーザーが指定する必要があります。 この構成を適用すると、その許可リストに該当しないコンピューターを発信元とする要求はすべて、サーバーによってブロックされます。  以下の図は、IP ベースのアクセス制御における接続処理フローを表したものです。

![IP ベースのアクセス制御における接続プロセスの図](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>クラウド サービスからの接続
DocumentDB を使用した中間層のサービス ロジックをホスティングする手段として、Azure ではクラウド サービスがよく利用されます。 クラウド サービスから DocumentDB データベース アカウントにアクセスできるようにするには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことによって、ご使用の DocumentDB データベース アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。  これにより、クラウド サービスのすべてのロール インスタンスからその DocumentDB データベース アカウントにアクセスできるようになります。 クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

新たにロール インスタンスを追加してクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、DocumentDB データベース アカウントへのアクセス権が自動的に割り当てられます。

## <a name="connections-from-virtual-machines"></a>仮想マシンからの接続
DocumentDB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。  仮想マシンからのアクセスを許可するように DocumentDB データベース アカウントを構成するには、[IP アクセス制御ポリシーを構成する](#configure-ip-policy)ことによって、ご使用の DocumentDB データベース アカウントに関して許可された IP アドレスに該当するように、仮想マシンまたは仮想マシン スケール セットのパブリック IP アドレスを構成する必要があります。 仮想マシンの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

このグループに新たに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的に DocumentDB データベース アカウントへのアクセス権が割り当てられます。

## <a name="connections-from-the-internet"></a>インターネットからの接続
インターネット上のコンピューターから DocumentDB データベース アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を DocumentDB データベース アカウントの IP アドレスの許可リストに追加する必要があります。 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> IP アクセス制御ポリシーの構成
IP アクセス制御ポリシーは、[Azure CLI](documentdb-automation-resource-manager-cli.md)、[Azure Powershell](documentdb-manage-account-with-powershell.md)、または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して `ipRangeFilter` プロパティを更新することでプログラムによって設定できます。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません  (例: "13.91.6.132,13.91.6.1/24")。 これらの方法でデータベース アカウントを更新するときは、既定の設定にリセットされないように、必ずすべてのプロパティを設定してください。

> [!NOTE]
> DocumentDB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 このモデルにより、ポータルからのデータ プレーン操作の閲覧もブロックされ、アクセス制御の整合性が確保されます。

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP アクセス制御ポリシーのトラブルシューティング
### <a name="portal-operations"></a>ポータルの操作
DocumentDB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 このモデルにより、ポータルからのデータ プレーン操作の閲覧もブロックされ、アクセス制御の整合性が確保されます。 

### <a name="sdk--rest-api"></a>SDK と Rest API
許可リストに追加されていないコンピューターから SDK または REST API 経由でアクセスした場合、セキュリティ上の理由から、詳しい情報を含まない汎用的な 404 Not Found 応答が返されます。 ご使用の DocumentDB データベース アカウントに正しいポリシー構成を適用するために、そのアカウントに対して構成されている IP 許可リストを確認してください。

## <a name="next-steps"></a>次のステップ
ネットワークに関連したパフォーマンス上のヒントについては、[パフォーマンスに関するヒント](documentdb-performance-tips.md)のページを参照してください。




<!--HONumber=Feb17_HO1-->


