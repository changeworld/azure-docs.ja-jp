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
ms.openlocfilehash: 2c668380103088fb0f434d513cf7490c39e4ead2
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570252"
---
# <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA コネクタ ホストの前提条件

>[!IMPORTANT]
> オンプレミス プロビジョニング プレビューは現在、招待のみのプレビューとなります。 この機能へのアクセスは、[こちら](https://aka.ms/onpremprovisioningpublicpreviewaccess)からリクエストできます。 今後数か月の間に、より多くのお客様とコネクタにプレビューを公開し、一般提供に向けて準備を進めていく予定です。

この記事では、Azure AD ECMA コネクタ ホストを使用するために必要な前提条件に関するガイダンスを提供します。  

Azure AD ECMA コネクタ ホストのインストールと構成はプロセスです。 プロセスを進めるには、以下のフローを使用します。

 ![インストールのフロー](./media/on-premises-ecma-prerequisites/flow-1.png)  

インストールと構成の詳細については、以下を参照してください。
   - [Azure AD ECMA コネクタ ホストのインストール](on-premises-ecma-install.md)
   - [Azure AD ECMA コネクタ ホストとプロビジョニング エージェントの構成](on-premises-ecma-configure.md)
   - [Azure AD ECMA コネクタ ホストの汎用 SQL コネクタの構成](on-premises-sql-connector-configure.md)

## <a name="on-premises-pre-requisites"></a>オンプレミスの前提条件
 - SQL データベースなど、ユーザーの作成、更新、および削除が可能なターゲット システム。
 - エクスポート、スキーマの取得、および必要に応じてフル インポートまたは差分インポート操作をサポートする、ターゲットシステムの ECMA 2.0 以降のコネクタ。 構成中に ECMA コネクタを準備していない場合でも、環境に SQL Server があり、Generic SQL コネクタを使用している場合は、エンドツーエンドフローを検証できます。
 - インターネットからアクセス可能な TCP/IP アドレス、ターゲットシステムへの接続、および login.microsoftonline.com への送信接続 (たとえば、Azure IaaS またはプロキシの背後でホストされている Windows Server 2016 仮想マシン) を備えた Windows Server 2016 以降のコンピューター。 サーバーには、少なくとも 3 GB の RAM が必要です。
 - .NET Framework 4.7.1 があるコンピューター

## <a name="cloud-requirements"></a>クラウドの要件

 - Azure AD Premium P1 または Premium P2 (または EMS E3 または E5) を持つ Azure AD テナント。 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]

 - Azure portal でプロビジョニングを構成するためのプロビジョニングエージェントおよびアプリケーション管理者またはクラウド管理者ロールを構成するためのハイブリッド管理者ロール。


## <a name="next-steps"></a>次の手順

- [Azure AD ECMA コネクタ ホスト のインストール](on-premises-ecma-install.md)
- [Azure AD ECMA コネクタ ホスト の構成](on-premises-ecma-configure.md)
- [Generic SQL コネクタ](on-premises-sql-connector-configure.md)
- [チュートリアル: ECMA コネクタ ホスト汎用 SQL コネクタ](tutorial-ecma-sql-connector.md)
