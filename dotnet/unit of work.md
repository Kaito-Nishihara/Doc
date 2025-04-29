# Unit of Work パターン開発ガイド

---

## 1. 概要

本ドキュメントは、ASP.NET Coreプロジェクトにおいて、  
**Unit of Workパターン**を使用して  
**トランザクション管理を標準化する方法**をまとめたものです。

トランザクションの開始・コミット・ロールバックを一元管理し、  
アプリケーションサービス層からトランザクションを意識しないコード設計を目指します。

---

## 2. 使用対象

- データベース操作（EF Core）
- まとめて実行する複数のリポジトリ更新
- 失敗時に自動でロールバックさせたいケース

---

## 3. 基本インターフェース

```csharp
public interface IUnitOfWork
{
    Task BeginTransactionAsync(Func<IDbContextTransaction, Task<bool>> operation);
    Task BeginTransactionAsync(Func<IDbContextTransaction, Task> operation);
    Task BeginTransactionAsync(Func<Task> operation);
}

## 4. 基本実装クラス

```csharp
public class UnitOfWork(AN1DbContext context) : UnitOfWork
{
    public async Task BeginTransactionAsync(Func<Task> operation)
    {
        await BeginTransactionAsync(async _ => await operation());
    }

    public async Task BeginTransactionAsync(Func<IDbContextTransaction, Task> operation)
    {
        await BeginTransactionAsync(async transaction =>
        {
            await operation(transaction);
            return true;
        });
    }

    public async Task BeginTransactionAsync(Func<IDbContextTransaction, Task<bool>> operation)
    {
        var strategy = context.Database.CreateExecutionStrategy();
        await strategy.ExecuteAsync(async () =>
        {
            await using var transaction = await context.Database.BeginTransactionAsync();
            try
            {
                var shouldCommit = await operation(transaction);

                if (shouldCommit)
                {
                    if (context.ChangeTracker.HasChanges())
                    {
                        await context.SaveChangesAsync();
                    }
                    await transaction.CommitAsync();
                }
                else
                {
                    await transaction.RollbackAsync();
                }
            }
            catch
            {
                await transaction.RollbackAsync();
                throw;
            }
        });
    }
}

```

## 5. 利用例（サービス層からの呼び出し）
- 基本実装クラスをそのまま下記のように使用する。
```csharp
await unitOfWork.BeginTransactionAsync(async () =>
{
    var user = new User { Name = dto.Name, Email = dto.Email };
    await userRepository.AddAsync(user);
    // 必要なら他のリポジトリ操作もここで行う

    // 例外が発生しなければ自動でSaveChanges + Commitされる
});
```

- もしくは、コミットを明示的に制御したい場合：
```csharp
public async Task<bool> UpdateUserAsync(UserUpdateDto dto)
{
    return await unitOfWork.BeginTransactionAsync(async transaction =>
    {
        var user = await userRepository.GetByIdAsync(dto.Id);
        if (user == null)
            return false; // → ロールバック

        user.Name = dto.Name;
        _userRepository.Update(user);

        return true; // → コミット
    });
}
```




