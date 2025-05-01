# Entity 実装ガイド


---

## 1. 概要

本ドキュメントは、ASP.NET Core における  
Entity Framework Core を使用した **エンティティクラス（Model）実装の標準ルール** をまとめたものです。

- データベースマッピングの一貫性
- 命名規則・型設計の標準化
- 複雑なリレーションや制約の記述方法

---


## 2. エンティティ実装基本ルール
- エンティティ名は単数形で宣言する（`User`  ）
### 2.1 基底property

| 要素           | 命名例       | 規則                                                       |
| :------------- | :----------- | :--------------------------------------------------------- |
| 主キー         | `Id`         | 全エンティティ共通。型は `Guid` を使用                     |
| 外部キー       | `XxxId`      | 対応エンティティ名 + `Id` 形式で命名                       |
| 作成日時       | `CreatedAt`  | デフォルトで `DateTime.UtcNow` を設定。常に `IsRequired()` |
| 作成者         | `CreatedBy`  | 作成ユーザーID（Guidなど）。認証情報から注入               |
| 更新日時       | `UpdatedAt`  | 最終更新日時。更新時に自動で更新                           |
| 更新者         | `UpdatedBy`  | 最終更新ユーザーID。更新時に認証情報から注入               |
| 論理削除フラグ | `IsDeleted`  | 論理削除に使用（bool型）。ハード削除を避ける場合に利用     |
| 楽観的同時制御 | `RowVersion` | `byte[]`型。`[Timestamp]` 属性を付与して自動バージョン管理 |

---

#### 実装例：`BaseEntity`

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

public abstract class BaseEntity
{
    [Key]
    public int Id { get; set; }

    [Required]
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;

    [Required]
    public Guid CreatedBy { get; set; }

    public DateTime? UpdatedAt { get; set; }

    public Guid? UpdatedBy { get; set; }

    [Required]
    public bool IsDeleted { get; set; } = false;

    [Timestamp]
    public byte[]? RowVersion { get; set; }
}
```


## 3. 実装例：単純なエンティティ

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

public class User: BaseEntity
{
    [Required]
    [StringLength(100)]
    public string Name { get; set; } = string.Empty;

    [EmailAddress]
    [StringLength(200)]
    public string? Email { get; set; }

    [Phone]
    public string? PhoneNumber { get; set; }

}
```
## 4. リレーションの定義

### 4.1 1対多（One-to-Many）

```csharp
public class Project: BaseEntity
{

    public string Name { get; set; } = string.Empty;

    // ナビゲーションプロパティ
    public ICollection<Task> Tasks { get; set; } = new List<Task>();
}

public class Task: BaseEntity
{
    public string Title { get; set; } = string.Empty;

    // 外部キー + ナビゲーション
    public int ProjectId { get; set; }

    public Project Project { get; set; } = null!;
}

    modelBuilder.Entity<Project>(entity =>
    {
        // 1対多リレーションの定義
        entity.HasMany(p => p.Tasks)
              .WithOne(t => t.Project)
              .HasForeignKey(t => t.ProjectId)
              .OnDelete(DeleteBehavior.Restrict); // または Cascade
    });

```

### 4.2 1対1（One-to-One）
```csharp
public class User : BaseEntity
{
    public UserProfile Profile { get; set; } = null!;
}

public class UserProfile : BaseEntity
{
    [Key, ForeignKey("User")]
    public int UserId { get; set; }

    public string Bio { get; set; } = string.Empty;

    public User User { get; set; } = null!;
}

modelBuilder.Entity<User>()
    .HasOne(u => u.Profile)
    .WithOne(p => p.User)
    .HasForeignKey<UserProfile>(p => p.UserId)
    .OnDelete(DeleteBehavior.Cascade);

```

### 4.3 多対多（Many-to-Many）【EF Core 5以降】

```csharp
public class Student : BaseEntity
{
    public ICollection<Course> Courses { get; set; } = new List<Course>();
}

public class Course : BaseEntity
{
    public ICollection<Student> Students { get; set; } = new List<Student>();
}

//EFが自動で中間テーブルを作成
modelBuilder.Entity<Student>()
    .HasMany(s => s.Courses)
    .WithMany(c => c.Students)
    .UsingEntity(j => j.ToTable("StudentCourses"));
```