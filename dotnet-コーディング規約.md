# コーディング規約

.NET C# の[公式スタイルガイド](https://learn.microsoft.com/ja-jp/dotnet/csharp/fundamentals/coding-style/coding-conventions)に準拠する。  
本ドキュメントでは、スタイルガイド内で意見が分かれる点や、プロジェクト固有の規約について明記する。

## 目次

- [コーディング規約](#コーディング規約)
  - [目次](#目次)
  - [1 命名規則](#1-命名規則)
    - [1.1 ファイルの命名](#11-ファイルの命名)
      - [例：](#例)
    - [1.2 名前の付け方](#12-名前の付け方)
  - [2 コード記述上の規約](#2-コード記述上の規約)
    - [2.1 var を使用する](#21-var-を使用する)
    - [2.2 null 判定は is null を使う](#22-null-判定は-is-null-を使う)
    - [2.3 式体メンバー構文を使う](#23-式体メンバー構文を使う)
    - [2.4 switch 式の活用](#24-switch-式の活用)
    - [2.5 nameof() を使用する](#25-nameof-を使用する)
    - [2.6 プライマリーコンストラクタ宣言する](#26-プライマリーコンストラクタ宣言する)
    - [2.7 コメント](#27-コメント)
    - [2.8 パターンマッチングの活用](#28-パターンマッチングの活用)
    - [2.10 コレクション初期化はインライン](#210-コレクション初期化はインライン)
    - [2.11 LINQクエリは可読性を優先する](#211-linqクエリは可読性を優先する)
    - [2.13 `ToArray()` はパフォーマンスを重視する場合に使用する](#213-toarray-はパフォーマンスを重視する場合に使用する)
  - [3 設計原則](#3-設計原則)
    - [3.1 依存性逆転の原則（DIP）](#31-依存性逆転の原則dip)
    - [3.2 単一責任の原則（SRP）](#32-単一責任の原則srp)
    - [3.3 開放/閉鎖原則（OCP）](#33-開放閉鎖原則ocp)
    - [3.4 リスコフの置換原則（LSP）](#34-リスコフの置換原則lsp)
    - [3.5 インターフェース分離原則（ISP）](#35-インターフェース分離原則isp)
  - [4 コントローラー設計](#4-コントローラー設計)
    - [4.1 各コントローラーは **1** ドメインに責務を限定する（例：UserController）](#41-各コントローラーは-1-ドメインに責務を限定する例usercontroller)
    - [4.2 RESTful な命名・HTTP メソッド設計を徹底する](#42-restful-な命名http-メソッド設計を徹底する)
    - [4.3 戻り値は ActionResult を使用する](#43-戻り値は-actionresult-を使用する)
    - [4.4 非同期メソッドには必ず async Task を使う](#44-非同期メソッドには必ず-async-task-を使う)
    - [4.5 HTTP メソッド属性は必ず明示的に記載する](#45-http-メソッド属性は必ず明示的に記載する)
    - [4.6  URI設計はリソース指向を意識すること（動詞を使わない）](#46--uri設計はリソース指向を意識すること動詞を使わない)
  - [5 HTTPステータスコードの設計](#5-httpステータスコードの設計)
    - [5.1 `200 OK`: 通常成功時](#51-200-ok-通常成功時)
    - [5.2 `201 Created`: リソース作成成功時](#52-201-created-リソース作成成功時)
    - [5.3 `204 No Content`: 更新・削除成功（レスポンスボディなし）](#53-204-no-content-更新削除成功レスポンスボディなし)
    - [5.4 `400 Bad Request`: 入力エラー・バリデーション失敗](#54-400-bad-request-入力エラーバリデーション失敗)
    - [5.5 `404 Not Found`: リソースが存在しない](#55-404-not-found-リソースが存在しない)
    - [5.6 `500 Internal Server Error`: 予期しないサーバーエラー](#56-500-internal-server-error-予期しないサーバーエラー)
  - [6 サービス・リポジトリ設計](#6-サービスリポジトリ設計)
    - [6.1 サービス層は業務処理のみを実装](#61-サービス層は業務処理のみを実装)
    - [6.2 リポジトリ層は DB への読み書き操作のみを実装](#62-リポジトリ層は-db-への読み書き操作のみを実装)
  - [7 バリデーション](#7-バリデーション)
    - [7.1 単項目バリデーション](#71-単項目バリデーション)
    - [7.2 業務バリデーション](#72-業務バリデーション)
  - [8 エラーハンドリング](#8-エラーハンドリング)
    - [8.1 throw の使用規約](#81-throw-の使用規約)
      - [✅ throw が許容されるケース](#-throw-が許容されるケース)
      - [❌ throw を避けるべきケース](#-throw-を避けるべきケース)
  - [9 フィルター](#9-フィルター)
    - [9.1 フィルターの使用規約](#91-フィルターの使用規約)
  - [10 共通ユーティリティクラス（Commons / Helper）](#10-共通ユーティリティクラスcommons--helper)
    - [10.1 Commons](#101-commons)
    - [10.2 Helper](#102-helper)
  - [11 使用禁止・制限事項（禁止系規約）](#11-使用禁止制限事項禁止系規約)
    - [11.1 Linq クエリ内での `DateTime.Now` の直接使用禁止](#111-linq-クエリ内での-datetimenow-の直接使用禁止)
    - [11.2 `AsEnumerable()` の使用を原則禁止とする](#112-asenumerable-の使用を原則禁止とする)
    - [11.3 `dynamic` の使用禁止](#113-dynamic-の使用禁止)
    - [11.4 `object` のキャスト運用の禁止](#114-object-のキャスト運用の禁止)
    - [11.5 `async void` の禁止](#115-async-void-の禁止)
    - [11.6 `throw ex;` の禁止（例外のスタックトレース破棄）](#116-throw-ex-の禁止例外のスタックトレース破棄)
    - [11.7 `catch(Exception)` の濫用禁止](#117-catchexception-の濫用禁止)
    - [11.8 `ToList()`は必要な時だけ使用する。](#118-tolistは必要な時だけ使用する)
    - [11.9 `Thread` クラスの直接使用禁止](#119-thread-クラスの直接使用禁止)
    - [11.10 深すぎるネストの禁止](#1110-深すぎるネストの禁止)
    - [11.11 `if (condition) return; else return;` の禁止](#1111-if-condition-return-else-return-の禁止)
    - [11.12 `if (x == true)` / `if (x == false)` の禁止](#1112-if-x--true--if-x--false-の禁止)
    - [11.13 検索系クエリで全件DBから取得することの禁止](#1113-検索系クエリで全件dbから取得することの禁止)
      - [注意事項](#注意事項)
    - [11.14 `Include()` の多用・誤用禁止](#1114-include-の多用誤用禁止)
    - [11.15 `null` 許容型に対する `!`（null許可演算子）の原則使用禁止](#1115-null-許容型に対する-null許可演算子の原則使用禁止)
---

## 1 命名規則

### 1.1 ファイルの命名

- C# のスタイルガイドに準じて、**PascalCase** を使用する。

- クラスとファイルは 1:1 で対応し、**ファイル名 = クラス名** とする。 

  - 例外として、インターフェースは同一ファイル内に併記してもよい（例：`IUserService` と `UserService`）。

#### 例：

| ファイル名            | 内容                        |
| --------------------- | --------------------------- |
| UserController.cs     | ユーザー API コントローラー |
| UserService.cs        | ユーザーのビジネスロジック  |
| UserRepository.cs     | データアクセス層            |
| UserDto.cs            | ユーザー DTO                |
| ApiExceptionFilter.cs | グローバル例外ハンドラ      |
| StringExtensions.cs   | 文字列操作の拡張メソッド    |
| DateTimeHelper.cs     | 日付ユーティリティ          |

### 1.2 名前の付け方

- **クラス名**：`PascalCase`（例：`UserService`）

- **メソッド名**：`PascalCase`（例：`GetUserById()`）

- **ローカル変数・引数名**：`camelCase`（例：`userId`）

- **インターフェース**：`I` + `PascalCase`（例：`IUserService`）

- **非同期メソッド**：`Async` サフィックス（例：`GetUserAsync()`）

---

## 2 コード記述上の規約

- **読みやすさ・簡潔さ**を優先して記述すること。
- `Visual Studio` 上のアナライザーで警告が出た場合は抑制回避せず **リファクタリング** を行う。
- `IDE` のインフォメーション（提案）も無視せず可能な限り **リファクタリング** を行う。

### 2.1 var を使用する

- ローカル変数は原則として [**`var`**](https://learn.microsoft.com/ja-jp/dotnet/csharp/language-reference/statements/declarations) を使用して記述を簡潔にすること

### 2.2 null 判定は is null を使う

- `null` チェックには `== null` / `!= null` の代わりに、`is null` / `is not null` を使用すること

### 2.3 式体メンバー構文を使う

- シンプルな `getter` や メソッド は式体メンバー構文を利用して簡潔に書く。

```csharp
public string FullName => $"{FirstName} {LastName}";
```

### 2.4 switch 式の活用

- 条件分岐には `switch` 式（C# 8 以降）を積極的に使用し、冗長な if-else を避ける。

```csharp
var role = user.Role switch
{
    "admin" => "管理者",
    "user" => "一般ユーザー",
    _ => "未設定"
};
```

### 2.5 nameof() を使用する

- パラメータ名・プロパティ名をハードコーディングせず、`nameof()` を使用すること
```csharp
throw new ArgumentNullException(nameof(email));
```

### 2.6 プライマリーコンストラクタ宣言する

- コンストラクタとフィールド定義を省略可能な `primary constructor` 構文を使用すること

```csharp
// ✅ 良い例
public class UserService(string Email, string Name) { }

// ❌ 悪い例 : 旧来の冗長な記法
public class UserService
{
    public string Email { get; }
    public string Name { get; }

    public UserService(string email, string name)
    {
        Email = email;
        Name = name;
    }
}
```

### 2.7 コメント

- `TODO` コメントには 期限の目安を明示する。
- XML ドキュメントコメント（`///`）は パブリックなクラス・メソッド・プロパティに必須とする。

### 2.8 パターンマッチングの活用
- `is`, `switch`, `when` などのパターンマッチングを活用し、明示的なキャストや冗長な分岐を避ける。

```csharp
// ✅ 良い例：is + 型パターン
if (obj is User user)
{
    Console.WriteLine(user.Name);
}

// ❌ 悪い例 : 明示的なキャスト + nullチェック（古典的な書き方）
if (obj != null && obj is User)
{
    var user = (User)obj; // ← 明示的キャスト
    Console.WriteLine(user.Name);
}


// ❌ 悪い例 : switchでの冗長な型判定 + キャスト
switch (obj.GetType().Name)
{
    case "Admin":
        var admin = (Admin)obj;
        admin.ManageUsers();
        break;
}
```

### 2.10 コレクション初期化はインライン
- コレクションや辞書の初期化は可能な限りインラインで簡潔に記述する。

```csharp
// ✅ 良い例
var names = new List<string> { "山田", "佐藤", "鈴木" };

// ❌ 悪い例 : 明示的に new したあとに Add で1件ずつ追加
var names = new List<string>();
names.Add("山田");
names.Add("佐藤");
names.Add("鈴木");

```

### 2.11 LINQクエリは可読性を優先する
- クエリ式とメソッドチェーンは、可読性を基準に使い分ける。
- 
```csharp
// ✅ 良い例 : 中間変数を使うことで、意図が明確になり、テストやデバッグも容易。
var activeUsers = users.Where(u => u.IsActive);
var orderedUsers = activeUsers.OrderBy(u => u.Name);
var result = orderedUsers.Select(u => new { u.Id, u.Name }).ToList();


// ❌ 悪い例 : チェーンを過剰にネストして読みづらい
var sorted = users.Where(u => u.IsActive).OrderBy(u => u.Name).Select(u => new { u.Id, u.Name }).ToList();

```

### 2.13 `ToArray()` はパフォーマンスを重視する場合に使用する
- `ToArray()` は `ToList()` よりも軽量かつ高速にメモリ確保されるため、固定長の配列で十分なケースに限定して使用する


## 3 設計原則

### 3.1 依存性逆転の原則（DIP）

- 依存関係の注入には `IServiceCollection` を使用し、`AddScoped` を基本とする。
- サービス層・リポジトリ層ともにインターフェース経由で注入し**抽象に依存**すること。

```csharp
// ✅ 良い例
services.AddScoped<IUserService, UserService>();
services.AddScoped<IUserRepository, UserRepository>();

// ❌ 悪い例 : 直接初期化
var userService = new UserService();
```

### 3.2 単一責任の原則（SRP）

- 各クラス・各レイヤーは**一つの責務に限定**すること。( たとえば、**コントローラーは API の受け口としての責務のみを持ち、業務処理はサービスに委譲する。**)
- SRP はサービス・リポジトリ層に限らず、あらゆる構成要素（**Filter、Middleware、DTO など**）に適用する設計の基本指針とする。

### 3.3 開放/閉鎖原則（OCP）

- コードの変更を伴わずに拡張できる設計を行うこと
- 新しい機能追加や仕様変更が発生しても、既存のコードを変更せずに拡張可能な構造とする。

```csharp
// ✅ 良い例 : switch 文や if-else による型分岐ではなく、ポリモーフィズム（継承やインターフェース）で処理を委譲する。

public abstract class NotificationSender
{
    public abstract void Send(string message);
}

public class EmailSender : NotificationSender
{
    public override void Send(string message)
    {
        Console.WriteLine($"Email: {message}");
    }
}

public class SmsSender : NotificationSender
{
    public override void Send(string message)
    {
        Console.WriteLine($"SMS: {message}");
    }
}

// 利用側は抽象型で扱う
public class NotificationService(NotificationSender sender)
{
    public void Notify(string message)
    {
        _sender.Send(message);
    }
}

// ❌ 悪い例 : 条件分岐が増え続ける
public class NotificationService
{
    public void Notify(string message, string type)
    {
        if (type == "email")
        {
            Console.WriteLine($"Email: {message}");
        }
        else if (type == "sms")
        {
            Console.WriteLine($"SMS: {message}");
        }
        else if (type == "slack") // 追加のたびに変更が必要
        {
            Console.WriteLine($"Slack: {message}");
        }
    }
}
```

### 3.4 リスコフの置換原則（LSP）

- 派生クラスは基底クラスとして置き換えても正しく動作すること
- 実装クラスは、インターフェースや抽象クラスの契約（前提・動作・戻り値）を裏切らず、同じ期待値で使えるように設計すること

```csharp
// ❌ 悪い例 : 呼び出し側は IMessageSender なのに例外で落ちる → LSP違反
public class BrokenEmailSender : IMessageSender
{
    public void Send(string message)
    {
        throw new NotSupportedException("メール送信は未サポートです"); // ← 実装していない
    }
}
```

### 3.5 インターフェース分離原則（ISP）

- 特定の目的に応じたインターフェースを分割し、呼び出し元に不要な依存を強制しないこと。
- 一つのインターフェースが多すぎる責務を持つと、実装クラスが不要なメソッドまで実装することになり、保守性が下がる。
- 実装クラスが使わないメソッドに依存させられるような設計は避けること。

```csharp
// ✅ 良い例 ： 目的別にインターフェースを分割
public interface IReadable
{
    string Read();
}

public interface IWritable
{
    void Write(string content);
}

public class ReadOnlyFile : IReadable
{
    public string Read() => "読み込み専用";
}

public interface IFileHandler
{
    string Read();
    void Write(string content);
}

// ❌ 悪い例 : 不必要な実装を強制する大きすぎるインターフェース
public class ReadOnlyFile : IFileHandler
{
    public string Read() => "読み込み専用";

    public void Write(string content)
    {
        throw new NotSupportedException("書き込みはサポートされていません");
    }
}
```

## 4 コントローラー設計

### 4.1 各コントローラーは **1** ドメインに責務を限定する（例：UserController）

- `UserController` はユーザー管理に関するAPIエンドポイントのみを担当し、`ProjectController`や `AuthController`など、他の機能と混在させないこと
- コントローラー単位で機能が明確になり、役割の境界が把握しやすくする実装を心がける

### 4.2 RESTful な命名・HTTP メソッド設計を徹底する

- GET /api/users

- GET /api/users/{id}

- POST /api/users

- PUT /api/users/{id}

- DELETE /api/users/{id}

### 4.3 戻り値は ActionResult<T> を使用する

- API のエンドポイントでは、HTTP ステータスコードとレスポンスデータの両方を柔軟に制御できるようにするため、<br>`ActionResult<T>` を戻り値とすることを原則とする。

```csharp
// ✅ 良い例
[HttpGet("{id}")]
public async Task<ActionResult<UserDto>> GetUser(Guid id)
{
    var user = await _service.GetUserByIdAsync(id);
    if (user == null) return NotFound();
    return Ok(user);
}

// ❌ 悪い例
[HttpGet("{id}")]
public UserDto GetUser(Guid id) // 非同期でもない・戻り値も固定
{
    return _service.GetUserById(id);
}
```

### 4.4 非同期メソッドには必ず async Task<T> を使う

- 非同期で実行される処理（DB アクセス、外部 API 呼び出し、ファイル IO など）では、必ず `async` 修飾子と `Task<T>` を併用すること。

```csharp
// ✅ 良い例
[HttpPost]
public async Task<IActionResult> CreateUserAsync(CreateUserRequest request)
{
    await _service.CreateUserAsync(request);
    return NoContent();
}

// ❌ 悪い例
[HttpPost]
public IActionResult CreateUser(CreateUserRequest request) // 同期的に処理している
{
    _service.CreateUser(request);
    return NoContent();
}
```

### 4.5 HTTP メソッド属性は必ず明示的に記載する
- コントローラーアクションには `[HttpGet]`, `[HttpPost]`, `[HttpPut]`, `[HttpDelete]` などの HTTP メソッド属性を省略せずに明示的に付与すること。


### 4.6  URI設計はリソース指向を意識すること（動詞を使わない）
- `POST /api/users/{id}/activate` ❌（動詞を含む）
- `POST /api/users/{id}/activation` ✅（リソースとして設計）


## 5 HTTPステータスコードの設計
- 開発者は下記の方針に従い適切にステータスコードを返す実装をすること

    - `200` OK: 通常成功時

    - `201` Created: リソース作成成功

    - `204` No Content: 更新・削除成功

    - `400` Bad Request: 入力ミス

    - `401` Unauthorized: 認証されてない

    - `403` Forbidden: 権限がない

    - `404` Not Found: リソースが存在しない

    - `409` Conflict: 衝突（例：一意制約違反）

    - `500` Internal Server Error : アプリケーション内で予期しないエラー
  
### 5.1 `200 OK`: 通常成功時
- `GET` リクエスト等でリソースを正常に取得できた場合に返す。
 
- レスポンスボディには取得されたリソース（DTO）、もしくはメッセージを含める。

```csharp
[HttpGet("{id}")]
public async Task<ActionResult<UserDto>> GetUser(Guid id)
{
    var user = await _service.GetUserByIdAsync(id);
    if (user is null) return NotFound();
    return Ok(user); 
}
```
### 5.2 `201 Created`: リソース作成成功時
- `POST` リクエストでリソースが新規作成されたときは、必ず `CreatedAtAction` を使用して `201 Created` を返す。

- `Location` ヘッダーに取得用URLを付け、レスポンスボディには作成済みのDTOを含める。

```csharp
[HttpPost]
public async Task<ActionResult<UserDto>> CreateUserAsync(CreateUserRequest request)
{
    var user = await _service.CreateUserAsync(request);

    return CreatedAtAction(
        nameof(GetUser),
        new { id = user.Id },
        user
    );
}

```csharp
[HttpGet("{id}")]
public async Task<ActionResult<UserDto>> GetUser(Guid id)
{
    var user = await _service.GetUserByIdAsync(id);
    if (user == null) return NotFound();
    return Ok(user);
}
```

### 5.3 `204 No Content`: 更新・削除成功（レスポンスボディなし）
- `PUT` や `DELETE` 成功時で、レスポンスボディが不要な場合に使用する。

- 成功はしたが、返すデータが無いときに明確に表現するために使う。

```csharp
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteUser(int id)
{
    var success = await _service.DeleteUserAsync(id);
    if (!success) return NotFound();
    return NoContent();
}
```

### 5.4 `400 Bad Request`: 入力エラー・バリデーション失敗
- クライアントのリクエストに構文的または意味的な誤りがある場合。

- `ModelState` を利用し、バリデーションエラー時に返す。

```csharp
[HttpPost]
public async Task<IActionResult> CreateUserAsync(CreateUserRequest request)
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);

    await _service.CreateUserAsync(request);
    return NoContent();
}
```

### 5.5 `404 Not Found`: リソースが存在しない
- 指定されたIDなどに該当するリソースが存在しない場合に返す。

- 存在チェックに失敗した場合、`null` のまま返すのではなく、必ず `NotFound()` を返すこと。

```csharp
var user = await _service.GetUserByIdAsync(id);
if (user is null) return NotFound();
```
### 5.6 `500 Internal Server Error`: 予期しないサーバーエラー
- アプリケーション内部で未処理の例外やバグが発生したとき返す。

- 通常は ExceptionFilter や Middleware で一括処理し、クライアントには曖昧なエラーメッセージのみ返すこと。

## 6 サービス・リポジトリ設計

### 6.1 サービス層は業務処理のみを実装

- アプリケーションの業務処理を実装すること
- コントローラーから直接 DB アクセスせず、サービスを介して処理を行うこと
- 複数のリポジトリにまたがる処理、もしくは複数エンティティに対して一貫性を保証する必要がある場合は、`Unit of Work` パターンを実装する。

### 6.2 リポジトリ層は DB への読み書き操作のみを実装

- 原則として、`Entity` と `Repository` は 1 対 1 の関係で設計すること

- CodeMaster などの読み取り専用の `Repository` は追加削除が可能な設計しないインターフェース設計をする

```csharp
// ❌ 悪い例：保存用のインターフェースが実装されている
public interface ICodeMasterQueryRepository
{
    Task<CodeMaster?> FindByCodeAsync(string category, string code);
    Task<List<CodeMaster>> GetByCategoryAsync(string category);
    Task<CodeMaster?> AddAsync(string category, string code);
}
```

- サービス層からは `IUserRepository`などの抽象インターフェース経由で呼び出す。
  - 依存性逆転の原則（DIP） を意識し、具象ではなく抽象に依存する。

```csharp
public class UserService : IUserService
{
    // ❌ 悪い例：実態クラスを直接参照している
    private readonly UserRepository _repository;
    // ✅ 良い例
    private readonly IUserRepository _repository;
}
```

## 7 バリデーション

### 7.1 単項目バリデーション

- 原則として [**`DataAnnotations`**](https://qiita.com/gushwell/items/266d208229697ea2ffc3) を使用すること
- また、バリデーションメッセージは必ず [リソースファイル](https://learn.microsoft.com/ja-jp/dotnet/core/extensions/create-resource-files)（**Messages.resx**）で管理すること

```csharp
// ✅ 良い例：リソースファイルを参照してメッセージを設定
[Required(ErrorMessageResourceType = typeof(Messages), ErrorMessageResourceName = nameof(Messages.V10001))]
[EmailAddress(ErrorMessageResourceType = typeof(Messages), ErrorMessageResourceName = nameof(Messages.V10003))]
[StringLength(50, ErrorMessageResourceType = typeof(Messages), ErrorMessageResourceName = nameof(Messages.V10002))]
public string Email { get; set; }

// ❌ 悪い例：メッセージをべた書き
[Required(ErrorMessage = "メールアドレスは必須です")]
[EmailAddress(ErrorMessage = "メールアドレスの形式が正しくありません")]
public string Email { get; set; }

// ❌ 悪い例：処理の中で手動でコントローラー内でバリデーションしている
if (string.IsNullOrEmpty(request.Email)) // ← NG: 手動チェックは冗長
    return BadRequest("メールアドレスは必須です");
```

### 7.2 業務バリデーション

- 複数項目間の相関チェックや、DB との突合、ユニーク制約など、ビジネスルールに基づく検証は業務バリデーションとしてサービス層で行うこと
- バリデーションエラーの場合は、`BadRequest` として `400` エラーを返すこと、またレスポンスの形式は PRJ で単項目バリデーションと統一すること

```csharp
if (await _userRepository.ExistsByEmailAsync(request.Email))
        return BadRequest(ApiValidationError.Create(Messages.E10001));
```

## 8 エラーハンドリング

### 8.1 throw の使用規約

- 原則として、例外をスローする状況は 防ぐべき異常状態が発生し、それが明示的にハンドリングされるべき場合に限定する。
- 通常の処理フローで発生し得る「想定内の条件分岐」は、`if` 文や `TryXX` パターンで対応し、`throw` を避ける。
- **外部サービスとの通信エラーや、システムの状態として致命的な失敗**（DB 接続失敗など）は `throw` 対象となる。
- 例外をスローするときは、必ず 意味のある独自例外（例：`BusinessException`、`NotFoundException`） を使用する。
- .NET の組み込み例外（例：`InvalidOperationException` や `ArgumentNullException`）は、システムの誤使用時のみに限定する。

#### ✅ throw が許容されるケース

- 外部サービスとの通信に失敗し、再試行やフォールバックが必要な場合
- 想定されないシステム異常や予期せぬ例外（ただし `ExceptionFilter` や `Middleware` で捕捉・共通ハンドリング）

#### ❌ throw を避けるべきケース

- 正常系の一部として「失敗時に `throw`」を使う（例：メール未登録 → エラーではなく `null` や `TryGet`）
- 単なる条件チェック（例：`if (x == null) throw`）で済む処理 → 事前検証・ガード節で対応

## 9 フィルター

### 9.1 フィルターの使用規約

- 共通的な処理（例：例外処理、ログ記録、バリデーションエラーの整形など）は、[`ActionFilter`](https://learn.microsoft.com/ja-jp/aspnet/mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-cs?source=recommendations) または [`ExceptionFilter`](https://learn.microsoft.com/ja-jp/aspnet/core/mvc/controllers/filters?view=aspnetcore-9.0) に分離して記述すること。
- コントローラー内で重複する前処理／後処理は極力フィルターに切り出すことで、責務分離と再利用性を高める。

```csharp
//✅ 良い例：例外処理を ExceptionFilter に切り出している
public class ApiExceptionFilter : IExceptionFilter
{
  public void OnException(ExceptionContext context)
  {
      context.Result = new ObjectResult(new ApiError
      {
          Message = "サーバーエラー",
      }) { StatusCode = 500 };
      context.ExceptionHandled = true;
      await mailService.SendAsync(context.Exception);
  }
}

// ❌ 悪い例：全コントローラーに try-catch をベタ書きしている
[HttpPost]
public IActionResult CreateUser(CreateUserRequest req)
{
    try
    {
        _userService.CreateUser(req);
        return Ok();
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, "エラー発生");
        return StatusCode(500, "エラーが発生しました");
    }
}
```

## 10 共通ユーティリティクラス（Commons / Helper）

- 静的メソッドとして実装すること。
- 拡張メソッド を使用する場合は、[型名]`Extensions` の命名規則を用いる。
- 複雑なロジックや外部依存を含む場合はインスタンス化可能な Service とすること

### 10.1 Commons

- 汎用的かつドメイン非依存なロジックをまとめたライブラリ層。
- 日付操作や変換など、あらゆる層で再利用される関数を実装すること。

```csharp
//✅ 良い例：汎用的かつドメイン非依存なロジック
public static string ToJapaneseDate(this DateTime date)
  => date.ToString("yyyy年MM月dd日");

// ❌ 悪い例：ログ出力処理（外部依存を持ち込まない）
public static void LogUsage(DateTime time)
{
  Console.WriteLine($"使われました: {time}");
}
```

### 10.2 Helper

- 用途が明確な、特定の操作に特化した補助的関数群。状態を持たず、基本的には静的クラスと静的メソッドで構成されること。

```csharp
//✅ 良い例：明確な機能単位（Email操作）
public static string Normalize(string email)
  => email.Trim().ToLowerInvariant();

// ❌ 悪い例：汎用的すぎるため Commons に移動すべき
public static string FormatDate(DateTime date)
  => date.ToString("yyyy/MM/dd");
```

## 11 使用禁止・制限事項（禁止系規約）

### 11.1 Linq クエリ内での `DateTime.Now` の直接使用禁止

- `LINQ to Entities`（EF Core 等）では `DateTime.Now` は `GETDATE()`として扱われ、DB 側のタイムゾーンで実行されるため、必ず `Linq` クエリの外で変数に格納して使用すること

```csharp
//✅ 良い例：変数に格納して使用
var datetime = DateTime.Now;
var users = dbContext.Users.Where(u => u.CreatedAt >= datetime);

// ❌ 悪い例：直接使用
var users = dbContext.Users.Where(u => u.CreatedAt >= DateTime.Now);
```

- PRJ によっては静的解析ツールなどを使用して `DateTime.Now` の使用そのものを禁止にすることも検討すること

### 11.2 `AsEnumerable()` の使用を原則禁止とする
- IQueryable → IEnumerable に切り替えることで、クエリ評価がDBからメモリに切り替わり、パフォーマンス・メモリに悪影響を与えるため原則禁止とする。
- DBで処理可能な形に変換する or 関数をSQLにマッピングすることが理想的
- 蒸気を踏まえて、それでもやむを得ず使用する場合は、明示的なコメントとコードレビュー承認が必要。

```csharp
// ❌ 全件ロードされ、Whereがメモリ上で実行される
// MyLocalFunctionがSQL側で評価できないため AsEnumerable() を使用して意図的にメモリ上で実行している
var users = dbContext.Users
    .AsEnumerable()
    .Where(u => MyLocalFunction(u))
    .ToList();
```

### 11.3 `dynamic` の使用禁止
- `dynamic` は 型安全を破壊するため、原則使用禁止。
- 特別な理由（リフレクション、COM相互運用、JSON柔軟解析など）がある場合のみ、明示的なコメントとコードレビュー承認が必要。

### 11.4 `object` のキャスト運用の禁止
- `object` 型への変換やボックス化 → アンボックス化は パフォーマンス低下とバグの温床のため **原則全面禁止**。

```csharp
// ❌ ジェネリクスや型安全な構造体で置き換えるべき
object val = 1;
int num = (int)val; 
```

### 11.5 `async void` の禁止
- async void は例外が非検知になるため**使用禁止**

### 11.6 `throw ex;` の禁止（例外のスタックトレース破棄）
- `throw ex;` は例外の 元のスタックトレースを失うため禁止。

- `throw;` を使用してスタックトレースを保持すること。

```csharp
// ✅ 良い例 
catch (Exception ex) throw;

// ❌ 悪い例 :スタックトレースが失われる
catch (Exception ex) throw ex;
```

### 11.7 `catch(Exception)` の濫用禁止
- `Exception` を丸ごとキャッチして握りつぶすのは**禁止**
```csharp
// ✅ 良い例 ：ログ＋再スロー
try { ... } catch (Exception ex) { logger.LogError(ex); throw; }

// ❌ 悪い例 :何もせず握りつぶす
try { ... } catch (Exception) { }
```

### 11.8 `ToList()`は必要な時だけ使用する。
- `ToList()` を使うのは「必要なときだけ」に限定し、`IEnumerable` の遅延評価を使用すること
- 明確な理由がない限り、パフォーマンスとメモリ効率を考慮してコレクションの中間状態で `ToList()` を挟まない
- 
```csharp
// ✅ 良い例 : 最終的に一覧として評価する必要がある場合
var sortedUsers = users
    .Where(u => u.IsActive)
    .OrderBy(u => u.Name)
    .ToList(); // 最終出力のため評価が必要

// ✅ 良い例 : List特有の機能（Count, Index, Addなど）を使うとき
var list = items.Where(x => x.IsValid).ToList();
list.Add(extraItem);


// ❌ 悪い例 : 無意味に中間で ToList() を使う
// LINQチェーンの途中で .ToList() を挟むとパフォーマンスとメモリ効率が低下する
var activeUsers = users.Where(u => u.IsActive).ToList();
var sorted = activeUsers.OrderBy(u => u.Name).ToList();

// ❌ 悪い例 : foreach のためだけに ToList() を呼ぶ。IEnumerable のままでよい。
foreach (var user in users.Where(u => u.IsActive).ToList()) // ← 不要な変換
{
    Console.WriteLine(user.Name);
}

// ❌ 悪い例 : 同じクエリを ToList() で複数回評価している
var validUsers = users.Where(u => u.IsValid);
var count = validUsers.ToList().Count;     // 評価①
var last = validUsers.ToList().Last();     // 評価②（再評価される）
```

### 11.9 `Thread` クラスの直接使用禁止
- `Thread` の直接使用は、ThreadPool を無視した非効率なスレッド生成となるため**禁止**
- Task.Run, BackgroundService, IHostedService など .NET Core に適した非同期モデルを使用すること

```csharp
// ✅ 良い例
await Task.Run(() => DoWork());

// ❌ 悪い例
var thread = new Thread(() => { DoWork(); });
thread.Start();
```
### 11.10 深すぎるネストの禁止
- `if`, `else if`, `else`, `switch`, `for`, `foreach`, `while`, `try-catch-finally` などのネストの深さは 2段階までに制限する。
- 3段階以上のネストが発生する場合は、以下の方法で必ずフラット化・分離すること
  - メソッド分離
  - `switch` 式 / パターンマッチ への変換
  - 早期 `return` の導入

```csharp
// ✅ 良い例：ロジックを分離
public void HandleUser(User user)
{
    if (!CanBeProcessed(user)) return;
    ProcessUser(user);
}

private bool CanBeProcessed(User user)
{
    return user is not null && user.IsActive && !user.IsLocked;
}


// ❌ 悪い例：ネストが深くて読みづらい
public void HandleUser(User user)
{
    if (user != null)
    {
        if (user.IsActive)
        {
            if (!user.IsLocked)
            {
                ProcessUser(user);
            }
        }
    }
}
```

### 11.11 `if (condition) return; else return;` の禁止
- 無意味な else は削除し、シンプルな制御構造にすること
```csharp
// ✅ 良い例
return condition;

// ❌ 悪い例
if (condition)
    return true;
else
    return false;
```

### 11.12 `if (x == true)` / `if (x == false)` の禁止
- bool 型の値を比較で評価するのは冗長、真偽値はそのまま評価すること
```csharp
// ✅ 良い例
if (isValid) { ... }
if (!isLocked) { ... }

// ❌ 悪い例
if (isValid == true) { ... }
if (isLocked == false) { ... }
```

### 11.13 検索系クエリで全件DBから取得することの禁止
- ページング処理などで大量データが検索対象の場合を表示すべき範囲だけを取得すること。
- クライアント側やメモリ上での後フィルタリング、全件取得 (`.ToList()` → `.Skip().Take()`) は パフォーマンス劣化・帯域浪費の原因となるため禁止
- `.Skip().Take()` は DBクエリの中（`IQueryable`）で使い、DBレベルで正しく `OFFSET` / `FETCH` されるようにする。

```csharp
// ❌ 悪い例 : 全件を取得し、アプリケーション側でページング
var allUsers = dbContext.Users.ToList();
var page = allUsers.Skip(pageIndex * pageSize).Take(pageSize).ToList();

// ✅ 良い例 : IQueryable のまま Skip/Take を適用し、DBで最小限のデータだけを取得
var page = dbContext.Users
    .Where(u => u.IsActive)
    .OrderBy(u => u.Name)
    .Skip(pageIndex * pageSize)
    .Take(pageSize)
    .ToList();
```
#### 注意事項
- `IEnumerable` に評価された時点でメモリ上に展開されるため、`IQueryable` の状態でページングを完了させることが必須

### 11.14 `Include()` の多用・誤用禁止
- 多重連結や複雑な関連の `N+1` 問題 を招きやすい
- 可能であれば `Select` で必要データだけを取得する

```csharp
// ✅ 必要なデータだけ Projection
var userSummaries = dbContext.Users
    .Select(u => new {
        u.Name,
        OrderCount = u.Orders.Count
    })
    .ToList();

// ❌ Includeの乱用
var users = dbContext.Users.Include(u => u.Orders).ToList();
```

### 11.15 `null` 許容型に対する `!`（null許可演算子）の原則使用禁止
- 使用した場合、将来の `NullReferenceException` の原因となるため、`!` を使わず、正しく初期化・検証することで null 安全性を保つこと
- `null` チェックはコード上で明示的に記述し、正しい初期化、検証、例外スローで安全性を担保すること
- ビジネスロジック上 `null` とならない場合は根本の設計を見直し修正すること
```csharp
// ✅ 良い例 : null チェックを明示的に行う
string? name = GetName();
if (name is null) name = "test"; // ← コード上で null 想定して補完すること
Console.WriteLine(name.Length);

// ❌ 悪い例：null許容を抑制して実行時エラーを誘発
string? name = GetName();
Console.WriteLine(name!.Length);
```