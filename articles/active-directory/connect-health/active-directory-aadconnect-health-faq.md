---
title: "Azure AD Connect Health の FAQ"
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
ms.date: 12/16/2016
ms.author: vakarand
translationtype: Human Translation
ms.sourcegitcommit: 2f990f4c888da91422a6a6e9873307cbb483e782
ms.openlocfilehash: b6687bc0ccde0e257338a939f39e343f75a6f5df


---
# <a name="azure-ad-connect-health-frequently-asked-questions-faq"></a>Azure AD Connect Health についてよく寄せられる質問 (FAQ)
この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。

## <a name="general-questions"></a>一般的な質問
**Q: 複数の Azure AD ディレクトリを管理しています。どのようにすればその 1 つを Azure Active Directory Premium に切り替えることができますか。**

右上隅にある、現在サインインしているユーザー名を選択し、適切なアカウントを選択することで、Azure AD テナントを切り替えることができます。 ここにアカウントが表示されていない場合は、[サインアウト] を選択し、Azure Active Directory Premium が有効になっているディレクトリのグローバル管理者資格情報を使用してサインインします。

## <a name="installation-questions"></a>インストールに関する質問
**Q: 各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響があるでしょうか。**

Microsoft Azure AD Connect Health エージェント ADFS、Web アプリケーション プロキシ サーバー、Azure AD Connect (同期) サーバー、ドメイン コントローラーをインストールすることによる、CPU、メモリ消費、ネットワーク帯域幅、ストレージへの影響は最小限です。

おおよその数値を以下に示します。

* CPU 消費率: 約 1% 上昇
* メモリ消費量: システム メモリ合計の最大 10%

> [!NOTE]
> エージェントは、Azure と通信できない場合、定義済みの上限に達するまで、データをローカルに保存します。 エージェントは、"最も長く使用されていない" という基準で、"キャッシュされた" データを上書きします。
> 
> 

* Azure AD Connect Health エージェントのローカル バッファー ストレージ: 約 20 MB
* AD FS サーバーの場合は、AD FS Audit Channel for Azure AD Connect Health エージェントがすべての監査データを上書き前に処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q: Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。 エージェントをインストールする場合、サーバーを再起動する必要はありません。 ただし、前提条件の一部のインストール手順では、サーバーの再起動が必要です。

たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールするにはサーバーの再起動が必要です。

**Q: Azure AD Connect Health サービスはパススルー http プロキシで動作しますか。**

はい。  実行中の操作については、HTTP プロキシを使用して送信 http 要求を転送するように Health エージェントを構成できます。

エージェントの登録時にプロキシを構成する必要がある場合は、Internet Explorer のプロキシ設定を事前に変更する必要があることがあります。

1. Internet Explorer から [設定]、[インターネット オプション]、[接続]、[LAN の設定] の順に開きます。
2. [LAN にプロキシ サーバーを使用する] をオンにします。
3. HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、[詳細設定] を選択します。

**Q: Azure AD Connect Health サービスは、Http プロキシに接続するときに基本認証をサポートしますか。**

いいえ。 基本認証に対して任意のユーザー名/パスワードを指定するメカニズムは、現在サポートされていません。

**Q: Azure AD Connect Health for AD DS では、どのバージョンの AD DS がサポートされていますか。**

以下の OS バージョンにインストールされている場合は AD DS の監視がサポートされます。

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

## <a name="operations-questions"></a>操作に関する質問
**Q: 自分の AD FS アプリケーション プロキシ サーバーまたは Web アプリケーション プロキシ サーバーで監査を有効にする必要はありますか。**

いいえ。Web アプリケーション プロキシ (WAP) サーバーで監査を有効にする必要はありません。 AD FS サーバーで有効にします。

**Q: Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。 Azure AD Connect Health エージェントは、定期的に成功条件を検出して、成功条件をサービスにレポートします。 一部のアラートは、時間に基づいて抑制されます。 つまり、アラートの生成から 72 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。

**Q: Azure AD Connect Health エージェントを使用するためには、どのファイアウォール ポートを開放すればよいですか。**

Azure AD Health サービス エンドポイントと通信できるようにするには、Azure AD Connect Health エージェント用に TCP/UDP ポート 443 と 5671 を開く必要があります。

**Q: Azure AD Connect Health ポータルに同じ名前のサーバーが 2 つ表示されるのはなぜですか?**

サーバーからエージェントを削除しても、サーバーは Azure AD Connect ポータルから自動的に削除されません。  サーバーから手動でエージェントを削除したか、サーバー自体を削除した場合は、Azure AD Connect Health ポータルから手動でサーバー エントリを削除する必要があります。 

同じ詳細情報 (コンピューター名など) でサーバーを再イメージ化したか、新しいサーバーを作成し、Azure AD Connect Health ポータルから登録済みサーバーを削除せずに新しいサーバーにエージェントをインストールした場合は、同じ名前の 2 つのエントリが表示される可能性があります。  
この場合は、古いサーバーに属しているエントリを手動で削除する必要があります。 このサーバーのデータは古くなっているはずです。

## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health のバージョンの履歴](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


