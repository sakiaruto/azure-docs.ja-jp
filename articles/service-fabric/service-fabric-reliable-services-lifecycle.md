---
title: "Azure Service Fabric Reliable Services のライフサイクルの概要 | Microsoft Docs"
description: "Service Fabric Reliable Services のさまざまなライフサイクル イベントに関する説明"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d5977a79cfe4016d6bd943cecb22edadc0eaec6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Reliable Services のライフサイクルの概要
> [!div class="op_single_selector"]
> * [Windows での C# ](service-fabric-reliable-services-lifecycle.md)
> * [Linux での Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services のライフサイクルについて考える際には、ライフサイクルの基本が最も重要です。 一般的には次のとおりです。

- スタートアップ時
  - サービスが構築される
  - サービスは 0 個以上のリスナーを構築して返すことができる
  - 返されたリスナーはすべて開かれて、サービスとの通信が許可される
  - サービスの RunAsync メソッドが呼び出され、ロング ランやバックグラウンド作業が可能になる
- シャットダウン時
  - RunAsync に渡されるキャンセル トークンは取り消され、リスナーが閉じられる
  - 上記の完了後、サービス オブジェクト自体が破棄される

これらのイベントの順序には、詳細な決まりがあります。 特に、イベントの順序は Reliable Services がステートレスかステートフルであるかによって若干異なる場合があります。 さらに、ステートフル サービスでは、プライマリ スワップのシナリオに対処する必要があります。 このシーケンスでは、サービスがシャットダウンすることなく、プライマリのロールが別のレプリカに転送 (または別のレプリカから復帰) されます。 最後に、エラーまたは失敗の条件を考慮する必要があります。

## <a name="stateless-service-startup"></a>ステートレス サービスのスタートアップ
ステートレス サービスのライフサイクルは、非常にわかりやすいものです。 イベントの順序は次の通りです。

1. サービスが構築されます。
2. 次に、2 つのことが並行して行われます。
    - `StatelessService.CreateServiceInstanceListeners()` が呼び出され、返されたリスナーはすべて開かれます。 `ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます。
    - サービスの `StatelessService.RunAsync()` メソッドが呼び出されます。
3. 存在する場合は、サービスの `StatelessService.OnOpenAsync()` メソッドが呼び出されます。 このオーバーライドは一般的ではありませんが利用可能です。

また、リスナーを作成し開く呼び出しと RunAsync の順番は調整されないことに注意してください。 RunAsync が開始する前にリスナーが開くことがあります。 同様に、通信リスナーが開く前、または構築される前に RunAsync が呼び出される場合もあります。 同期が必要な場合、実装者がこれを行います。 一般的な解決策は次のとおりです。

  - リスナーは、別の情報が作成されるか作業が完了するまで機能しないことがあります。 ステートレス サービスの場合、通常、この作業は次のように他の場所で行うことができます。 
    - サービスのコンストラクター内
    - `CreateServiceInstanceListeners()` 呼び出し時
    - リスナー自体を作成する際
  - RunAsync のコードをリスナーが開くまで起動しないようにする必要のある場合があります。 この場合、追加の調整が必要です。 1 つの一般的な解決策としては、リスナー内にフラグを設定して、リスナーがいつ完了したかを知らせるようにします。 RunAsync 内でこのフラグをチェックしてから、実際の作業を続行します。

## <a name="stateless-service-shutdown"></a>ステートレス サービスのシャットダウン
ステートレス サービスをシャット ダウンするときは、同じパターンが逆の順序で実行されます。

1. 次のことが並行して行われます。
    - 開いているすべてのリスナーが閉じられます。 `ICommunicationListener.CloseAsync()` が各リスナーに呼び出されます。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの`ThrowIfCancellationRequested` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると、サービスの `StatelessService.OnCloseAsync()` メソッドが呼び出されます (存在する場合)。 `StatelessService.OnCloseAsync()` のオーバーライドも一般的ではありません。
3. `StatelessService.OnCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-startup"></a>ステートフル サービスのスタートアップ
ステートフル サービスのパターンはステートレス サービスに似ていますが、変更点がいくつかあります。 ステートフル サービスを開始するときのイベントの順序は次の通りです。

1. サービスが構築されます。
2. `StatefulServiceBase.OnOpenAsync()` が呼び出されます これがサービスでオーバーライドされることはほとんどありません。
3. 次のことが並行して行われます。
    - `StatefulServiceBase.CreateServiceReplicaListeners()` が呼び出されます。 
      - サービスがプライマリである場合、返されるすべてのリスナーは開かれます。 `ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます。
      - サービスがセカンダリである場合、`ListenOnSecondary = true` とマークされているリスナーのみが開かれます。 開いているリスナーがセカンダリにあるのは、あまり一般的ではありません。
    - サービスが現在プライマリである場合、サービスの `StatefulServiceBase.RunAsync()` メソッドが呼び出されます。
4. すべてのレプリカ リスナーの `OpenAsync()` 呼び出しが完了して、`RunAsync()` が呼び出されると、`StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 これがサービスでオーバーライドされることはほとんどありません。

ステートレス サービスと同様に、リスナーが作成され開かれる順序と RunAsync がいつ呼び出されるかは調整されません。 調整が必要な場合、解決策はほぼ同じです。 1 つ追加のケースがあります。通信リスナーが受信する呼び出しで、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) に保管されている情報が必要です。 Reliable Collection が読み込み可能または書き込み可能になる前、および RunAsync が起動する前に通信リスナーが開く可能性があるため、追加の調整が必要となります。 最も簡単で一般的なソリューションは、クライアントに要求のリトライを知らせるエラー コードを通信リスナーが返すことです。

## <a name="stateful-service-shutdown"></a>ステートフル サービスのシャットダウン
ステートレス サービスと同様に、シャットダウン中のライフサイクル イベントは起動時と同じですが、順序が逆です。 ステートフル サービスのシャットダウン時には、次のイベントが発生します。

1. 次のことが並行して行われます。
    - 開いているすべてのリスナーが閉じられます。 `ICommunicationListener.CloseAsync()` が各リスナーに呼び出されます。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの`ThrowIfCancellationRequested` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると、サービスの `StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 (これがサービスでオーバーライドされることはほとんどありません)。
    - RunAsync が完了するまで待機する必要があるのは、このサービス レプリカがプライマリである場合のみです。
3. `StatefulServiceBase.OnChangeRoleAsync()` メソッドが完了すると、`StatefulServiceBase.OnCloseAsync()` メソッドが呼び出されます。 このオーバーライドは一般的ではありませんが利用可能です。
3. `StatefulServiceBase.OnCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-primary-swaps"></a>ステートフル サービスのプライマリ スワップ
ステートフル サービスの実行中、通信リスナーを開き、RunAsync メソッドを呼び出すのはそのステートフル サービスのプライマリ レプリカだけです。 セカンダリは構築されますが、それ以上の呼び出しは行われません。 ただし、ステートフル サービスの実行中に、現在プライマリであるレプリカが変更される場合があります。 レプリカに生じるライフサイクル イベントの観点から見ると、これはどういうことでしょうか。 ステートフルなレプリカに起こる動作は、それがスワップ中に降格されているレプリカであるか、昇格されているものかによって変わります。

### <a name="for-the-primary-being-demoted"></a>降格されているプライマリの場合
Service Fabric では、このレプリカでメッセージの処理を終了して、途中のバックグラウンド作業をすべて終了する必要があります。 その結果、この手順はサービスのシャットダウン時と似たものになります。 1 つ違う点は、サービスがセカンダリのままであるために、サービスが破棄されたり閉じられたりしないということです。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - 開いているすべてのリスナーが閉じられます。 `ICommunicationListener.CloseAsync()` が各リスナーに呼び出されます。
    - `RunAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `IsCancellationRequested` プロパティをチェックすると true が返され、呼び出された場合、トークンの`ThrowIfCancellationRequested` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `CloseAsync()` が完了し、`RunAsync()` も完了すると、サービスの `StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 これがサービスでオーバーライドされることはほとんどありません。

### <a name="for-the-secondary-being-promoted"></a>昇格されているセカンダリの場合
同様に、Service Fabric では、このレプリカで送信中のメッセージのリッスンを開始して、関連するバックグラウンド タスクをすべて開始する必要があります。 その結果、レプリカ自体が既に存在している点以外、この手順はサービスのシャットダウン時と似たものになります。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - `StatefulServiceBase.CreateServiceReplicaListeners()` が呼び出され、返されたリスナーはすべて開かれます。 `ICommunicationListener.OpenAsync()` が各リスナーに呼び出されます。
    - サービスの `StatefulServiceBase.RunAsync()` メソッドが呼び出されます。
2. すべてのレプリカ リスナーの `OpenAsync()` 呼び出しが完了して、`RunAsync()` が呼び出されると、`StatefulServiceBase.OnChangeRoleAsync()` が呼び出されます。 これがサービスでオーバーライドされることはほとんどありません。

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>ステートフル サービスのシャットダウンとプライマリの降格における一般的な問題
Service Fabric は、さまざまな理由でステートフル サービスのプライマリを変更します。 最も一般的な理由は、[クラスターの再分散](service-fabric-cluster-resource-manager-balancing.md)と[アプリケーションのアップグレード](service-fabric-application-upgrade.md)です。 これらの操作の際 (および通常のサービス シャットダウンの際にサービスが削除されたかどうかを確認する場合) には、サービスが `CancellationToken` に対応することが重要です。 キャンセルを適切に処理しないサービスでは、いくつかの問題が発生します。 具体的には、それらの操作の速度が遅くなります。サービスが正常に停止するのを Service Fabric が待機するためです。 最終的には、アップグレードがタイムアウトしてロールバックし、アップグレードに失敗することにつながります。 キャンセル トークンに対応しないことにより、クラスターが不均衡になることもあります。ノードの負荷が高くなってもサービスの移動に時間がかかるためサービスを分散できないためです。 

サービスがステートフルなため、サービスが [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) を使用する可能性も高くなります。 Service Fabric では、プライマリが降格されたときに最初に発生することの 1 つは、基になる状態への書き込みアクセスの取り消しです。 これは、サービスのライフサイクルに影響を与える第 2 の一連の問題につながります。 レプリカが移動されるのかシャットダウンされるのかとそのタイミングに基づいて、コレクションが例外を返します。 これらの例外を正しく処理する必要があります。 Service Fabric によってスローされる例外は、永続的なカテゴリ [(`FabricException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) と一時的なカテゴリ [(`FabricTransientException`)](https://docs.microsoft.com/en-us/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) に分類されます。 永続的な例外はログに記録されてスローされますが、一時的な例外は再試行ロジックに基づいて再試行される場合があります。

`ReliableCollections` とサービスのライフサイクル イベントを組み合わせて使用した結果の例外を処理することは、Reliable Service のテストと検証の重要な部分です。 運用環境にデプロイする前にアップグレードと [chaos テスト](service-fabric-controlled-chaos.md)を実行するとき、常にサービスを負荷の下で実行することをお勧めします。 このような基本の手順を行うことで、サービスが正しく実装され、ライフサイクル イベントを正しく処理できるようになります。


## <a name="notes-on-service-lifecycle"></a>サービスのライフサイクルに関するメモ
  - `RunAsync()` メソッドおよび `CreateServiceReplicaListeners/CreateServiceInstanceListeners` の呼び出しは両方とも省略可能です。 サービスには、これらのいずれかまたは両方を含めることも、どちらも含めないこともできます。 例えば、サービスがユーザーの呼び出しに応答してすべての処理を実行する場合、`RunAsync()` を実装する必要はありません。 通信リスナーおよびそれに関連付けられたコードのみが必要です。 同様に、サービスにはバックグラウンドの作業しかない場合があり、この場合 `RunAsync()` の実装のみが必要なため、通信リスナーを作成して返す必要はありません。
  - サービスは、正常に `RunAsync()` を完了して戻ることができます。 完了は、エラー条件ではありません。 `RunAsync()` の完了は、サービスのバックグラウンド処理が完了したことを示します。 ステートフル リライアブル サービスで、レプリカがプライマリからセカンダリへ一度降格して再度プライマリに昇格した場合、`RunAsync()` が再度呼び出されます。
  - サービスが予期しない例外をスローして `RunAsync()` を終了した場合はエラーです。 サービス オブジェクトがシャットダウンされ、正常性エラーが報告されます。
  - これらのメソッドから制御が戻るときに時間制限はありませんが、Reliable Collection への書き込みはすぐにできなくなるため、実際の処理を完了できません。 取り消し要求を受信したらできるだけ短時間で制御が戻るようにすることをお勧めします。 サービスが適切な時間内にこれらの API 呼び出しに応答しない場合、Service Fabric はサービスを強制的に終了する場合があります。 通常、これはアプリケーションのアップグレード中か、サービスの削除中のみに発生します。 このタイムアウト時間は既定で 15 分です。
  - `OnCloseAsync()` パスで障害が起きると、`OnAbort()` が呼び出されます。これは、サービスが要求したリソースすべてをクリーンアップし解放するための最後のベストエフォートの機会になります。

## <a name="next-steps"></a>次のステップ
- [Reliable Services 入門](service-fabric-reliable-services-introduction.md)
- [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
- [Reliable Services の詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)
- [レプリカとインスタンス](service-fabric-concepts-replica-lifecycle.md)