---
title: Azure Video Analyzer を使用したマネージド ID
description: このトピックでは、Azure Video Analyzer でマネージド ID を使用する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 46d3936c1822b4409dad8ad373352b062c6c9b77
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852771"
---
# <a name="managed-identity"></a>マネージド ID

異なるサービス間でやり取りされる通信のセキュリティを確保するシークレットと資格情報の管理は、開発者に共通の課題です。 Azure のマネージド ID を使用すれば、開発者が資格情報を管理する必要がなくなります。Azure リソースの ID は Azure Active Directory (Azure AD) から提供され、その ID を使用して Azure AD トークンが取得されます。

Azure Video Analyzer アカウントを作成する場合は、Azure ストレージ アカウントを関連付ける必要があります。 Video Analyzer を使用してカメラからライブ ビデオを記録する場合、そのデータはストレージ アカウント内のコンテナーに BLOB として格納されます。 必要に応じて、IoT Hub を Video Analyzer アカウントに関連付けることができます。これは、Video Analyzer エッジ モジュールを[透過的なゲートウェイ](./cloud/use-remote-device-adapter.md)として使用する場合に必要です。 次のように、マネージド ID を使用して、Video Analyzer アカウントにストレージ アカウントおよび IoT Hub (ソリューションに必要な場合) への適切なアクセス権を付与する必要があります。

## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Video Analyzer のユーザー割り当てマネージド ID

* [ユーザー割り当てマネージド ID (UAMI)](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) を作成する

> [!NOTE]
> Azure サブスクリプションが必要です。このサブスクリプションでは、[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールと[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)ロールの両方にアクセスできます。このリソースグループには、新しいリソース (ユーザー割り当てマネージド ID、ストレージ アカウント、Video Analyzer アカウント) を作成します。 適切なアクセス許可がない場合は、これらのアクセス許可を付与してもらうようアカウント管理者に依頼してください。 関連づけられるストレージ アカウントは、Video Analyzer アカウントと同じリージョンにある必要があります。 [Standard 汎用 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) ストレージ アカウントを使用することをお勧めします。
また、Video Analyzer アカウントに IoT Hub をアタッチする場合は、その共同作成者ロールにアクセスできる必要もあります。

### <a name="enable-video-analyzer-account-to-access-storage-account"></a>Video Analyzer アカウントでストレージ アカウントにアクセスできるようにする

* Azure ストレージ アカウントを作成します。

* 上記のストレージ アカウントの[ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールと[閲覧者](../../role-based-access-control/built-in-roles.md#reader)ロールをマネージド ID に追加します。

* ユーザー割り当てマネージド ID とストレージ アカウントを関連するプロパティの値として指定して、Video Analyzer アカウントを作成します。

Video Analyzer では、マネージド ID を使用して、ユーザーに代わってストレージ アカウントにアクセスできるようになります。

### <a name="enable-video-analyzer-account-to-access-iot-hub"></a>Video Analyzer アカウントで IoT Hub にアクセスできるようにする

前のセクションでは、Video Analyzer でストレージ アカウントにアクセスできるようにする UAMI を作成しました。 この手順では、UAMI 経由で Video Analyzer アカウントに IoT Hub へのアクセス権を付与した後、IoT Hub を Video Analyzer アカウントにリンクします。 マネージド ID と IoT Hub の連携方法の詳細については、[IoT Hub マネージド ID](../../iot-hub/iot-hub-managed-identity.md) に関する記事を参照してください。

* Azure portal で Video Analyzer 管理ブレードに移動し、左側のペインで **[設定]** の下にある **[IoT Hub]** を選択します。
* 次に、[IoT Hub のアタッチ] ペインで **[アタッチ]** を選択します。 **[IoT Hub のアタッチ]** 構成ペインで、必要なフィールド値を入力します。
    * サブスクリプション - IoT Hub が作成される Azure サブスクリプションの名前を選択します
    * IoT Hub - Video Analyzer アカウントにアタッチする必要がある既存の IoT Hub を選択します
    * マネージド ID - IoT Hub へのアクセスに使用するユーザー割り当てマネージド ID (前のセクションの前半で作成したもの) を選択します
* **[保存]** をクリックして、IoT Hub を Video Analyzer アカウントにリンクします。

上記 の手順 を実行する方法の例については、[この](create-video-analyzer-account.md)記事を参照してください。

## <a name="next-steps"></a>次のステップ

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。
