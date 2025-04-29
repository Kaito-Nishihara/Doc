# DTO (Data Transfer Object) 実装ガイド
**- ASP.NET Core WebAPI 標準設計手順 -**

---

## 1. 概要

本ドキュメントは、ASP.NET Core WebAPI開発において、  
**DTO（Data Transfer Object）** を設計・実装する標準手順をまとめたものです。

DTOは、  
- リクエストとレスポンスを明確に分離し、  
- エンティティ（DBモデル）を直接外部公開しないために使用します。

---

## 2. DTO設計の基本方針

| 項目                                | 方針                                                    |
| :---------------------------------- | :------------------------------------------------------ |
| エンティティとDTOは明確に区別する   | 直接Entityを返さず、必ずDTOに変換する                   |
| 用途別にDTOを分ける                 | Create/Update/Getで異なるDTOを用意する                  |
| バリデーションルールはDTOに持たせる | `[Required]`, `[StringLength]`, `[Range]`などを付与する |
| シンプルな構造を心がける            | できるだけ必要最小限のプロパティだけ持たせる            |

---

## 3. DTO作成手順
- 必要なフィールドだけを持つ（内部情報を含めない）
### 3.1 一覧・詳細表示用DTO (Read用)

```csharp
public class UserDto
{
    public int Id { get; set; }

    public string Name { get; set; } = string.Empty;

    public string Email { get; set; } = string.Empty;
}
```
### 3.2 新規作成用DTO (Create用)
- 必須項目は `[Required]` で明示する

- 長さ制限・形式制約は適切にアノテーションを付ける

```csharp
public class UserCreateDto
{
    [Required]
    [StringLength(50)]
    public string Name { get; set; } = string.Empty;

    [Required]
    [EmailAddress]
    public string Email { get; set; } = string.Empty;
}
```

### 3.3 更新用DTO (Update用)
- 基本的にはCreateDtoと似るが、必要に応じて差別化できる

  - 例：Updateでは一部フィールドだけ更新可にしたい場合など
```csharp
public class UserUpdateDto
{
    [Required]
    [StringLength(50)]
    public string Name { get; set; } = string.Empty;

    [Required]
    [EmailAddress]
    public string Email { get; set; } = string.Empty;
}
```



## 4. バリデーションアノテーション一覧
- 基本的なアノテーションは下記となる
  
| アノテーション        | 説明                   |
| :-------------------- | :--------------------- |
| `[Required]`          | 必須項目               |
| `[StringLength(max)]` | 文字列長の上限設定     |
| `[Range(min, max)]`   | 数値の範囲制限         |
| `[EmailAddress]`      | メールアドレス形式制限 |
| `[Phone]`             | 電話番号形式制限       |




- その他のアノテーションは下記を参照すること<br>
[ASP.NET Core MVC および Razor Pages でのモデルの検証](https://learn.microsoft.com/ja-jp/aspnet/core/mvc/models/validation?view=aspnetcore-9.0#built-in-attributes)

- これらのバリデーションエラーは自動で400 BadRequestとして返されます。