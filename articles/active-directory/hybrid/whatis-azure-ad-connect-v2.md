---
title: Azure AD Connect V2.0 とは | Microsoft Docs
description: Azure AD Connect の次のバージョンについて説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management, has-adal-ref
ms.openlocfilehash: 4ef535684c8d2cefcf0db9e2044c59b02318ed97
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717603"
---
# <a name="introduction-to-azure-ad-connect-v20"></a>Azure AD Connect v2.0 について 

Azure AD Connect は数年前にリリースされました。  これ以降、Azure AD Connect を使用しているいくつかのコンポーネントが非推奨となり、新しいバージョンに更新されます。  これらのコンポーネントのすべてを個別に更新するには、時間と計画が必要です。  

これに対処するため、これらの新しいコンポーネントの多くを新しい単一のリリースにバンドルします。こんため、更新は 1 回で済みます。 このリリースは Azure AD Connect v2.0 になります。  これは、最新の基本コンポーネントを使用して構築されたハイブリッドの ID 目標を達成するために使用される、同じソフトウェアの新しいバージョンです。 

## <a name="what-are-the-major-changes"></a>主な変更は? 

### <a name="sql-server-2019-localdb"></a>SQL Server 2019 LocalDB 

以前のバージョンの Azure AD Connect は、SQL Server 2012 LocalDB に付属して出荷されます。 V2.0 は SQL Server 2019 LocalDB 付きで出荷されます。これにより、安定性とパフォーマンスが向上し、セキュリティ関連のいくつかのバグ修正が行われます。 SQL Server 2012 は、2022年7月に延長サポートを終了します。 詳しくは、[Microsoft SQL 2019](https://www.microsoft.com/sql-server/sql-server-2019)をご覧ください。

### <a name="msal-authentication-library"></a>MSAL 認証ライブラリ 

以前のバージョンの Azure AD Connect は、ADAL 認証ライブラリに付属して出荷されます。 このライブラリは、2022 年 6 月に非推奨となる予定です。 V2.0 リリースには、新しい MSAL ライブラリが付属しています。 詳細については、[「MSAL ライブラリの概要](../../active-directory/develop/msal-overview.md)」を参照してください。

### <a name="visual-c-redist-14"></a>Visual C++ Redist 14 

SQL Server 2019 には Visual C++ Redist 14 ランタイムが必要であるため、このバージョンを使用するように C++ ランタイム ライブラリを更新します。 これは Azure AD Connect v2.0 パッケージと共にインストールされるため、C++ ランタイムの更新プログラムに対して何らかの操作を行う必要はありません。 

### <a name="tls-12"></a>TLS 1.2 

TLS 1.0 と TLS 1.1 は、安全でないと見なされ、Microsoft によって非推奨とされているプロトコルです。 このリリースの Azure AD Connect では、TLS 1.2 のみがサポートされます。 Azure AD Connect V2.0 でサポートされているすべてのバージョンの Windows Server では、既に TLS 1.2 が既定になっています。 サーバーで TLS 1.2 がサポートされていない場合は、Azure AD Connect v2.0 を展開する前にこの機能を有効にする必要があります。 詳細については、[Azure AD Connect への TLS 1.2 の適用](reference-connect-tls-enforcement.md)に関するページを参照してください。

### <a name="all-binaries-signed-with-sha2"></a>SHA2 で署名されたすべてのバイナリ 

一部のコンポーネントには SHA1 署名済みバイナリがあることがわかりました。 ダウンロード可能なバイナリに対して SHA1 はサポートされなくなりました。すべてのバイナリを SHA2 署名にアップグレードしました。 デジタル署名は、更新プログラムが Microsoft から直接取得され、配信中に改ざんされないようにするために使用されます。 SHA-1 アルゴリズムには欠点があり、業界標準に合わせるために、Windows 更新プログラムの署名が、より安全な SHA-2 アルゴリズムを使用するように変更されました。」  

ユーザー側で必要な操作はありません。 

### <a name="windows-server-2012-and-windows-server-2012-r2-are-no-longer-supported"></a>Windows Server 2012 と Windows Server 2012 R2 はサポートされなくなりました 

SQL Server 2019 には、サーバー オペレーティング システムとして Windows Server 2016 以降が必要です。 AAD Connect v2 には SQL Server 2019 コンポーネントが含まれているため、以前のバージョンの Windows サーバーをサポートすることはできなくなりました。  

このバージョンを以前のバージョンの Windows サーバーにインストールすることはできません。 Azure AD Connect サーバーを Windows サーバー2019にアップグレードすることをお勧めします。これは、Windows サーバーのオペレーティング システムの最新バージョンです。 

この[記事で](/windows-server/get-started-19/install-upgrade-migrate-19)は、古い Windows サーバー バージョンから Windows サーバー 2019 へのアップグレードについて説明します。 

### <a name="powershell-50"></a>PowerShell 5.0 

このリリースの Azure AD Connect には、PowerShell 5.0 を必要とするいくつかのコマンドレットが含まれているため、この要件は Azure AD Connect の新しい前提条件です。  

PowerShell の前提条件の詳細については、[こちら](/powershell/scripting/windows-powershell/install/windows-powershell-system-requirements#windows-powershell-50)を参照してください。

 >[!NOTE]
 >PowerShell 5 は既に Windows Server 2016 の一部であるため、最近のバージョンの Windows Server であれば、アクションを実行する必要はありません。 

## <a name="what-else-do-i-need-to-know"></a>他に知るべきことは? 


**このアップグレードが重要なのはなぜですか?** </br>
来年は、現在の Azure AD Connect サーバーのインストールに含まれるいくつかのコンポーネントがサポートから除外されます。 サポートされていない製品を使用している場合、サポート チームは組織が必要とするサポート エクスペリエンスを提供することが難しくなります。 そのため、できるだけ早くこの新しいバージョンにアップグレードすることをお勧めします。 

このアップグレードは、Azure AD Connect の前提条件を更新する必要があり、これらの前提条件の新しいバージョンにサーバーを計画および更新するのに時間がかかる場合があるため、特に重要です 

**理解しておくべき新機能はありますか?** </br>
いいえ。このリリースには、新しい機能は含まれません。 このリリースには、Azure AD Connect 上の一部の基本コンポーネントの更新プログラムのみが含まれています。 

**以前のバージョンから V2.0 にアップグレードすることはできますか?** </br>
はい–以前のバージョンの Azure AD Connect から Azure AD Connect v2.0 へのアップグレードがサポートされています。 [この記事](how-to-upgrade-previous-version.md)のガイダンスに従って、最適なアップグレード方法を決定してください。 

**現在のサーバーの構成をエクスポートして Azure AD Connect v2.0 にインポートすることはできますか?** </br>
はい、できます。これは、特に新しいオペレーティング システムのバージョンにアップグレードする場合なら特に、Azure AD Connect v2.0 に移行するための優れた方法です。 インポート/エクスポートの構成機能の詳細と、この[記事](how-to-connect-import-export-config.md)での使用方法については、こちらを参照してください。 

**Azure AD Connect の自動アップグレードを有効にしました-この新しいバージョンを自動的に取得しますか?** </br> いいえ。現時点では、Azure AD Connect v2.0 は自動アップグレードに使用できません。 

**まだアップグレードする準備ができていませんが、どれくらいの時間がありますか?** </br>
できるだけ早く Azure AD Connect V2.0 にアップグレードする必要があります。 **__すべての Azure AD Connect V1 バージョンが 2022 年 8 月 31 日に廃止されます。__** 現時点では、以前のバージョンの Azure AD Connect は引き続きサポートされますが、Azure AD Connect の一部のコンポーネントがサポート対象外になった場合は、適切なサポート エクスペリエンスを提供することが困難になる可能性があります。 このアップグレードは、ADAL および TLS 1.0/1.1 では特に重要であり、非推奨とされた後、これらのサービスが予期せず動作しなくなる可能性があります。 

**外部の SQL データベースを使用していて、SQL 2012 LocalDb を使用していない場合でも、アップグレードが必要ですか?** </br>
はい。TLS 1.0/1.1 および ADAL の廃止のために SQL Server 2012 を使用しない場合でも、サポートされている状態を維持するためにアップグレードする必要があります。 SQL Server 2012 は Azure AD Connect v2.0 でまだ外部の SQL データベースとして使用できることに注意してください。Azure AD Connect v2.0 の SQL 2019 ドライバーは、SQL Server 2012 と互換性があります。

**Azure AD Connect インスタンスを V2.0 にアップグレードした後、SQL 2012 コンポーネントは自動的にアンインストールされますか?** </br>
いいえ。SQL 2019 へのアップグレードでは、サーバーから SQL 2012 コンポーネントが削除されることはありません。 これらのコンポーネントが不要になった場合は、[SQL Server のアンインストールの手順](/sql/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup)に従ってください。

**アップグレードしなかった場合に行われる処理は?** </br>
インベントリから削除されるコンポーネントのいずれかが実際に非推奨とされるまでは、影響はありません。 Azure AD Connect は引き続き動作します。 

2022年1月に TLS 1.0/1.1 が非推奨となる予定です。サービスが予期せず動作を停止する可能性があるため、この日までにこれらのプロトコルを使用していないことを確認する必要があります。 ただし、サーバーを TLS 1.2 用に手動で構成することはできますが、V2.0 の Azure AD Connect を更新する必要はありません 

2022 年 6 月に、ADAL はサポートを終了します。 ADAL がサポートされなくなると、認証が予期せず動作を停止し、Azure AD Connect サーバーが正常に動作しなくなる可能性があります。 2022 年 6 月より前に Azure AD Connect V2.0 にアップグレードすることを強くお勧めします。 現在の Azure AD Connect バージョンでサポートされている認証ライブラリにアップグレードすることはできません。 

**2.0 にアップグレードした後、ADSync PowerShell コマンドレットが機能しませんか?** </br>
これは既知の問題です。 これを解決するには、バージョン2.0 をインストールまたはアップグレードした後、PowerShell セッションを再起動してから、モジュールを再インポートします。 次の指示に従って、モジュールをインポートします。
 
 1.  管理者特権で Windows PowerShell を開きます。
 1.  次のコードを入力するか、コピーして貼り付けます。 
 
     ```powershell
     Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync"
     ```

## <a name="license-requirements-for-using-azure-ad-connect-v20"></a>Azure AD Connect V2.0 を使用するためのライセンス要件

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Azure AD Connect Health を使用するためのライセンス要件
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>次のステップ

- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)

この記事では、古い Windows サーバー バージョンから Windows サーバー 2019 へのアップグレードについて説明します。
