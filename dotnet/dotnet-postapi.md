## 1. DTO作成（追加処理用）
- /Dtos フォルダ配下に作成します。
```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApp.Dtos
{
    public class UserCreateDto
    {
        [Required]
        [MaxLength(50)]
        public string Name { get; set; } = string.Empty;

        [Required]
        [EmailAddress]
        public string Email { get; set; } = string.Empty;
    }
}

```

### 注意事項
- **ID（主キー）**はCreateDtoには持たせない。
- バリデーションを必ず付与して、Service層やController層で検証漏れを防ぐ。

## 2. サービスクラス作成
- 保存対象のデータには、必ずログイン中ユーザーのIDなど所有者情報を設定する。

- 不正な登録操作を防ぐため、入力値の整合性チェックを行う。
- 
```csharp
public async Task<int> CreateAsync(UserCreateDto input, int currentUserId)
{
    var user = new User
    {
        Name = input.Name,
        Email = input.Email,
        OwnerId = currentUserId // 必ず所有者情報を埋め込む！
    };

    await _repository.AddAsync(user);

    return user.Id;
}
```