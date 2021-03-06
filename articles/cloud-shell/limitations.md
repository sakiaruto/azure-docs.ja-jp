---
title: "Azure Cloud Shell (プレビュー) の制限 | Microsoft Docs"
description: "Azure Cloud Shell の制限の概要"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: fe325cf5fa5e3d4b0a188599de7308cf2008b458
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell の制限
Azure Cloud Shell には、次の既知の制限があります。

## <a name="general-limitations"></a>一般的な制限事項

### <a name="system-state-and-persistence"></a>システム状態と永続化
Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、ファイル共有をマウントする必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:
* マウントされたストレージでは、`clouddrive` ディレクトリ内の変更のみが永続化されます。 Bash では、`$Home` ディレクトリも永続化されます。
* ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
  * Bash では、`env` を実行して、`ACC_LOCATION` として設定されたリージョンを検索します。
* Azure Files は、ローカル冗長ストレージと geo 冗長ストレージのアカウントのみをサポートします。

### <a name="browser-support"></a>ブラウザーのサポート
Cloud Shell では、Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox、および Apple Safari の最新バージョンがサポートされます。 プライベート モードの Safari はサポートされません。

### <a name="copy-and-paste"></a>コピーと貼り付け

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>特定のユーザーがアクティブにできるシェルは 1 つだけである
ユーザーは、一度に 1 種類のシェル (**Bash** または **PowerShell**) だけを起動できます。 ただし、Bash または PowerShell の複数のインスタンスを同時に実行できます。 Bash と PowerShell の間でスワップを行うと、Cloud Shell が再起動し、既存セッションが終了します。

### <a name="usage-limits"></a>Usage limits (使用状況の制限)
Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

## <a name="bash-limitations"></a>Bash の制限事項

### <a name="user-permissions"></a>ユーザーのアクセス許可
権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` ディレクトリ外のインストールはすべて失われます。
`clouddrive` ディレクトリ内の `git clone` などの特定のコマンドには適切なアクセス許可がありませんが、`$Home` ディレクトリにはアクセス許可があります。

### <a name="editing-bashrc"></a>.bashrc の編集
.bashrc を編集すると Cloud Shell で予期しないエラーが発生する可能性があるため、編集には注意が必要です。 

### <a name="bashhistory"></a>.bash_history
bash コマンドの履歴は、Cloud Shell セッションの中断または同時セッションにより、一致していない場合があります。

## <a name="powershell-limitations"></a>PowerShell の制限事項

### <a name="slow-startup-time"></a>起動時間が遅い
Azure Cloud Shell の PowerShell は、プレビュー期間中は、初期化に最大で 60 秒かかる場合があります。

### <a name="no-home-directory-persistence"></a>$Home ディレクトリが永続化されない
アプリケーション (たとえば、git、vim など) によって $Home に書き込まれたデータは、PowerShell のセッション間で保持されません。  回避策については、[こちらをご覧ください](troubleshooting.md#powershell-resolutions)。

### <a name="error-if-azurerm-module-is-updated-from-powershellgallery"></a>PowerShellGallery から AzureRM モジュールを更新する場合のエラー
AzureRM モジュールを最新バージョン (4.4.0) に更新すると、Cloudshell の起動時に次のエラーが表示されることがあります。
``` powershell
VERBOSE: Authenticating to Azure ...
Import-Module : Method 'RemoveUser' in type 'Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory' from assembly 'Microsoft.Azure.PSCloudConsole.ADAuth, Version=0.0.0.0,
Culture=neutral, PublicKeyToken=null' does not have an implementation.
At C:\Program Files\WindowsPowerShell\Modules\PSCloudShellADAuth\PSCloudShellADAuth.psm1:12 char:1
+ Import-Module -Name $AdAuthModulePath -PassThru -Force
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Import-Module], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.PowerShell.Commands.ImportModuleCommand

Unable to find type [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory].
At C:\Users\ContainerAdministrator\PSCloudShellStartup.ps1:94 char:9
+         [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory]::Update ...
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (Microsoft.Azure...h.ADAuthFactory:TypeName) [], RuntimeException
    + FullyQualifiedErrorId : TypeNotFound
```

## <a name="next-steps"></a>次のステップ
[Cloud Shell のトラブルシューティング](troubleshooting.md) <br>
[Bash のクイックスタート](quickstart.md) <br>
[PowerShell のクイックスタート](quickstart-powershell.md)
