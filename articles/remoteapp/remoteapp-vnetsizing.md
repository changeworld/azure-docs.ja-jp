
<properties
    pageTitle="Azure RemoteApp の VNET に関するサイズ情報 | Microsoft Azure"
    description="VNET で実行する Azure RemoteApp の IP アドレス要件について説明します。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="elizapo" />



# Azure RemoteApp の VNET に関するサイズ情報

Azure RemoteApp を仮想ネットワーク (VNET) で使用する場合、RemoteApp はサブネット内で IP アドレスを使用します。RemoteApp サービスのスケールに基づき、RemoteApp 仮想マシンが使用できる十分な IP アドレスがサブネットにあることを確認する必要があります。サイジングに関するこのガイダンスは、RemoteApp がコレクション内の仮想マシンを動的にスピン アップ、およびスピン ダウンする際の動作まで考慮する場合は完璧とは言えませんが、サブネットの範囲を見積もるのに有効です。これは、RemoteApp サービスを VNET に配置した後でサブネット サイズを拡張するには RemoteApp を削除するしかないため、特に重要です。

最大能力で実行する RemoteApp コレクションごとに、利用可能な 100 個の IP アドレスが必要です。たとえば、Standard プランの 1 つの RemoteApp コレクションがあり、最大 500 ユーザーを設定する場合は、そのコレクションに対して 100 個の IP アドレスが必要です。同様に、Basic プランの RemoteApp コレクションに 800 ユーザーを設定する場合も、100 個の IP アドレスが必要です。設定するユーザー数が少ない (最大値より小さい) 場合は、コレクションごとに必要な IP アドレス数を削減できます。IP アドレス 30 個がサブネットの最小サイズ要件です (/27)。

次の情報を参照して、VNET が正しく構成されて動作していることを確認してください。

- [個人の VNET から Azure VNET への切り替え](remoteapp-migratevnet.md)
- [Azure RemoteApp で使用する Azure VNET の検証](remoteapp-vnet.md)

<!---HONumber=AcomDC_0330_2016-->