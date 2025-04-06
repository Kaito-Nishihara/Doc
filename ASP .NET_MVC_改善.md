## アプリ内共通機能系
### ページングの部分レンダリング化
- 従来のプロジェクト（JICA、持続化等）は**ページ切り替えごとに全画面がリロードされていた**が今回は**部分ビュー（PartialView）と AJAX** を活用し、ページング部分のみを動的に差し替えるように改善

- 利点
  - ページ切り替えがスムーズで、ユーザーの操作感が軽くなった
  - レイアウトを保ったままデータ部分のみ更新

- 改善点
  - Offsetページングを採用しているため大量データになると、後ろのページほどクエリが遅くなる
    - Keyset（Seek）方式に変更することを検討する必要がある（実装難易度が高いことが課題）
    - Redisキャッシュの導入も検討

## `EF系`
### エンティティを非同期で処理を導入
```csharp
//修正前
_dbContext.Add(entity);

//修正後
_dbContext.AddAsync(entity);
```

- 利点
  - **スレッドのブロック回避**
    - I/O 待機中にスレッドをブロックしない。
  - **高トラフィック環境でのパフォーマンス向上**
    - 同時に多くのリクエストを処理する環境で、非同期の恩恵が大きい。

### Unit of Work パターンを導入
- Unit of Work（UoW）パターンは、1つのビジネス操作（ユースケース）に対して、データベース操作を一括して管理・保存する仕組み
- 複数のリポジトリで行われた変更を、**まとめて1つのトランザクションとして確定（またはロールバック）** する

```planintext
[Service Layer]
↓
[UnitOfWork]
├── RepositoryA
└── RepositoryB
↓
[DbContext] ← まとめて SaveChangesAsync()
```
- 利点
  - 一つの操作の中で複数のリポジトリを横断的に利用しやすくなる。
  - UnitOfWork はトランザクション制御に集中（責務の分離）

## Logger系
### AzureStorageへのログの出力をILogger に統合（WebJob限定で導入）
- 以前は`NLog` で直接出力していたが `Microsoft.Extensions.Logging.Abstractions` に依存する形で統合することでロガーの差し替えやテストが容易になる構造を実現

- 利点
  - ILogger<T> を用いることで、NLog, Serilog, Console, Debug など任意のロガーを DI コンテナを通じて差し替えることが可能
  - 標準ライブラリ`Microsoft.Extensions.Logging.Abstractions`を使用して出力している外部のライブラリ（.NET Identity）等からの出力もStorageへ保存可能

## メール系
### メール送信の非同期化
- 利点
  - メール送信を非同期化することによりパフォーマンスの向上

- 改善点 (次回プロジェクトで導入したい)
  - **アプリケーション内からの直接送信をやめ、Azure Function など外部サービスに移譲**を検討する必要がある

- 改善後構成イメージ（例）
```plaintext
[Webアプリ / API]
       ↓ （送信要求メッセージ）
[Azure Queue or Azure Service Bus]
       ↓
[Azure Function (メール送信)]
       ↓
[SMTP / SendGrid / etc.]
```

## コーディング規約系