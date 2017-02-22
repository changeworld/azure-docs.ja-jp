---
title: "Azure Active Directory Connect Health の FAQ | Microsoft Docs"
description: "この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。"
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 7c320a043322fefea1f58301492d4c5a0567320c
ms.openlocfilehash: aff2ced283de8282860d0aa356b12478f647d09c

---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect Health についてよく寄せられる質問 (FAQ)
この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## <a name="general-questions"></a>一般的な質問
**Q: 複数の Azure AD ディレクトリを管理しています。Azure Portal で異なるディレクトリに切り替えるにはどうすればよいですか。**

右上隅にある、現在サインインしているユーザー名を選択し、適切なアカウントを選択することで、Azure AD テナントを切り替えることができます。 アカウントがここに表示されない場合は、[サインアウト] を選び、ディレクトリの適切な資格情報を使ってサインインします。

**Q: Azure AD Connect Health では、どのバージョンの ID の役割がサポートされていますか。**

次の表は、役割と、サポートされているオペレーティング システムのバージョンの一覧です。

|役割| オペレーティング システム/バージョン|
|--|--|
|AD FS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | バージョン 1.0.9125 以降。|
|AD DS| <ul> <li> Windows Server 2008R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

サービスが提供する機能は、役割とオペレーティング システムによって異なる場合があることに注意してください。 つまり、すべての機能をすべての OS バージョンで利用できるとは限りません。 詳しくは、機能の説明をご覧ください。

**Q: インフラストラクチャを監視するには、いくつのライセンスが必要ですか。**

* 最初の Connect Health エージェントに、少なくとも&1; つの Azure AD Premium ライセンスが必要です。
* 追加登録されるエージェントごとに、25 個の追加 AADP ライセンスが必要です。 
* エージェントの数は、監視対象のすべての役割 (AD FS、Azure AD Connect、AD DS) で登録されるエージェントの合計数に相当します。

ライセンスの情報については、[Azure AD の料金に関するページ](https://aka.ms/aadpricing)もご覧ください。

例:

| 登録されているエージェント | 必要なライセンスの数 | 監視構成の例 |
| ------ | --------------- | --- |
| 1 | 1 | AAD Connect Server ×&1; |
| 2 | 26| AAD Connect Server × 1、ドメイン コントローラー × 1 |
| 3 | 51 | AD FS Server × 1、AD FS プロキシ × 1、ドメイン コントローラー × 1 |
| 4 | 76 | AD FS Server × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |
| 5 | 101 | AAD Connect Server × 1、AD FS Server × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |
## <a name="installation-questions"></a>インストールに関する質問
**Q: 各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響があるでしょうか。**

Microsoft Azure AD Connect Health エージェント ADFS、Web アプリケーション プロキシ サーバー、Azure AD Connect (同期) サーバー、ドメイン コントローラーをインストールすることによる、CPU、メモリ消費、ネットワーク帯域幅、ストレージへの影響は最小限です。

おおよその数値を以下に示します。

    * CPU 消費率: 約&1; -&5;% 上昇
    * メモリ消費量: システム メモリ合計の最大 10%

> [!NOTE]
> Health エージェントは、Connect Health サービスへの接続を失うと、定義されている上限に達するまで、ローカルにデータを格納します。 エージェントは、"最も長く使用されていない" という基準で、"キャッシュされた" データを上書きします。
>
>

* Azure AD Connect Health エージェントのローカル バッファー ストレージ: 約&20; MB
* AD FS サーバーの場合は、AD FS Audit Channel for Azure AD Connect Health エージェントがすべての監査データを上書き前に処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q: Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。 エージェントをインストールする場合、サーバーを再起動する必要はありません。

前提条件の一部のインストール手順では、サーバーの再起動が必要です。 たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールするにはサーバーの再起動が必要です。

**Q: Azure AD Connect Health サービスはパススルー http プロキシで動作しますか。**

はい。  実行中の操作については、HTTP プロキシを使用して送信 http 要求を転送するように Health エージェントを構成できます。 詳しくは、[Health エージェント対応の HTTP プロキシの構成に関するページ](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)をご覧ください。

エージェントの登録時にプロキシを構成する必要がある場合は、Internet Explorer のプロキシ設定を事前に変更する必要があることがあります。

1. Internet Explorer から [設定]、[インターネット オプション]、[接続]、[LAN の設定] の順に開きます。
2. [LAN にプロキシ サーバーを使用する] をオンにします。
3. HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、[詳細設定] を選択します。

**Q: Azure AD Connect Health サービスは、Http プロキシに接続するときに基本認証をサポートしますか。**

いいえ。 基本認証に対して任意のユーザー名/パスワードを指定するメカニズムは、現在サポートされていません。

**Q: Azure AD Connect Health エージェントを使用するためには、どのファイアウォール ポートを開放すればよいですか。**

ファイアウォールのポートとその他の接続要件の一覧については、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。


**Q: Azure AD Connect Health ポータルに同じ名前のサーバーが&2; つ表示されるのはなぜですか?**

サーバーからエージェントを削除しても、サーバーは Azure AD Connect ポータルから自動的に削除されません。  サーバーから手動でエージェントを削除したか、サーバー自体を削除した場合は、Azure AD Connect Health ポータルから手動でサーバー エントリを削除する必要があります。

同じ詳細情報 (コンピューター名など) でサーバーを再イメージ化したか、新しいサーバーを作成し、Azure AD Connect Health ポータルから登録済みサーバーを削除せずに新しいサーバーにエージェントをインストールした場合は、同じ名前の&2; つのエントリが表示される可能性があります。  
この場合は、古いサーバーに属しているエントリを手動で削除する必要があります。 このサーバーのデータは古くなっているはずです。

## <a name="health-agent-registration-and-data-freshness"></a>Health エージェントの登録とデータの新しさ

**Q: Health エージェントの登録が失敗する一般的な理由と、トラブルシューティングの方法を教えてください。**

Health エージェントの登録は、次の理由により失敗する可能性があります。
    * ファイアウォールがトラフィックをブロックしているため、エージェントが必要なエンドポイントと通信できません。 これは、Web アプリケーション プロキシ サーバーで特によくあります。 必要なエンドポイントとポートへの発信通信が許可されていることを確認してください。 詳しくは、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。
    * 発信通信は、ネットワーク層による SSL 検査の対象になります。 そのために、エージェントによって使われる証明書が検査サーバー/エンティティによって置き換えられ、エージェント登録の完了に必要な手順の実行が失敗します。
    * ユーザーに、エージェントの登録を実行するためのアクセス権がありません。 グローバル管理者には、既定でアクセス権が与えられます。 [ロール ベースのアクセス制御](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)を使って、他のユーザーにアクセスを委任できます。

**Q: "Health サービス データが最新ではありません" というアラートを受け取ります。トラブルシューティングの方法を教えてください。**

このアラートは、過去 2 時間以内に Health サービスがサーバーからすべてのデータ ポイントを受信しないと生成されます。 このアラートが発生する場合、複数の理由が考えられます。
    * ファイアウォールがトラフィックをブロックしているため、エージェントが必要なエンドポイントと通信できません。 これは、Web アプリケーション プロキシ サーバーで特によくあります。 必要なエンドポイントとポートへの発信通信が許可されていることを確認してください。 詳しくは、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。
    * 発信通信は、ネットワーク層による SSL 検査の対象になります。 そのために、エージェントによって使われる証明書が検査サーバー/エンティティによって置き換えられ、Connect Health サービスへのデータのアップロードが失敗します。
    * エージェントに組み込まれている接続コマンドを使うことができます。 詳細については、[こちら](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を参照してください。
    * また、エージェントは、認証されていない HTTP プロキシを経由した発信接続もサポートしています。 詳細については、[こちら](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy)を参照してください。


## <a name="operations-questions"></a>操作に関する質問
**Q: Web アプリケーション プロキシ サーバーで監査を有効にする必要がありますか。**

いいえ。Web アプリケーション プロキシ (WAP) サーバーで監査を有効にする必要はありません。

**Q: Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。 Azure AD Connect Health エージェントは、定期的に成功条件を検出して、成功条件をサービスにレポートします。 一部のアラートは、時間に基づいて抑制されます。 つまり、アラートの生成から 72 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。


## <a name="migration-questions"></a>移行に関する質問

このセクションは、Azure AD Connect Health サービス データの今後の移行が通知されるユーザーのみを対象としています。

**Q: 移行後、エージェントを再登録するか、通知を再構成する必要はありますか。**

いいえ。エージェントの登録情報と通知の設定は、移行の一環として移動されます。

**Q: 移行後どのくらい経ってから、ポータルにデータが表示されますか。**

データは、移行後&1; 時間以内にポータルに表示されます。

**Q: 既存のアクティブなアラートはどうなりますか。**

該当するすべてのアラートが、移行後&1; 時間以内に有効になります。


## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health のバージョンの履歴](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Feb17_HO2-->


