---
title: ユーザーを Azure RemoteApp コレクションに追加する | Microsoft Docs
description: Azure RemoteApp コレクションにユーザーを追加する方法について説明します。
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Azure RemoteApp コレクションにユーザーを追加する方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。詳細については、[お知らせ](https://go.microsoft.com/fwlink/?linkid=821148)をご覧ください。
> 
> 

Azure RemoteApp のアプリをユーザーが表示および使用できるようにするには、コレクションへのアクセス権をユーザーに付与しておく必要があります。これは簡単です。**[ユーザー アクセス]** タブで、対象となるユーザーのアカウント情報を入力し、チェック マークをクリックします。

どのようなアカウント情報が必要ですか。 作成したコレクション (クラウドまたはハイブリッド) のタイプとそのコレクションで Office 365 ProPlus を使用しているかどうかによって異なります。

## サポートされているユーザー ID
アプリケーションへのアクセスには、コレクションのタイプ (クラウドとハイブリッド) ごとに、異なるユーザー ID を使用できます。

RemoteApp のハイブリッド コレクションでは、オンプレミスの Active Directory ドメイン インフラストラクチャと Azure Active Directory テナントをディレクトリ統合でセットアップする必要があります (および必要に応じてシングル サインオン)。さらに、オンプレミスのディレクトリに Active Directory オブジェクトを作成する必要があります。

RemoteApp のクラウド コレクションの場合、Azure Active Directory サポート ID を持つすべてのユーザーに、Microsoft アカウントを組み込むための RemoteApp へのユーザー アクセス権を付与できます。次の表を参照してください。

Office 365 ユーザーは、Azure Active Directory のユーザーです。そのユーザーに Azure Active Directory のハイブリッドのディレクトリ同期アカウントがあれば、RemoteApp ハイブリッド デプロイでのユーザー アクセス権を付与できます。

次の表は、コレクションでサポートされる ID と Active Directory の要件を確認するためのクイック リファレンスとして使用できます。

| ユーザー アカウント | クラウド | ハイブリッド |
| --- | --- | --- |
| Microsoft アカウント |はい |なし |
| Azure Active Directory (Azure AD) | | |
| Azure AD クラウドのみ |はい |なし |
| パスワード同期がある ADsync |はい |はい |
| パスワード同期がない ADsync |はい |なし |
| AD FS がある ADsync |はい |はい |
| [サード パーティの Azure サポート ID プロバイダー](https://msdn.microsoft.com/library/azure/jj679342.aspx) (Ping など) |はい |はい |
| Multi-Factor Authentication |はい |はい |

RemoteApp を使用するために Active Directory を構成する方法の詳細については、[ここ](remoteapp-ad.md)を参照してください。

> [!NOTE]
> Azure Active Directory ユーザーは、サブスクリプションに関連付けられているテナントに属している必要があります(サブスクリプションは、ポータルの **[設定]** タブで表示、変更できます。詳細については、「[Change the Azure Active Directory tenant used by RemoteApp](remoteapp-changetenant.md)」を参照してください)。
> 
> 

## Office 365 ProPlus ユーザー アカウントの情報
コレクションに、Office 365 ProPlus のテンプレート イメージを使用しているか*または* Office 365 を使用するカスタム イメージを作成した場合は、サブスクリプションの既定のドメイン用の Office 365 サブスクリプションを持つ Azure Active Directory のユーザーのみを追加できます。詳細については、「[Azure RemoteApp で Office 365 を使用する](remoteapp-o365.md)」を参照してください。

<!---HONumber=AcomDC_0817_2016-->