# Repository実装
- 本ドキュメントは、データアクセス層（Repository）の標準実装方法をまとめたものです。
- すべてのRepositoryは、汎用的なインターフェース・ベースクラスを継承して実装します。

## 1. 基底クラス
- Repositoryの共通機能は RepositoryBase<TEntity> に実装します。

- 基底クラスは、標準的なCRUD操作のみを提供し、それ以上の責務は持たないものとします。
  - 従来の基底クラスは機能が多すぎて拡張性が乏しいため改修

### `RepositoryBase<TEntity>`
```csharp
public interface IRepositoryBase<TEntity> where TEntity : class
{
    IQueryable<TEntity> Query();
    Task<TEntity?> GetByIdAsync(object id);
    Task AddAsync(TEntity entity);
    void Update(TEntity entity);
    void Delete(TEntity entity);
    Task SaveChangesAsync();
}

public abstract class RepositoryBase<TEntity> : IRepositoryBase<TEntity>
    where TEntity : class
{
    protected readonly ApplicationDbContext Context;
    protected readonly DbSet<TEntity> DbSet;

    protected RepositoryBase(ApplicationDbContext context)
    {
        Context = context;
        DbSet = context.Set<TEntity>();
    }

    public IQueryable<TEntity> Query()
    {
        return DbSet.AsQueryable();
    }

    public async Task<TEntity?> GetByIdAsync(object id)
    {
        return await DbSet.FindAsync(id);
    }

    public async Task AddAsync(TEntity entity)
    {
        await DbSet.AddAsync(entity);
    }

    public void Update(TEntity entity)
    {
        DbSet.Update(entity);
    }

    public void Delete(TEntity entity)
    {
        DbSet.Remove(entity);
    }

    public async Task SaveChangesAsync()
    {
        await Context.SaveChangesAsync();
    }
}

```

## 2. 実装手順

### 2.1  別リポジトリの作成
- エンティティごとに専用のRepositoryインターフェース・クラスを作成します。

- 必要に応じて、エンティティ特有のクエリメソッドを追加します。

### 例：UserRepository
```csharp
public interface IUserRepository : IRepositoryBase<User>
{
    Task<bool> ExistsByEmailAsync(string email);
}

public class UserRepository : RepositoryBase<User>, IUserRepository
{
    public UserRepository(ApplicationDbContext context) : base(context) { }

    public async Task<bool> ExistsByEmailAsync(string email)
    {
        return await DbSet.AnyAsync(u => u.Email == email);
    }
}
```

### 2.2 リポジトリの使用
- リポジトリ層は、**サービス層**からのみ呼び出します。  

```csharp
public async Task<UserDto?> GetByIdAsync(int id)
{
    var user = await userRepository.GetByIdAsync(id);
    return user == null ? null : mapper.Map<UserDto>(user);
}

public async Task<List<UserDto>> GetAllAsync(int name)
{
    var user = await userRepository
    .Query(u => u.Name == name)
    .OrderBy(u => u.Name)
    .ToListAsync();

    return mapper.Map<List<UserDto>>(users);
}

public async Task<UserDto> CreateAsync(UserCreateDto dto)
{
    var user = mapper.Map<User>(dto);

    await userRepository.AddAsync(user);
    await userRepository.SaveChangesAsync();

    return mapper.Map<UserDto>(user);
}

public async Task<bool> DeleteAsync(int id)
{
    var user = await userRepository.GetByIdAsync(id);
    if (user is null)
        return false;

    userRepository.Delete(user);
    await userRepository.SaveChangesAsync();

    return true;
}
```

### 注意事項
- 注意事項
- IQueryableを返すメソッドは、呼び出し側で確実に`ToListAsync()`、`FirstOrDefaultAsync()`等を呼び、クエリを確定させること。
- ベースクラスと役割が重複しないように注意する
- DTOマッピングはRepository層で行わないこと。あくまで「データベースアクセスに限定」する。

