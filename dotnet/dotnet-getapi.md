# 

## 1. DTO（レスポンス用オブジェクト）作成
- `/Dtos` フォルダ配下に作成
- APIレスポンス用に必要な項目のみを持つクラス
- `Entity` モデルをそのまま返さないこと
```csharp
namespace MyApp.Dtos
{
    public class UserDto
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```
※エンティティをそのまま返さず、DTOで必要な項目だけ返す。

## 2. リポジトリクラス作成
- `/Repositories` フォルダ配下に `[Entity]Repository.cs` を作成
- リポジトリインターフェースを実装し、データを取得
```csharp
public class UserRepository : IUserRepository
{
    public async Task<User?> GetByIdAsync(int id)
    {
        // 実装例：DBアクセスやメモリデータ
        return await Task.FromResult(new User { Id = id, Name = "Sample" });
    }
}

```

## 3. サービスクラス作成
- 他アカウントのデータを参照できないように、サービス層で防御すること。

- 一致しない場合は、null を返却するか、独自例外（例：ForbiddenException）をスローして対応する。
```csharp
public interface IUserService
{
    Task<UserDto?> GetAsync(int id);
}

public async Task<UserDto?> GetAsync(int id, int currentUserId)
{
    var user = await _repository.GetByIdAsync(id);

    // 存在しない場合
    if (user is null)
        return null;

    // ログインユーザーとデータ所有者が一致しない場合 (可能であればリポジトリのクエリに仕込む)
    if (user.Id != currentUserId)
        return null; // または throw new ForbiddenException("アクセス権がありません。");

    // 正常時
    return new UserDto
    {
        Id = user.Id,
        Name = user.Name
    };
}

```

## 4. Getコントローラー作成
- サービス層を呼び出し、HTTPレスポンスを返却
- 該当する値が存在しない場合は `NotFound`、存在する場合は `ok` でレスポンスる
```csharp
[HttpGet("{id}")]
public async Task<ActionResult<UserDto>> Get(int id)
{
    var user = await _service.GetAsync(id);
    return user is not null ? Ok(user) : NotFound();
}
```

## 5. チェックリスト

| チェック項目                                         | 
| :--------------------------------------------------- | 
| DTOを作成し、Entityを直接レスポンスしていない        | 
| Getでレスポンスする際にDTOへマッピングしている       |    
| ログイン中のユーザーに紐づくデータのみ取得している   |   
| 他アカウントのデータを参照できないように制御している |     
| データが存在しない場合はNotFoundを返している         |     

