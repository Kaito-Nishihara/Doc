# AutoMapperによるマッピング実装ガイド

---

## 1. 概要

本ドキュメントは、ASP.NET Coreプロジェクトにおいて、  
**AutoMapper**を使用してエンティティとDTO間のマッピングを自動化する方法をまとめたものです。

手動マッピングの記述量を減らし、保守性を向上させます。

---

## 2. AutoMapperとは？

- オブジェクト同士のプロパティコピーを自動化するライブラリ
- 「マッピング設定」を定義することで、明示的なコピーコードを書かずに済む
- プロパティ名が一致すれば自動でマッピングされる

---

## 3. セットアップ方法

### 3.1 NuGetパッケージインストール

```bash
dotnet add package AutoMapper
dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
```

### 3.2  DIコンテナへの登録（Startup.csやProgram.cs）
```csharp
builder.Services.AddAutoMapper(typeof(Program)); 
// or Assembly単位でマッピングプロファイルをスキャン
```

### 3.3 マッピングプロファイル作成
-  エンティティ単位（ドメイン単位）で分割する
```
/MappingProfiles
    UserMappingProfile.cs
    ProjectMappingProfile.cs
    OrderMappingProfile.cs
    CommonMappingProfile.cs

```
- AutoMapperでは、マッピング定義をProfileクラスにまとめます。
```csharp
using AutoMapper;

public class UserMappingProfile : Profile
{
    public UserMappingProfile()
    {
        CreateMap<User, UserDto>()
        .ForMember(dest => dest.StatusText, opt => opt.MapFrom(src => src.IsActive ? "有効" : "無効"))
        .ForMember(dest => dest.Name, opt => opt.MapFrom(src => string.IsNullOrEmpty(src.Name) ? "未設定" : src.Name));
        CreateMap<UserCreateDto, User>();
        reateMap<UserUpdateDto, User>();
    }
}
```

## 4 サービス層での使用例

### 4.1 コンストラクタ

```csharp
public class UserService(IMapper mapper) 
{
    //
}
```

### 4.2 マッピング実装
#### エンティティ → DTO

```csharp
public async Task<UserDto?> GetAsync(int id)
{
    var user = await userRepository.GetByIdAsync(u => u.Id == id);
    return user == null ? null : mapper.Map<UserDto>(user);
}
```

#### DTO → エンティティ

```csharp
public async Task<Guid> CreateAsync(UserCreateDto dto, Guid currentUserId)
{
    var user = mapper.Map<User>(dto);

    await userRepository.AddAsync(user);
    await userRepository.SaveAsync();

    return user.Id;
}
```

## 5. 注意事項
- コーディング規約に基づいて過剰に使用しない。
- マッピング設定は必ずProfileで一元管理する。
