---
title: セキュリティの概要
description: Azure Arc 対応サーバーに関するセキュリティ情報。
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: e5fc087dac29a01f6463766c8d3b3ae0ae26596b
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069484"
---
# <a name="azure-arc-enabled-servers-security-overview"></a>Azure Arc 対応サーバー セキュリティの概要

この記事では、企業内に Azure Arc 対応サーバーを展開する前に評価しなければならない、セキュリティ構成と考慮事項について説明します。

## <a name="identity-and-access-control"></a>ID とアクセスの制御

Azure Arc 対応サーバーにはそれぞれマネージド ID が存在します。これは、Azure サブスクリプションに属するリソース グループの一部です。 その ID はオンプレミスまたはクラウド環境で稼働しているサーバーを表します。 このリソースへのアクセスは、標準の [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)によって制御されます。 Azure portal の [ **[Access Control (IAM)]**](../../role-based-access-control/role-assignments-portal.md) ページから、Azure Arc 対応サーバーにアクセスできるユーザーを確認できます。

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure Arc 対応サーバーのアクセス制御" border="false" lightbox="./media/security-overview/access-control-page.png":::

リソースに対する[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)または管理者ロールのアクセス権を付与されたユーザーおよびアプリケーションは、マシン上での[拡張機能](manage-vm-extensions.md)の展開や削除などの、リソースに対する変更を加えることができます。 拡張機能には特権コンテキストで実行される任意のスクリプトを含めることができるため、Azure リソースの共同作成者はサーバーの間接管理者になるものと見なしてください。

**Azure Connected Machine のオンボード** ロールは、大規模なオンボードで使用でき、Azure での新しい Azure Arc 対応サーバーの読み取りまたは作成のみが可能です。 既に登録済みのサーバーの削除や、拡張機能の管理には使用できません。 ベスト プラクティスとして、このロールは、大規模なマシンのオンボードに使用される Azure Active Directory (Azure AD) サービス プリンシパルのみに割り当てることをお勧めします。

**Azure Connected Machine のリソース管理者** ロールのメンバーであるユーザーは、マシンの読み取り、変更、再オンボード、および削除を行うことができます。 このロールは、Azure Arc 対応サーバーの管理をサポートするように設計されていますが、リソース グループまたはサブスクリプション内の他のリソースの管理はサポートしていません。

## <a name="agent-security-and-permissions"></a>エージェントのセキュリティとアクセス許可

Windows 上の Azure Connected Machine エージェント (azcmagent) を管理するには、ローカルの管理者グループのメンバーであるユーザー アカウントが必要です。 Linux では、ルート アクセス権が必要です。

Azure Connected Machine エージェントは、お使いのマシンで実行される 3 つのサービスで構成されています。

* Hybrid Instance Metadata Service (HIMDS) サービスは、Arc のすべてのコア機能を担当します。これには、Azure へのハートビートの送信、他のアプリがマシンの Azure リソース ID を知るためのローカル インスタンス メタデータ サービスの公開、他の Azure サービスに対して認証を行うための Azure AD トークンの取得などが含まれます。 このサービスは、Windows では特権のない仮想サービス アカウントとして実行され、Linux では **HIMDS** ユーザーとして実行されます。

* ゲスト構成サービス (GCService) は、マシン上の Azure Policy を評価する役割を担います。

* ゲスト構成拡張サービス (ExtensionService) は、マシン上の拡張機能 (エージェント、スクリプト、またはその他のソフトウェア) のインストール、更新、削除を担当します。

ゲスト構成と拡張サービスは、Windows ではローカル システムとして、Linux ではルートとして実行されます。

## <a name="using-a-managed-identity-with-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーでのマネージド ID の使用

既定では、Arc によって使用される Azure Active Directory システム割り当て ID は、Azure での Azure Arc 対応サーバーの状態を更新するためにのみ使用できます。 たとえば、ハートビートの状態の *[最終表示日]* です。 自分のサーバーにあるアプリケーションで、システム割り当て ID を使用して他の Azure サービスにアクセスする場合、必要ならば、他のロールを ID に割り当てることもできます。 Azure リソースにアクセスできるようにシステム割り当てマネージド ID を設定する方法の詳細は「[Azure Arc 対応サーバーでの Azure リソースに対して認証を行う](managed-identity-authentication.md)」を参照してください。 

Hybrid Instance Metadata Service は、マシン上で実行されている任意のアプリケーションからアクセスできますが、承認されたアプリケーションのみがシステム割り当て ID の Azure AD トークンを要求できます。 トークン URI への最初のアクセス試行時に、信頼された呼び出し元のみが読み取ることができるファイル システム上の場所に、ランダムに生成された暗号 BLOB がサービスによって生成されます。 次に呼び出し元は、(適切なアクセス許可があることを証明するために) ファイルを読み取り、Authorization ヘッダー内のファイルの内容で要求を再試行して、Azure AD トークンを正常に取得する必要があります。

* Windows では、呼び出し元は BLOB を読み取るために、ローカルの **Administrators** グループか、または **ハイブリッド エージェント拡張アプリケーション** グループのメンバーである必要があります。

* Linux では、呼び出し元は BLOB を読み取るために **HIMDS** グループのメンバーである必要があります。

Arc 対応 サーバーでマネージド ID を使用して認証を行い、Azure リソースにアクセスする方法の詳細は、次の動画をご覧ください。

> [!VIDEO https://www.youtube.com/embed/4hfwxwhWcP4]

## <a name="using-disk-encryption"></a>ディスク暗号化の使用

Azure Connected Machine エージェントと Azure サービスとの通信には公開キー認証が使用されます。 サーバーを Azure Arc にオンボードすると、秘密キーがディスクに保存され、エージェントが Azure と通信するたびに使用されます。 盗まれた場合、別のサーバー上で秘密キーを使用してサービスと通信し、元のサーバーと同じように動作させることができます。 これには、システム割り当て ID と、その ID がアクセスできるすべてのリソースへのアクセス権の取得が含まれます。 秘密キー ファイルは、読み取りのために **HIMDS** アカウントのアクセスのみを許可するように保護されています。 オフライン攻撃を防ぐために、サーバーのオペレーティング システムのボリュームでディスク全体の暗号化 (BitLocker、dm-crypt など) を使用することを強くお勧めします。

## <a name="next-steps"></a>次のステップ

* 複数のハイブリッド マシンにまたがって Azure Arc 対応サーバーを評価または有効にする前に、[Connected Machine エージェントの概要](agent-overview.md)に関するページを確認して、要件、エージェントに関する技術的な詳細、デプロイ方法を理解してください。

* [計画と展開ガイド](plan-at-scale-deployment.md)を参照して、任意の規模で Azure Arc 対応サーバーをデプロイし、一元的な管理と監視を実装する計画を立ててください。
