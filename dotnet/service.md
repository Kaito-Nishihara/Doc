# Service実装
- Controllerでは直接Repositoryにアクセスせず、Service層を経由してデータ取得・操作を行う。

- クライアント向けDTOとの変換、認可チェック、バリデーションなどもここで行う。


## 1. サービスインターフェース作成
- サービス層も基本的に**非同期（Task）**を使うこと

```csharp
namespace MyApp.Services
{
    public interface IUserService
    {
        Task<UserDto?> GetAsync(Guid id, Guid currentUserId);
        Task<Guid> CreateAsync(UserCreateDto dto, Guid currentUserId);
    }
}

```

## 2. サービス実装クラス作成
```csharp
public class UserService(IMapper mapper, IUserRepository userRepository) : IUserService
{
    
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
}
```

## 3. 標準的な実装方法

### 3.1 バリデーションチェック
- サービス層では、**基本的な業務バリデーション**を行います。  
（例：一意性チェック、存在確認、状態遷移制御など）
```csharp
public async Task<ValidationResult> Validation(UserCreateDto dto)
{
    var result = new ValidationResult
    if (await _userRepository.GetByAny(u => u.Email == dto.Email))
    {
        result.AddFieldError(new ValidFieldError(nameof(dto.Email), "メッセージ"));
        return result;
    }
}
```

### 3.2 マッピング

### 3.3 トランザクション管理

