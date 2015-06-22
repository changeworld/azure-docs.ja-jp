
<properties 
    pageTitle="ハイブリッド コレクションの作成のトラブルシューティング"
    description="RemoteApp のハイブリッド コレクション作成でエラーが発生したときのトラブルシューティング方法について説明します。" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# ハイブリッド コレクションの作成のトラブルシューティング

ハイブリッド コレクション作成時のエラーのトラブルシューティングを行う前に、ハイブリッド コレクションの作成方法を理解しておくことが有用です。ハイブリッド コレクションでは、RemoteApp インスタンスのドメインへの参加が必要です。これは、コレクションの作成時に行います。コレクションの作成プロセスが開始されると、アップロードしたテンプレート イメージのコピーが VNET 内に作成され、VNET 作成時に指定された DNS IP レコードによって解決されたドメインへのサイト間 VPN トンネルを使用してドメインに参加します。

Azure 管理ポータルでの一般的なエラー:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

上のエラーのいずれかが表示される場合は、次のことを確認してください。

- DNS IP 構成が有効であることの確認
- DNS IP レコードが、パブリック IP レコードであるか、または VNET 作成時に指定した "ローカル アドレス空間" の一部であることの確認
- VNET トンネルがアクティブまたは接続状態であることの確認 
- VPN 接続のオンプレミス側がネットワーク トラフィックをブロックしていないことを確認します。これは、ローカルの VPN デバイスかソフトウェアのログで確認できます。
- コレクションの作成時に指定したドメインが稼働していることを確認します。

	ProvisioningTimeout


このエラーが表示される場合は、次のことを確認してください。

- VPN 接続のオンプレミス側がネットワーク トラフィックをブロックしていないことを確認します。これは、ローカルの VPN デバイスかソフトウェアのログで確認できます。
- アップロードした RemoteApp テンプレート イメージが適切に sysprep されていることを確認します。RemoteApp イメージの要件は、ここで確認することができます: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- アップロードしたテンプレート イメージを使用して VM を作成し、(a) ローカルの Hyper-V サーバーと (b) Azure サブスクリプションでの Azure IAAS VM の作成のいずれでも問題なく起動して実行できることを確認します。VM の作成に失敗するか VM が起動しない場合は、通常はテンプレート イメージが適切に準備されていなくて修正が必要であることを示します。

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

上のエラーのいずれかが表示される場合は、次のことを確認してください。

- ドメイン参加のために入力した資格情報が有効であることの確認
- ドメイン参加の資格情報が正しいか、ドメイン参加のための適切なアクセス許可があることの確認
- 組織単位 (OU) が適切にフォーマットされていて、Active Directory 内に存在していることを確認します。 


<!--HONumber=52--> 