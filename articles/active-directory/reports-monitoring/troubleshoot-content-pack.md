---
title: Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う | Microsoft Docs
description: Azure Active Directory アクティビティ ログ コンテンツ パックのエラー メッセージの一覧とエラーを修正するための手順を示します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782176"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う 

|  |
|--|
|現在、Azure AD Power BI コンテンツ パックでは Azure AD Graph API を使用して Azure AD テナントからデータを取得します。 そのため、コンテンツ パック内のデータと[レポート用の Microsoft Graph API](concept-reporting-api.md) を使用して取得したデータに差異が発生する可能性があります。 |
|  |

Power BI Content Pack for Azure Active Directory (プレビュー) を操作しているときに、次のエラーが発生する可能性があります。 

- [更新が失敗しました](troubleshoot-content-pack.md#refresh-failed) 
- [データ ソースの資格情報を更新できませんでした](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [データのインポートに時間がかかっています](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
この記事では、考えられる原因とこれらのエラーを修正する方法について説明します。
 
## <a name="refresh-failed"></a>更新が失敗しました 
 
**このエラーがどのように表面化するか**: Power BI からの電子メールまたは更新履歴の失敗状態。 


| 原因 | 修正方法 |
| ---   | ---        |
| 更新失敗エラーは、コンテンツ パックに接続するユーザーの資格情報がリセットされたが、コンテンツ パックの接続設定が更新されていないときに発生することがあります。 | Power BI で、Azure Active Directory アクティビティ ログ ダッシュボード (Azure Active Directory のアクティビティ ログ) に対応するデータセットを検索し、[更新のスケジュール設定] を選択し、Azure AD 資格情報を入力します。 |
| 更新は、基になるコンテンツ パックのデータの問題が原因で失敗することがあります。 | サポート チケットを提出します。 詳細については、「[Azure Active Directory のサポートを得る方法](../fundamentals/active-directory-troubleshooting-support-howto.md)」を参照してください。|
 
 
## <a name="failed-to-update-data-source-credentials"></a>データ ソースの資格情報を更新できませんでした 
 
**このエラーがどのように表面化するか**: Power BI で、Azure Active Directory アクティビティ ログ (プレビュー) コンテンツ パックに接続するとき。 

| 原因 | 修正方法 |
| ---   | ---        |
| 接続するユーザーが、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれでもありません。 | コンテンツ パックにアクセスするには、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれかのアカウントを使用します。 |
| テナントが Premium テナントでないか、Premium ライセンス ファイルに少なくとも 1 人のユーザーが登録されていません。 | サポート チケットを提出します。 詳細については、「[Azure Active Directory のサポートを得る方法](../fundamentals/active-directory-troubleshooting-support-howto.md)」を参照してください。|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>データのインポートに時間がかかっています 
 
**このエラーがどのように表面化するか**: Power BI でコンテンツ パックに接続すると、Azure Active Directory アクティビティ ログ用のダッシュボードを準備するためのデータ インポート処理が開始されます。 *[データのインポート中...]* というメッセージが表示されます。  

| 原因 | 修正方法 |
| ---   | ---        |
| テナントのサイズに応じて、この手順は数分から 30 分かかることがあります。 | しばらくお待ちください。 1 時間以内にメッセージがダッシュボードの表示に変わらない場合は、サポート チケットを提出してください。 詳細については、「[Azure Active Directory のサポートを得る方法](../fundamentals/active-directory-troubleshooting-support-howto.md)」を参照してください。|

## <a name="next-steps"></a>次の手順

Power BI Content Pack for Azure Active Directory (プレビュー) をインストールするには、[こちら](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/)をクリックします。


