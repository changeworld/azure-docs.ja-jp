
<properties 
    pageTitle="RemoteApp の VNET に関するサイズ情報"
    description="VNET で実行する RemoteApp に関する IP アドレス要件の詳細" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# RemoteApp の VNET に関するサイズ情報

RemoteApp を仮想ネットワーク \(VNET\) で使用する場合、RemoteApp はサブネット内で IP アドレスを使用します。RemoteApp サービスのスケールに基づき、RemoteApp 仮想マシンが使用できる十分な IP アドレスがサブネットにあることを確認する必要があります。サイジングに関するこのガイダンスは、RemoteApp がコレクション内の仮想マシンを動的にスピン アップ、およびスピン ダウンする際の動作まで考慮する場合は完璧とは言えませんが、サブネットの範囲を見積もるのに有効です。これは、RemoteApp サービスを VNET に配置した後でサブネット サイズを拡張するには RemoteApp を削除するしかないため、特に重要です。

最大能力で実行する RemoteApp コレクションごとに、利用可能な 100 個の IP アドレスが必要です。たとえば、スタンダード プランの 1 つの RemoteApp コレクションがあり、最大 500 ユーザーを設定する場合は、そのコレクションに対して 100 個の IP アドレスが必要です。同様に、ベーシック プランの RemoteApp コレクションに 800 ユーザーを設定する場合も、100 個の IP アドレスが必要です。設定するユーザー数が少ない \(最大値より小さい\) 場合は、コレクションごとに必要な IP アドレス数を削減できます。IP アドレス 30 個がサブネットの最小サイズ要件です \(/27\)。

<!--HONumber=52-->
