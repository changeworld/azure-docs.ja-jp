---
title: Azure AD ECMA Connector ホストの前提条件
description: この記事では、Azure AD ECMA コネクタ ホストを使用するために必要な前提条件とハードウェア要件について説明します。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50bb95b5362902eed2ce297350e9fb2760530e22
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449575"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA コネクタ ホストの前提条件

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスを要求するには、[アクセス要求フォーム](https://aka.ms/onpremprovisioningpublicpreviewaccess)を使用してください。 今後数か月の間に、より多くのお客様および接続ユーザーにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

この記事では、Azure Active Directory (Azure AD) ECMA Connector Host を使用するために必要な前提条件に関するガイダンスを提供します。

次のフローは、Azure AD ECMA Connector Host をインストールして構成するプロセスを示しています。

 ![インストールのフローを示す図。](./media/on-premises-ecma-prerequisites/flow-1.png)

インストールと構成の詳細については、以下を参照してください。

   - [Azure AD ECMA コネクタ ホストのインストール](on-premises-ecma-install.md)
   - [Azure AD ECMA コネクタ ホストとプロビジョニング エージェントの構成](on-premises-ecma-configure.md)
   - [Azure AD ECMA コネクタ ホストの汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)

## <a name="on-premises-prerequisites"></a>オンプレミスの前提条件

 - SQL データベースなど、ユーザーの作成、更新、および削除が可能なターゲット システム。
 - エクスポート、スキーマの取得、および必要に応じてフル インポートまたは差分インポート操作をサポートする、ターゲットシステムの ECMA 2.0 以降のコネクタ。 構成中に ECMA コネクタの準備ができていない場合でも、環境に SQL Server インスタンスが存在し、汎用 SQL コネクタを使用していれば、エンド ツー エンドのフローを検証できます。
 - インターネット アクセス可能な TCP/IP アドレス、ターゲット システムへの接続のほか、login.microsoftonline.com への送信接続を備えた Windows Server 2016 以降のコンピューター。 1 つの例は、Azure IaaS でホストされているか、またはプロキシの背後にある Windows Server 2016 仮想マシンです。 サーバーには、少なくとも 3 GB の RAM が必要です。
 - .NET Framework 4.7.1 を含むコンピューター。

## <a name="cloud-requirements"></a>クラウドの要件

 - Azure AD Premium P1 または Premium P2 (または EMS E3 または E5) を持つ Azure AD テナント。 
 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
 - プロビジョニング エージェントを構成するためのハイブリッド管理者ロールと、Azure portal でプロビジョニングを構成するためのアプリケーション管理者またはクラウド管理者ロール。

## <a name="next-steps"></a>次のステップ

- [Azure AD ECMA Connector Host のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA Connector Host の構成](on-premises-ecma-configure.md)
- [汎用 SQL コネクタ](on-premises-sql-connector-configure.md)
- [チュートリアル - ECMA Connector Host の汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)
