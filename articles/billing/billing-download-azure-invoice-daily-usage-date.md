---
title: "Azure の請求書と毎日の使用状況データをダウンロードする | Microsoft Docs"
description: "Azure の請求書と毎日の使用状況データをダウンロードまたは表示する方法について説明します。"
keywords: "請求書,請求書のダウンロード,Azure の請求書,Azure の使用状況"
services: 
documentationcenter: 
author: genlin
manager: tonguyen
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01057dd7832cab6121a22e3bb7d0dc4cd7e9b35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure の請求書と毎日の使用状況データをダウンロードまたは表示する
請求書を [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) からダウンロードするか、メールで送信することができます。 毎日の使用状況をダウンロードするには、[Azure アカウント センター](https://account.azure.com/Subscriptions)にアクセスします。 請求書と使用状況データを取得するアクセス許可を持つのは、アカウント管理者などの特定のロールのみです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](billing-manage-access.md)に関するページをご覧ください。

>[!NOTE]
>この記事は、Enterprise Agreement (EA) のお客様には適用されません。 EA のお客様でない場合、請求書が登録管理者に直接送信されます。

## <a name="get-your-invoice-in-email-pdf"></a>メールで請求書を入手する (.pdf)
オプトインして、Azure の請求書を電子メールで受取る受信者を追加設定することができます。 この機能は、サポート プラン、エンタープライズ契約、Azure イン オープン プランなどの特定のサブスクリプションでは利用できない場合があります。

1. [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。 自分が所有するサブスクリプションごとにオプトインします。 **[請求書]** をクリックし、**[請求書を送信する]** をクリックします。 

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. **[オプトイン]** をクリックして、条項に同意します。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 契約書に同意したら、追加の受信者を構成できます。

    ![オプトイン フローを示すスクリーン ショット](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
以上の手順を実行してもメールが届かない場合は、[プロファイルの通信設定](https://account.windowsazure.com/profile)のメール アドレスが正しいことを確認してください。

## <a name="download-invoice-from-azure-portal-pdf"></a>Azure Portal から請求書をダウンロードする (.pdf)

1. Azure ポータルの [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページから、[請求書へのアクセス権を持つユーザー](billing-manage-access.md)として自分のサブスクリプションを選択します。

2. **[請求書]** を選択します。 

    ![Screenshot that shows the Billing & usage option](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. PDF 請求書のコピーを表示するには、**[請求書のダウンロード]** をクリックします。 "**使用できません**" というメッセージが表示される場合は、「[前回の請求期間の請求書が表示されない理由](#noinvoice)」を参照してください。

    ![Screenshot that shows billing periods, the download option, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 請求期間をクリックして、毎日の使用状況を表示することもできます。 

請求書の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md)」をご覧ください。

## <a name="download-usage-from-the-account-center-csv"></a>アカウント センターから使用状況をダウンロードする (.csv)

1. アカウント管理者として [Azure アカウント センター](https://account.windowsazure.com/subscriptions)にサインインします。

2. 請求書と使用状況を確認するサブスクリプションを選択します。

3. **[請求履歴]**を選択します。 

    ![Screenshot that shows billing history option](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 過去 6 回の請求期間と、現行の未請求期間の明細も確認できます。 

    ![Screenshot that shows billing periods, options to download invoice and daily usage, and total charges for each billing period](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 見積もりを作成した時点での料金の見積もりを表示するには、**[現在の明細書の表示]** を選択します。 この情報は毎日更新されますが、すべての累計使用量が含まれていない可能性があります。 その月の実際の請求書は、この見積もりとは異なる場合があります。

    ![Screenshot that shows the View Current Statement option](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot that shows the estimate of current charges](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. CSV ファイル形式で毎日の使用状況データをダウンロードするには、**[利用状況のダウンロード]** を選択します。 バージョンが 2 つある場合は、バージョン 2 をダウンロードします。

    ![Screenshot that shows the Download Usage option](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Azure アカウント センターにアクセスできるのは、アカウント管理者のみです。 所有者などの他の課金管理者は、[課金 API](billing-usage-rate-card-overview.md) を使用して使用状況の情報を取得できます。

毎日の使用状況の詳細については、「[Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md)」をご覧ください。

## <a name="noinvoice"></a> 前回の請求期間の請求書が表示されない理由

請求書が表示されない理由はいくつか考えられます。

- サブスクリプションの月単位のクレジット額がまだ残っているか、無料試用版がある場合。 請求書は、負債額がある場合にのみ生成されます。

- Azure のサブスクリプションを開始してから 30 日以上経過していない。

- 請求書がまだ生成されていない。 請求期間の終了までお待ちください。

- アカウント管理者以外のユーザーには、古い請求書は利用できません。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。

