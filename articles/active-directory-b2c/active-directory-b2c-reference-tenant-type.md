<properties
	pageTitle="Azure Active Directory B2C: 運用スケールとプレビューの B2C テナント | Microsoft Azure"
	description="Azure Active Directory B2C テナントの種類に関するトピック"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: 運用スケールとプレビューの B2C テナント

Azure Active Directory (Azure AD) B2C 上に運用アプリを作成する予定がある場合は、運用するために適したテナントの "種類" があることを確信する必要があります。何があるかを確認するには、この手順に従って、Azure ポータルの [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)し、**[Tenant type (テナントの種類)]** の下を調べてください。

## 概要

Azure AD B2C では、北米の**運用スケール** B2C テナントだけで、運用アプリをサポートしています。

| テナントの種類 | 国/地域 | 一般公開済み |
| ----------- | -------------- | --------------------- |
| **運用スケール テナント** | 北米の国/地域 | はい |
| **運用スケール テナント** | 北米を除くすべての国/地域 | いいえ |
| **プレビュー テナント** | すべての国/地域 | いいえ |

> [AZURE.NOTE]
Azure AD B2C テナント (コンシューマー向け) は、現在、Azure AD テナント (従業員向け) が利用可能な国や地域の一部でご利用いただけません。詳細については、以降のセクションを参照してください。

## 北米の運用スケール B2C テナント

北米 (つまり、米国、カナダ、コスタリカ、ドミニカ共和国、エルサルバドル、グアテマラ、メキシコ、パナマ、プエルトリコ、トリニダード トバゴという国や地域のいずれか) で [B2C テナントを作成](active-directory-b2c-get-started.md)し、B2C 管理 UI の **[Tenant type (テナントの種類)]** が **[Production-scale (運用スケール)]** である場合は、テナントを運用アプリ用に使用できます。

> [AZURE.NOTE]
運用スケール テナントは、テナントごとに数億のコンシューマー ID にまで拡張できます。

![Screen shot of a production-scale tenant](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## すべての国/地域のプレビュー B2C テナント

Azure AD B2C のプレビュー期間中に B2C テナントを作成した場合は、通常、**[Tenant type (テナントの種類)]** が **[Preview tenant (プレビュー テナント)]** になります。その場合は、開発とテストの目的のみにテナントを使用する必要があります。運用アプリ用には使用しないでください。

> [AZURE.IMPORTANT]
プレビュー B2C テナントから運用スケール B2C テナントへの移行パスはありません。プレビュー B2C テナントを削除し、同じドメイン名で運用スケール B2C テナントを再作成する場合に、既知の問題があることに注意してください。運用スケール B2C テナントは異なるドメイン名で作成する必要があります。

![Screen shot of a preview tenant](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## 北米以外の運用スケール B2C テナント

Azure AD B2C は、現在、北米以外では一般公開されていません。ただし、以下のいずれかの国または地域で、開発およびテスト用に運用スケール テナントを作成して使用できます: アルジェリア、オーストリア、アゼルバイジャン、バーレーン、ベラルーシ、ベルギー、ブルガリア、クロアチア、キプロス、チェコ共和国、デンマーク、エジプト、エストニア、フィンランド、フランス、ドイツ、ギリシャ、ハンガリー、アイスランド、アイルランド、イスラエル、イタリア、ヨルダン、カザフスタン、ケニア、クウェート、ラトビア、レバノン、リヒテンシュタイン、リトアニア、ルクセンブルク、マケドニア、マルタ、モンテネグロ、モロッコ、オランダ、ナイジェリア、ノルウェー、オマーン、パキスタン、ポーランド、ポルトガル、カタール、ルーマニア、ロシア、サウジアラビア、セルビア、スロバキア、スロベニア、南アフリカ、スペイン、スウェーデン、スイス、チュニジア、トルコ、ウクライナ、アラブ首長国連邦、英国。

Azure AD B2C によって上記の国または地域での一般公開が発表されると、これらの運用スケール テナントを引き続き使用し、データを失うことなく実稼働アプリと共に稼働することができます。

## B2C テナントの可用性

B2C テナントは、現在、以下の国または地域ではご利用いただけません: アフガニスタン、アルゼンチン、オーストラリア、ブラジル、チリ、コロンビア、エクアドル、香港特別行政区、インド、インドネシア、イラク、日本、韓国、マレーシア、ニュージーランド、パラグアイ、ペルー、フィリピン、シンガポール、スリランカ、台湾、タイ、ウルグアイ、ベネズエラ。今後対応していく予定です。

<!---HONumber=AcomDC_0831_2016-->