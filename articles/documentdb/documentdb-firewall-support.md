---
title: "DocumentDB のファイアウォール サポート | Microsoft Docs"
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
ms.date: 10/17/2016
ms.author: ankshah; kraman
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb3c5c2adbaedc4bfb1e68f26b88079aeabe50f5


---
# <a name="documentdb-firewall-support"></a>DocumentDB のファイアウォール サポート
Azure DocumentDB データベース アカウントに格納されているデータを保護するために、DocumentDB は、強力なハッシュベースのメッセージ認証コード (HMAC) を利用したシークレット ベースの[承認モデル](https://msdn.microsoft.com/library/azure/dn783368.aspx)を備えています。 シークレット ベースの承認モデルに加え、DocumentDB は現在、ポリシーに基づく IP ベースのアクセス制御を使った受信ファイアウォールに対応しています。 このモデルは、従来型データベース システムのファイアウォール規則によく似ていますが、DocumentDB データベース アカウントに対するセキュリティ水準をさらに引き上げることができます。 このモデルによって今後は、承認した一連のコンピューターやクラウド サービスからのみアクセスできるように DocumentDB データベース アカウントを構成することができます。 ただし承認されている一連のコンピューターやサービスから DocumentDB リソースにアクセスするためには、呼び出し側が有効な承認トークンを提示する必要がある点は変わりません。

## <a name="ip-access-control-overview"></a>IP アクセス制御の概要
既定では、有効な承認トークンと共に要求が送信されれば、DocumentDB データベース アカウントにパブリック インターネットからアクセスすることができます。 IP ポリシー ベースのアクセス制御を構成するためには、特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式でユーザーが指定する必要があります。 この構成を適用すると、その許可リストに該当しないコンピューターを発信元とする要求はすべて、サーバーによってブロックされます。  以下の図は、IP ベースのアクセス制御における接続処理フローを表したものです。

![IP ベースのアクセス制御における接続プロセスの図](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>クラウド サービスからの接続
DocumentDB を使用した中間層のサービス ロジックをホスティングする手段として、Azure ではクラウド サービスがよく利用されます。 クラウド サービスから DocumentDB データベース アカウントにアクセスできるようにするには、[Azure サポート](#configure-ip-policy)に連絡して、ご使用の DocumentDB データベース アカウントに関連付けられている IP アドレスの許可リストにクラウド サービスのパブリック IP アドレスを追加する必要があります。  これにより、クラウド サービスのすべてのロール インスタンスからその DocumentDB データベース アカウントにアクセスできるようになります。 クラウド サービスの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。 

![Azure Portal に表示されるクラウド サービスのパブリック IP アドレスを示すスクリーンショット](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

新たにロール インスタンスを追加してクラウド サービスをスケールアウトした場合、それらのインスタンスは、同じクラウド サービスに属すことになるので、DocumentDB データベース アカウントへのアクセス権が自動的に割り当てられます。

## <a name="connections-from-virtual-machines"></a>仮想マシンからの接続
DocumentDB を使用する中間層サービスのホスティングには、[仮想マシン](https://azure.microsoft.com/services/virtual-machines/)または[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使用することもできます。  仮想マシンからのアクセスを許可するように DocumentDB データベース アカウントを構成するには、[Azure サポートに連絡](#configure-ip-policy)して、ご使用の DocumentDB データベース アカウントに関して許可された IP アドレスに該当するように、仮想マシンまたは仮想マシン スケール セットのパブリック IP アドレスを構成する必要があります。 仮想マシンの IP アドレスは Azure Portal で確認できます。次のスクリーンショットを参照してください。

![Azure Portal に表示される仮想マシンのパブリック IP アドレスを示すスクリーンショット](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

このグループに新たに仮想マシン インスタンスを追加した場合、それらのインスタンスにも自動的に DocumentDB データベース アカウントへのアクセス権が割り当てられます。

## <a name="connections-from-the-internet"></a>インターネットからの接続
インターネット上のコンピューターから DocumentDB データベース アカウントにアクセスするときは、そのコンピューターのクライアント IP アドレスまたは IP アドレス範囲を DocumentDB データベース アカウントの IP アドレスの許可リストに追加する必要があります。 

## <a name="a-idconfigure-ip-policya-configuring-the-ip-access-control-policy"></a><a id="configure-ip-policy"></a> IP アクセス制御ポリシーの構成
ご使用のデータベース アカウントで IP アクセス制御ポリシーを有効にするためのリクエストは、Azure Portal から [Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に申請します。

1. [[ヘルプとサポート]](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ブレードで **[新しいサポート要求]** を選択します。
2. **[新しいサポート要求]** ブレードで、**[基本]** を選択します。
3. **[基本]** ブレードで次のように選択します。
   * **問題の種類**: クォータ
   * **[サブスクリプション]**: IP アクセス制御ポリシーの追加先となるアカウントに関連付けられているサブスクリプション。
   * **[クォータの種類]**: DocumentDB
   * **[サポート プラン]**: [Quota Support - Included (クォータのサポート - 含む)]
4. **[問題]** ブレードで、次の手順を実行します。
   * **[重大度]**: [C - 最小限の影響] を選択します。
   * **[詳細]**: 以下のテキストをボックスにコピーし、アカウント名と IP アドレスを実際の値に置き換えます: "I would like to enable firewall support for my DocumentDB database account. Database account: *Include account name/s*. Allowed IP address/Ranges: *Include IP address/range in CIDR format, for example 13.91.6.132, 13.91.6.1/24*." ("DocumentDB データベース アカウントのファイアウォール サポートを有効にしたいと考えています。データベース アカウント: <アカウント名>。許可する IP アドレス/範囲: <13.91.6.132, 13.91.6.1/24 のような CIDR 形式での IP アドレス/範囲>。")
   * ページの下部にある [次へ]」を参照してください。 
5. ご自身の連絡先情報を **[連絡先情報]** ブレードに入力し、**[作成]** をクリックします。 

通常、申請が受理されてから 24 時間以内に IP アクセス制御が有効になります。 申請が完了すると、通知が送信されます。

![[ヘルプとサポート] ブレードのスクリーンショット](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![[問題] ブレードのスクリーンショット](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>IP アクセス制御ポリシーのトラブルシューティング
### <a name="portal-operations"></a>ポータルの操作
DocumentDB データベース アカウントの IP アクセス制御ポリシーを有効にすると、構成されている許可リストの IP アドレス範囲に該当しないコンピューターからのアクセスがすべてブロックされます。 このモデルにより、ポータルからのデータ プレーン操作の閲覧もブロックされ、アクセス制御の整合性が確保されます。 

### <a name="sdk-rest-api"></a>SDK と Rest API
許可リストに追加されていないコンピューターから SDK または REST API 経由でアクセスした場合、セキュリティ上の理由から、詳しい情報を含まない汎用的な 404 Not Found 応答が返されます。 ご使用の DocumentDB データベース アカウントに正しいポリシー構成を適用するために、そのアカウントに対して構成されている IP 許可リストを確認してください。

## <a name="next-steps"></a>次のステップ
ネットワークに関連したパフォーマンス上のヒントについては、[パフォーマンスに関するヒント](documentdb-performance-tips.md)のページを参照してください。




<!--HONumber=Nov16_HO3-->


