
<properties 
    pageTitle="Azure RemoteApp で Office 365 を使用する" 
    description="Azure RemoteApp で Office 365 を使用する方法について説明します。" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="elizapo" />

# Azure RemoteApp で Office 365 を使用する

RemoteApp で Office アプリケーションをホストする場合、Office 365 ProPlus と Office 2013 Professional Plus 評価版の 2 つの選択肢があります。

## Office 365 ProPlus 
Office 365 ProPlus テンプレート イメージを使用して RemoteApp コレクションを作成することができます。このオプションでは、Office 365 サービスを RemoteApp に拡張することができます。既存のサブスクリプション プランが必要で、ユーザーはスタンドアロンまたは Office 365 ProPlus サービスを介したライセンスを取得している必要があります。

RemoteApp は、Office 365 共有コンピューターのライセンス認証をサポートします。共有コンピューターのライセンス認証を有効にし、インストール用に [Office 展開ツール](http://www.microsoft.com/download/details.aspx?id=36778)を使用する場合、Office 365 ProPlus はライセンス認証されることなくインストールされます。Office 365 を含むコレクションにユーザーがサインインすると、Office によって、ユーザーが Office 365 ProPlus に対してプロビジョニングされているかどうかがチェックされます。プロビジョニングされている場合、Office によって、Office 365 ProPlus が一時的にライセンス認証されます。このライセンス認証は、ユーザーがサービスからサインアウトするまで継続します。

Office 365 共有コンピューターのライセンス認証を使用するには、[カスタム テンプレート](remoteapp-create-custom-image.md)を作成し、「[リモート デスクトップ サービスを使用して Office 365 ProPlus を展開する](https://technet.microsoft.com/library/dn782858.aspx)」の説明に従って、Office 365 ProPlus をインストールする必要があります。

ユーザーの Office 365 ライセンスは、[Office 365 管理ポータル](https://portal.office365.com/)で管理できます。詳細については、「[Office 365 プランのオプション](http://technet.microsoft.com/library/office-365-plan-options.aspx)」を参照してください。


## Office 2013 Professional Plus 評価版 
RemoteApp の 30 日間の無償試用期間中は、Office 2013 Professional Plus (評価版) テンプレート イメージを使用して RemoteApp コレクションを作成できます。Azure Active Directory 作業アカウントまたは Microsoft アカウントを使用して、この評価版のコレクションをユーザーに割り当てることができます。追加のサブスクリプションは必要ありません。

これは Office で RemoteApp を試用できる便利なオプションです。ただし、このオプションは評価とテストのみを目的としています。Office 2013 Professional Plus (評価版) のテンプレート イメージを使用して作成 RemoteApp コレクションは、実稼働モードに移行することはできず、試用期間の終了後に無効になります。

## 評価版から実稼働環境への移行
30 日間の無償評価版を開始すると、ポータルの [RemoteApp] セクション内のメモに有料アカウントに移行するまでの残りの試用期間が表示されます。自分のアカウントをアクティブ化し、このメモのリンクを使用して実稼働モードに切り替えることができます。

自分のアカウントをアクティブ化すると、アカウント内のすべての RemoteApp コレクションに影響します。

- Windows Server 2012 R2 または Office 365 ProPlus のテンプレート イメージで実行されているコレクションは実稼働環境にシームレスに移行します。実行中のセッションを含むすべてのユーザー データおよび設定は、そのまま移行されます。
- カスタム テンプレート イメージをアップロードしている場合は、それらのイメージを使用しているコレクションもシームレスに移行されます。
- Office 2013 Professional Plus (評価版) のテンプレート イメージは、評価のみを目的としています。このテンプレート イメージで実行されているコレクションは、実稼働環境に移行することはできません。これらが "無効" の状態になります。


試用期間の終了後に実稼働モードに移行しないと、RemoteApp コレクションは無効になります。データは保存されます - 設定とユーザーのデータはあと 90 日間保存されますので、サービスをアクティブ化してデータを失うことなく、実稼働モードに切り替えることができます。

<!--HONumber=54-->