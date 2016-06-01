
<properties
    pageTitle="RemoteApp クラウド コレクションの作成のトラブルシューティング | Microsoft Azure"
    description="RemoteApp のクラウド コレクション作成でエラーが発生したときのトラブルシューティング方法について説明します。"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2016"
    ms.author="elizapo" />



# RemoteApp クラウド コレクション作成のトラブルシューティング

クラウド コレクションを作成できない場合、次の情報を確認してください。

## イメージが無効です ##
Azure がコレクションにプロビジョニングするのを待っているとき、「GoldImageInvalid」のようなメッセージが表示された場合、テンプレート イメージが[定義されているイメージ要件](remoteapp-imagereqs.md)に一致していません。その場合、[要件](remoteapp-imagereqs.md)を読み、イメージを修正し、コレクションをもう一度作成します。

## Microsoft Azure 管理ポータルでの一般的なエラー

	DNS server could not be reached
	ProvisioningTimeout

カスタム イメージを使用しているため、多くの場合、クラウド コレクションの作成中にエラーが発生します。上のエラーのいずれかが表示され、コレクションの作成にカスタム イメージを使用している場合は、次のことを確認してください。

- アップロードしたカスタム イメージがイメージの要件を満たしていることを確認します。
- ほとんどの場合、一般的な問題はイメージが適切に sysprep されていないことです。  
- イメージが Hyper-V を使用して起動できることを確認するか、イメージを使用して Azure サブスクリプション内で IaaS 仮想マシンを直接作成してみます。仮想マシンの起動が失敗する場合、通常はカスタム イメージが適切に準備されていないことを示します。RemoteApp 用のカスタム テンプレート イメージの作成方法に従って、カスタム イメージが作成されていることを確認します。

サブスクリプションに含まれている Microsoft イメージのいずれかを使用している場合は、再度コレクションの作成を試みます。引き続き問題が発生する場合は、Microsoft サポートにお問い合わせください。

	PlatformImageTrialModeOnly

このエラーが表示される場合、通常は、次のようなことを意味します。有料アカウントにアップグレード済みであるのに、サービスの試用版モードでのみ有効な Microsoft 提供のイメージを使用している。この場合は、適切なイメージを指定して、クラウド コレクションを再度作成してみてください。

<!---HONumber=AcomDC_0518_2016-->