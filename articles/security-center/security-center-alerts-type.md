---
title: "Azure Security Center における各種のセキュリティ アラート | Microsoft Docs"
description: "この記事では、Azure Security Center で利用できるさまざまなセキュリティ アラートについて説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 274c50dad9b8a1d79a71a29b04cb8e44ad91893c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Azure Security Center のセキュリティ アラートの概要
この記事では、Azure Security Center で利用できるさまざまなセキュリティ アラートと関連する分析情報についてわかりやすく説明します。 アラートとインシデントを管理する方法の詳細については、「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」を参照してください。

高度な検出をセットアップする場合には、Azure Security Center Standard にアップグレードする必要があります。 60 日間の無料試用版が提供されています。 アップグレードするには、[[セキュリティ ポリシー]](security-center-policies.md) で **[価格レベル]** を選択します。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

> [!NOTE]
> Security Center は、制限付きプレビューに対して、Linux マシン上の悪意のある行為を検出するために、共通の監査フレームワークである監査レコードを活用した一連の新しい検出機能をリリースしました。 プレビューに参加するには、サブスクリプション ID を記入した電子メールを[こちら](mailto:ASC_linuxdetections@microsoft.com)に送信してください。

## <a name="what-type-of-alerts-are-available"></a>使用できる警告の種類
Azure Security Center では、さまざまな[検出機能](security-center-detection-capabilities.md)を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 こうした情報は、脅威を検出するために使用された分析の種類によって異なります。 インシデントには、脅威を調査する際に活用できる追加のコンテキスト情報も含まれる可能性があります。  この記事では、次の警告の種類について説明します。

* 仮想マシンの動作分析 (VMBA)
* ネットワーク分析
* リソース分析
* コンテキスト情報

## <a name="virtual-machine-behavioral-analysis"></a>仮想マシンの動作分析
Azure Security Center は動作分析を使用し、仮想マシンのイベント ログの分析に基づいて、侵害の発生したリソースを特定します。 分析対象となるイベントには、プロセス作成イベント、ログイン イベントなどがあります。 また、他のシグナルとの間には、蔓延している攻撃の裏付けとなる兆候を確認できる相関関係が存在します。

> [!NOTE]
> Security Center の検出機能に関する詳細については、「[Azure Security Center の検出機能](security-center-detection-capabilities.md)」を参照してください。
>

### <a name="crash-analysis"></a>クラッシュ分析
クラッシュ ダンプ メモリ分析は、従来のセキュリティ ソリューションを回避することができる高度なマルウェアの検出に使用される方法です。 さまざまな形式のマルウェアは、ディスクへの書き込みを行わないことや、ディスクに書き込まれたソフトウェア コンポーネントを暗号化することで、ウイルス対策製品によって検出される可能性を減らすよう試みます。 この手法が、従来のマルウェア対策手法を使ったマルウェア検出を困難にしています。 しかし、マルウェアが機能するためにはメモリに痕跡を残す必要があるので、メモリ分析を使用すればそのようなマルウェアを検出することができます。

ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。 クラッシュは、マルウェア、一般的なアプリケーション、またはシステムの問題によって引き起こされる可能性があります。 Security Center では、クラッシュ ダンプでメモリを分析することによって、ソフトウェアの脆弱性の悪用や機密データへのアクセスに使用された手法を検出できます。また、侵入したコンピューターに密かに常駐するタイプの手法であっても検出が可能です。 Security Center バックエンドによって分析が実行されるため、この方法ではホストのパフォーマンスに対する影響が最小限に抑えられます。

以下のフィールドは、この記事で後ほど紹介するクラッシュ ダンプ アラートの例に共通して存在するものです。

* DUMPFILE (ダンプ ファイル): クラッシュ ダンプ ファイルの名前。
* PROCESSNAME (プロセス名): クラッシュしているプロセスの名前。
* PROCESSVERSION (プロセスのバージョン): クラッシュしているプロセスのバージョン。

### <a name="shellcode-discovered"></a>シェルコードの検出
シェルコードは、マルウェアがソフトウェアの脆弱性を突破した後に実行されるペイロードです。 このアラートは、悪意のあるペイロードに共通した振る舞いをする実行可能コードがクラッシュ ダンプ分析によって検出されたことを意味します。 悪意のないソフトウェアによる振る舞いである可能性もありますが、ソフトウェア開発の標準的な慣例からは逸脱しています。

このシェルコードに関するアラートの例では、ほかにも次のフィールドがあります。

* ADDRESS (アドレス): メモリ内におけるシェルコードの場所。

この種類のアラートの例を次に示します。

![Shellcode alert](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>モジュールのハイジャックの検出
Windows では、システムに共通の機能をソフトウェアから利用できるようにするために、ダイナミック リンク ライブラリ (DLL) を使用しています。 DLL ハイジャックは、マルウェアが DLL の読み込み順序を改変することによって行われます。このときメモリに悪質なペイロードが読み込まれ、任意のコードを実行できる状態となります。 このアラートは、クラッシュ ダンプ分析により、よく似た名前のモジュールが 2 つの異なるパスから読み込まれている状態が検出されたことを示しています。 パスの 1 つは、一般的な Windows システム バイナリが置かれた場所です。

悪意からではなく、インストルメント化や Windows OS の拡張、Windows アプリケーションの拡張といった目的で、通常のソフトウェア開発者が DLL の読み込み順序を変更することも皆無ではありません。 DLL の読み込み順序に対する悪意のある変更と、無害である可能性の高い変更とを判別しやすいように、Azure Security Center では、読み込まれているモジュールに疑わしい特徴が見られるかどうかをチェックします。 このチェックの結果は、警告の "SIGNATURE (シグネチャ)" フィールドに表示され、警告の重大度、説明、修正手順に反映されます。 モジュールが正当なものか、それとも悪意のあるものかを調査するには、ハイジャック モジュールのディスク コピーを分析します。 たとえば、ファイルのデジタル署名を検証したり、ウイルス対策スキャンを実行したりする方法が考えられます。

このアラートには、「シェルコードの検出」のセクションで取り上げた共通のフィールドに加えて次のフィールドがあります。

* SIGNATURE (シグネチャ): ハイジャック モジュールが、疑わしい動作のプロファイルと一致しているかどうかを示します。
* HIJACKEDMODULE (ハイジャックされたモジュール): ハイジャックされた Windows システム モジュールの名前。
* HIJACKEDMODULEPATH (ハイジャックされたモジュールのパス): ハイジャックされた Windows システム モジュールのパス。
* HIJACKINGMODULEPATH (ハイジャック モジュールのパス): ハイジャック モジュールのパス。

この種類のアラートの例を次に示します。

![Module hijacking alert](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>Windows モジュールのなりすましの検出
マルウェアには、Windows システムに共通するバイナリ名 (SVCHOST.EXE など) やモジュール名 (NTDLL.DLL など) が使用されている場合があります。悪質なソフトウェアを "*紛れ込ませる*" ことで、その正体をシステム管理者に見破られないようにするためです。 このアラートは、Windows システムのモジュール名と同じ名前のモジュールがクラッシュ ダンプ ファイルに含まれているものの、それ以外の点は一般的な Windows モジュールの基準をそのモジュールは満たしていないことが、クラッシュ ダンプ分析によって検出されたことを意味します。 そのモジュールが悪意のあるものかどうかについては、ディスク上のなりすましモジュールのコピーを分析することによって、さらに詳しい情報を得ることができます。 実行される分析の例を次に示します。

* 問題になっているファイルが正規ソフトウェア パッケージの一部として出荷されていること確認する。
* ファイルのデジタル署名を確認する。
* ファイルに対してウイルス対策スキャンを実行する。

このアラートには、「シェルコードの検出」のセクションで取り上げた共通のフィールドに加えて次のフィールドがあります。

* DETAILS (詳細): モジュールのメタデータが有効であるかどうかと、モジュールがシステム パスから読み込まれたかどうかを表します。
* NAME (名前): Windows のモジュールになりすましているモジュールの名前。
* PATH (パス): Windows のモジュールになりすましているモジュールのパス。

また、このアラートにはモジュールの PE ヘッダーから特定のフィールド ("CHECKSUM"、"TIMESTAMP" など) が抽出されて表示されます。 これらのフィールドは、モジュールに存在する場合にのみ表示されます。 これらのフィールドの詳細については、「 [Microsoft PE and COFF 仕様](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) 」を参照してください。

この種類のアラートの例を次に示します。

![Masquerading Windows alert](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>システム バイナリの改変の検出
マルウェアは、侵入したシステム上でこっそりデータにアクセスしたり人目に付かないよう常駐したりするために、主要なシステム バイナリを改変する場合があります。 このアラートは、Windows OS の主要なバイナリがメモリ内またはディスク上で改変されたことがクラッシュ ダンプ分析によって検出されたことを意味します。

悪意からではなく、迂回やアプリケーションの互換性といった目的で、通常のソフトウェア開発者がメモリ内のシステム モジュールに変更を加えることも皆無ではありません。 悪意のあるモジュールとそうでないモジュールを判別しやすいよう、Azure Security Center は、改変されたモジュールに疑わしい特徴が見られるかどうかをチェックします。 このチェックの結果は、警告の重大度、説明、修正手順に反映されます。

このアラートには、「シェルコードの検出」のセクションで取り上げた共通のフィールドに加えて次のフィールドがあります。

* MODULENAME (モジュール名): 改変されたシステム バイナリの名前。
* MODULEVERSION (モジュールのバージョン): 改変されたシステム バイナリのバージョン。

この種類のアラートの例を次に示します。

![System binary alert](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>疑わしいプロセスの実行
Security Center は、ターゲット仮想マシンで実行されている疑わしいプロセスを特定し、アラートをトリガーします。 この検出では、具体的な名前ではなく、実行可能ファイルのパラメーターが確認されます。 このため、攻撃者が実行可能ファイルの名前を変更した場合でも、不審なプロセスを検出することができます。

この種類のアラートの例を次に示します。

![Suspicious process alert](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domains-accounts-queried"></a>複数のドメイン アカウントのクエリ
Security Center は、Active Directory ドメイン アカウントに対してクエリが複数回試行されていることを検出できます。このような試行は、通常、ネットワークの偵察時に攻撃者が実行することです。 攻撃者は、この手法を使ってドメインに問い合わせ、ユーザー、ドメインの管理者アカウント、ドメイン コントローラーであるコンピューターを特定できるほか、他のドメインとの潜在的なドメイン信頼関係も特定できます。

この種類のアラートの例を次に示します。

![Multiple domains account alert](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>ローカル管理者グループのメンバーの列挙操作

Windows Server 2016 と Windows 10 でセキュリティ イベント 4798 がトリガーされると、Security Center からアラートがトリガーされます。 このアラートは、ローカル管理者グループが列挙されたときにトリガーされます。この操作は、攻撃者がネットワーク偵察時に実行することが多いためです。 攻撃者は、管理特権を持ったユーザーの ID を照会する目的で、この手法を利用することがあります。

この種類のアラートの例を次に示します。

![ローカル管理者](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>大文字と小文字の変則的な組み合わせ

Security Center は、コマンド ラインで大文字と小文字の組み合わせが使われたことを検出すると、アラートをトリガーします。 大文字と小文字の区別やハッシュベースのマシン ルールから身を隠すために、一部の攻撃者がこの手法を使うことがあります。

この種類のアラートの例を次に示します。

![変則的な組み合わせ](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>Kerberos ゴールデン チケット攻撃の疑い

攻撃者は、不正に入手した [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) キーを使って Kerberos "ゴールデン チケット" を作成し、任意のユーザーになりすます場合があります。 このタイプのアクティビティが検出されると、Security Center からアラートがトリガーされます。

> [!NOTE] 
> Kerberos ゴールデン チケットの詳細については、「[Windows 10 credential theft mitigation guide (Windows 10 資格情報の盗難防止ガイド)](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx)」を参照してください。

この種類のアラートの例を次に示します。

![ゴールデン チケット](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>疑わしいアカウントの作成

既存の組み込み管理特権アカウントと酷似するアカウントが作成されると、Security Center からアラートがトリガーされます。 この手法は、攻撃者が、人の目では気付きにくい不正アカウントを作成する目的で使う場合があります。
 
この種類のアラートの例を次に示します。

![疑わしいアカウント](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>疑わしいファイアウォール規則が作成された

攻撃者は、ホストのセキュリティを迂回するために、ファイアウォール規則を独自に作成しようと試みる場合があります。悪質なアプリケーションがコマンド アンド コントロール サーバーと通信を行ったり、侵入済みのホストを介してネットワーク経由で攻撃をしかけたりするのがその目的です。 Security Center は、疑わしい場所にある実行可能ファイルから新しいファイアウォール規則が作成されたことを検出すると、アラートをトリガーします。
 
この種類のアラートの例を次に示します。

![ファイアウォール規則](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>HTA と PowerShell の疑わしい組み合わせ

Security Center は、Microsoft HTML アプリケーション ホスト (HTA) が PowerShell のコマンドを起動しようとしていることを検出するとアラートをトリガーします。 これは攻撃者が悪質な PowerShell スクリプトを起動するときに使う手法です。
 
この種類のアラートの例を次に示します。

![HTA と PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>ネットワーク分析
Security Center のネットワーク脅威検出は、Azure IPFIX (Internet Protocol Flow Information Export) トラフィックからセキュリティ情報を自動的に収集することによって機能します。 この情報を分析し、ときには複数の情報源から得た情報との関連性を探りながら、脅威を特定します。

### <a name="suspicious-outgoing-traffic-detected"></a>疑わしい送信トラフィックの検出
ネットワーク デバイスは、他の種類のシステムとほぼ同じように検出、プロファイリングされることがあります。 通常、攻撃者は初めにポート スキャンまたはポート スイープを行います。 次の例では、ある VM から不審な Secure Shell (SSH) トラフィックが発生しています。 このシナリオでは、外部リソースに対する SSH ブルート フォース攻撃またはポート スイープ攻撃が考えられます。

![Suspicious outgoing traffic alert](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

このアラートには、この攻撃を開始するにあたり使用されたリソースを特定する際に役立つ情報が表示されます。 また、セキュリティが侵害されたコンピューター、検出の日時、使用されたプロトコルとポートの特定に役立つ情報も確認できます。 このページには、この問題を軽減するために使用できる修復手順の一覧も表示されます。

### <a name="network-communication-with-a-malicious-machine"></a>悪意のあるコンピューターとのネットワーク通信
Azure Security Center は、悪意のある IP アドレスと通信している、セキュリティが侵害されたコンピューターを、Microsoft 脅威インテリジェンス フィードを使用して検出できます。 悪意のあるアドレスは多くの場合、コマンド アンド コントロール センターです。 この例では、Security Center によって、Pony Loader マルウェア ([Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF) とも呼ばれる) を使用した通信が行われたことが検出されました。

![network communication alert](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

このアラートでは、この攻撃の開始に使用されたリソース、攻撃されたリソース、被害を受けた IP、攻撃者の IP、検出の日時を特定できる情報が表示されます。

> [!NOTE]
> ライブ IP アドレスは、プライバシー保護の目的でこのスクリーンショットからは削除しています。
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>送信サービス拒否攻撃の可能性の検出
ある仮想マシンによる異常なネットワーク トラフィックが原因となって、Security Center が潜在的なサービス拒否型の攻撃をトリガーする場合があります。

この種類のアラートの例を次に示します。

![Outgoing DOS](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>リソース分析
Security Center のリソース分析は、[Azure SQL Database の脅威の検出](../sql-database/sql-database-threat-detection.md)機能との統合など、サービスとしてのプラットフォーム (PaaS) サービスに重点を置いています。 これらの領域の分析結果に基づいて、Security Center はリソースに関連するアラートをトリガーします。

### <a name="potential-sql-injection"></a>SQL インジェクションの可能性
SQL インジェクションとは、後で SQL Server のインスタンスに渡して解析と実行の対象とする文字列に、悪意のあるコードが挿入される攻撃です。 SQL Server は受け取った有効な構文のクエリをすべて実行してしまうため、SQL ステートメントを構成するすべてのプロシージャにおいて、挿入に対する脆弱性を確認する必要があります。 SQL 脅威の検出では、機械学習、動作分析、異常検出を使用して、Azure SQL データベースで発生しているおそれのある疑わしいイベントを特定します。 For example:

* 以前の従業員によるデータベース アクセスの試行
* SQL インジェクション攻撃
* 在宅中のユーザーからの運用データベースへの不自然なアクセス

![Potential SQL Injection alert](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

このアラートに表示される情報は、攻撃を受けたリソース、検出の日時、攻撃の状態の特定に役立ちます。 また、さらに詳しい調査をするための手順を説明したリンクも表示されます。

### <a name="vulnerability-to-sql-injection"></a>SQL インジェクションにつながる脆弱性
このアラートは、データベースでアプリケーション エラーが検出された場合にトリガーされます。 このアラートは、SQL インジェクション攻撃に対する脆弱性が存在する可能性を示すものです。

![Potential SQL Injection alert](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>不明な場所からの不自然なアクセス
このアラートは、最後の期間に見られなかった不明な IP アドレスからのアクセス イベントがサーバーで検出された場合にトリガーされます。

![Unusual access alert](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>コンテキスト情報
アナリストが調査を行い、脅威の性質とそれを軽減する方法を判断するためには、追加のコンテキストが必要になります。  たとえば、ネットワーク異常が検出された際に、ネットワーク上で発生していたその他のイベントや対象となっているリソースを把握できなければ、対処法を決めることは困難です。 この判断を助けるため、セキュリティ インシデントにはアーティファクトや関連イベントのほか、調査に役立つ情報が含まれています。 利用できる追加情報は、検出された脅威の種類や環境内の構成によって異なります。また、すべてのセキュリティ インシデントで追加情報が利用できるとは限りません。

追加情報が利用可能な場合は、セキュリティ インシデントの警告の一覧の下に表示されます。 次のような情報が含まれます。

- ログの消去イベント
- 不明なデバイスからの PNP デバイスの接続
- 対応できないアラート 

![Unusual access alert](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>関連項目
この記事では、Security Center のさまざまな種類のセキュリティ アラートについて説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ インシデントの処理](security-center-incident.md)
* [Azure Security Center の検出機能](security-center-detection-capabilities.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」: このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
