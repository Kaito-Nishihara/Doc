# コントローラー ページング実装ガイド

## 1. 概要

本ドキュメントは、ASP.NET Core WebAPIプロジェクトにおいて、  
**標準的なコントローラー（CRUD操作）** を実装する手順をまとめたものです。


## 2. 基本設計方針

| 項目           | 方針                                                       |
| :------------- | :--------------------------------------------------------- |
| HTTPメソッド   | GET / POST / PUT / DELETEを適切に使い分ける                |
| ルーティング   | リソースベース（例：`/api/users`）                         |
| 依存関係       | サービス層（IUserServiceなど）をDIする                     |
| 戻り値         | `ActionResult<T>` を使って状態コードとデータを統一的に返す |
| バリデーション | 必要に応じてDTOレベルでバリデーションを行う                |


## 3. サンプル実装例（UserController）
- すべて `async`/ `await` ベースで統一する
### 3.1 コントローラー定義

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController(IUserService userService) : ControllerBase
{

}
```
### 3.2 一覧取得（GET /api/users）
```csharp
[HttpGet]
public async Task<ActionResult<IEnumerable<UserDto>>> GetUsers()
{
    var users = await _userService.GetAllAsync();
    return Ok(users);
}
```

### 3.3 詳細取得（GET /api/users/{id}）
```csharp
[HttpGet("{id}")]
public async Task<ActionResult<UserDto>> GetUser(Guid id)
{
    var user = await _userService.GetByIdAsync(id);
    if (user is null)
        return NotFound(); // 必ず存在しない場合を考慮する
    return Ok(user);
}
```

### 3.4 新規作成（POST /api/users）
```csharp
[HttpPost]
public async Task<ActionResult<UserDto>> CreateUser([FromBody] UserCreateDto dto)
{
    var createdUser = await _userService.CreateAsync(dto);
    return CreatedAtAction(nameof(GetUser), new { id = createdUser.Id }, createdUser);
}
```

### 3.5 更新（PUT /api/users/{id}）
```csharp
[HttpPut("{id}")]
public async Task<IActionResult> UpdateUser(int id, [FromBody] UserUpdateDto dto)
{
    var success = await _userService.UpdateAsync(id, dto);
    if (!success)
        return NotFound();
    return NoContent();
}
```

### 3.6 削除（DELETE /api/users/{id}）
```csharp
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteUser(Guid id)
{
    var success = await _userService.DeleteAsync(id);
    if (!success)
        return NotFound();
    return NoContent();
}
```
