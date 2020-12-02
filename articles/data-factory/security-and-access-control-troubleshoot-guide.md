---
title: セキュリティとアクセス制御に関するイシューのトラブルシューティング
description: Azure Data Factory でのセキュリティとアクセス制御に関するイシューのトラブルシューティングを行う方法について説明します。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008729"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Azure Data Factory でのセキュリティとアクセス制御に関するイシューのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory でのセキュリティとアクセス制御に対する一般的なトラブルシューティングの方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>パブリック ネットワーク アクセスを無効にした後、認証キーのイシューが無効または空である

#### <a name="symptoms"></a>現象

Data Factory に対するパブリック ネットワーク アクセスを無効にすると、"認証キーが無効または空です" というエラーがセルフホステッド統合ランタイムからスローされます。

#### <a name="cause"></a>原因

パブリック接続を無効にしてプライベート エンドポイントを確立しても再接続の助けにはならないため、この問題の原因として最も考えられるのは DNS 解決のイシューです。

#### <a name="resolution"></a>解決方法

1. ADF の FQDN に対して PsPing を実行したところ、ADF のパブリック エンドポイントにバッファーが、それを無効にした後でも送信されることを検出しました。

1. プライベート IP を FQDN にマップするように VM 内のホスト ファイルを変更し、PsPing をもう一度実行します。 ADF の正しいプライベート IP にバッファーがアクセスできるようになります。

1. セルフホステッド統合ランタイムを再登録すると、それが稼働状態になるのがわかります
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>プライベート リンクが原因で、セルフホステッド VM 上で IR 認証キーを登録できない

#### <a name="symptoms"></a>現象

プライベート リンクが有効になっていることが原因で、セルフホステッド VM 上で IR 認証キーを登録できません。

エラー情報が次のように表示されます。

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>原因

このイシューの原因は、SHIR のインストールが試行されている VM にあると考えられます。 クラウドに接続する場合は、パブリック ネットワーク アクセスを有効にする必要があります。

#### <a name="resolution"></a>解決方法

 **解決策 1:** この問題を解決するには、以下の手順に従ってください。

1. 次のリンクに移動します。 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. **[試してみる]** オプションをクリックし、必要な詳細情報をすべて入力します。 次のプロパティを **[本文]** にも貼り付けます。

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. ページの最後にある **[実行]** をクリックして、関数を実行します。 応答コード 200 が表示されることを確認します。 貼り付けたプロパティは JSON 定義にも表示されます。

1. これで、統合ランタイムでの IR 認証キーの追加を再試行できます。


**解決策 2:** 解決策については、次の記事を参照してください。

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

ユーザー インターフェイスを使用してパブリック ネットワーク アクセスを有効にしてみてください。

![パブリック ネットワーク アクセスを有効にする](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory 用の Azure Private Link](data-factory-private-link.md)
*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)