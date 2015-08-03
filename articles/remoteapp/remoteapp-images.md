<properties
    pageTitle="RemoteApp テンプレート イメージとは何ですか。"
    description="RemoteApp に含まれるテンプレート イメージについて説明します。"
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/13/2015" 
    ms.author="elizapo" />

# RemoteApp テンプレート イメージとは何ですか。

Azure RemoteApp サブスクリプションには、次の 3 つのテンプレート イメージが含まれています:


- Windows Server 2012
- Microsoft Office 365 ProPlus (Office 365 サブスクリプションが必要)
- Microsoft Office 2013 Professional Plus (評価版のみ)

> [AZURE.IMPORTANT]使用するイメージにかかわらず、他のユーザーとアプリを共有するときは常にライセンスが必要です。詳細については、「[How does licensing work in Azure RemoteApp?](remoteapp-licensing.md)」を参照してください。

各イメージが含んでいるものについては、詳細を参照してください。

## Windows Server 2012 R2 ("バニラ イメージ")
このイメージは Microsoft Windows Server 2012 R2 Datacenter オペレーティング システムに基づいており、Azure RemoteApp テンプレート イメージの要件を満たすために、次のような役割と機能がインストールされています。


- .NET Framework 4.5, 3.5.1, 3.5
- デスクトップ エクスペリエンス
- インクと手書きサービス
- メディア ファンデーション
- リモート デスクトップ セッション ホスト
- Windows PowerShell 4.0
- Windows PowerShell ISE
- WoW64 サポート

このイメージには次のようなアプリケーションがインストールされています。

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## Microsoft Office 365 ProPlus (サブスクリプションが必要)
Office 365 は最もリクエストの多いアプリケーションなので、連携する "カスタム" イメージを作成しました。

このイメージは、バニラ イメージの拡張機能であり、Windows Server 2012 R2 のイメージで説明するコンポーネントに加え、次の Microsoft Office 365 ProPlus を含んでいます。


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office Proofing Tools

次のアプリケーションも同様にインストールされます。

- SQL Native Client
- ODBC ドライバー
- SQL Server データ マイニング クライアント
- MasterDataServices クライアント
- Microsoft Publisher
- Power Query
- Power Map


Office 365 ProPlus のアプリのすべての機能は、Office 365 ProPlus のプランを持つユーザーのみが使用できます。Office 365 のサブスクリプション プランについては、「[Office 365 プランのオプション](http://technet.microsoft.com/library/office-365-plan-options.aspx)」を参照してください。RemoteApp のライセンス体系の詳細については、「[How does licensing work in Azure RemoteApp?](remoteapp-licensing.md)」を参照してください。

## Microsoft Office 2013 Professional Plus (評価版のみ)
無償試用期間中には、Office 2013 のイメージを使用してサービスをテストすることができます。

このイメージは、バニラ イメージの拡張機能であり、Windows Server 2012 R2 のイメージで説明するコンポーネントに加え、インストールされている次の Microsoft Office 2013 Professional Plus のコンポーネントを含んでいます。


- Access
- Excel
- Lync
- OneNote
- OneDrive for Business
- Outlook
- PowerPoint
- Project
- Visio
- Word
- Microsoft Office Proofing Tools

> [AZURE.IMPORTANT]**法的情報:** このイメージには、Microsoft Office のライセンスは含まれていないため、*運用環境では使用できません*。Office 2013 Professional Plus イメージは評価版を使用する目的でのみ提供されます。運用環境で Azure RemoteApp の Office アプリケーションを使用する場合は、Office 365 ProPlus のイメージを使用する必要があります。RemoteApp のライセンス体系の詳細については、「[How does licensing work in Azure RemoteApp?](remoteapp-licensing.md)」を参照してください。
 

<!---HONumber=July15_HO4-->