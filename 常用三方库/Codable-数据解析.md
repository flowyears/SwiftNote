

**Swift Codable 详解**  
Codable 是 Swift 中最强大的协议之一，用于实现数据模型与外部数据格式（如 JSON、Property List）之间的 **序列化（编码）** 和 **反序列化（解码）**。它通过编译时代码生成，兼顾了类型安全和开发效率。以下是其核心机制、高级用法和实战技巧的全面解析。

---

### **一、Codable 的核心概念**
#### 1. **协议定义**
- `Encodable`: 定义如何将对象 **编码（序列化）** 为外部数据。
- `Decodable`: 定义如何从外部数据 **解码（反序列化）** 为对象。
- `Codable`: 类型别名，组合了 `Encodable` 和 `Decodable`。
  ```swift
  typealias Codable = Encodable & Decodable
  ```

#### 2. **自动合成（Auto-Synthesis）**
Swift 编译器会自动为满足条件的类型生成 `Codable` 实现，条件包括：
- 所有存储属性都遵循 `Codable`。
- 没有自定义的编码/解码逻辑。
  ```swift
  struct User: Codable {
      let id: Int
      let name: String
      let createdAt: Date
  }
  ```

---

### **二、基础用法**
#### 1. **JSON 编码与解码**
```swift
import Foundation

// JSON 字符串
let json = """
{
    "id": 123,
    "name": "John",
    "createdAt": "2023-10-01T12:00:00Z"
}
"""

// 解码（JSON → Model）
let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .iso8601  // 指定日期格式
let user = try decoder.decode(User.self, from: json.data(using: .utf8)!)
print(user.name) // "John"

// 编码（Model → JSON）
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted
encoder.dateEncodingStrategy = .iso8601
let jsonData = try encoder.encode(user)
print(String(data: jsonData, encoding: .utf8)!)
```

#### 2. **支持的格式**
- **JSON**：通过 `JSONEncoder`/`JSONDecoder`。
- **Property List**：通过 `PropertyListEncoder`/`PropertyListDecoder`。
- **自定义格式**：可手动实现 `Encoder`/`Decoder`（如 XML、CSV）。

---

### **三、高级用法**
#### 1. **处理键名不一致**
通过 `CodingKeys` 枚举映射 JSON 键与属性名：
```swift
struct User: Codable {
    let id: Int
    let fullName: String
    let createdAt: Date

    enum CodingKeys: String, CodingKey {
        case id
        case fullName = "name"  // JSON 键为 "name"
        case createdAt
    }
}
```

#### 2. **自定义日期格式**
```swift
let decoder = JSONDecoder()
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy-MM-dd HH:mm:ss"
decoder.dateDecodingStrategy = .formatted(dateFormatter)
```

#### 3. **嵌套容器与复杂结构**
处理多层嵌套的 JSON：
```swift
struct Post: Codable {
    let title: String
    let author: User
    let comments: [Comment]
}

let post = try decoder.decode(Post.self, from: jsonData)
```

#### 4. **动态类型（多态解析）**
使用 `JSONDecoder` 的 `userInfo` 传递上下文：
```swift
protocol Animal: Codable {
    var type: String { get }
}

struct Dog: Animal {
    let type = "dog"
    let name: String
}

struct Cat: Animal {
    let type = "cat"
    let color: String
}

// 注册类型到 userInfo
let decoder = JSONDecoder()
decoder.userInfo = [CodingUserInfoKey.animalTypeKey: "type"]

// 根据 "type" 字段动态解析
let animals = try decoder.decode([Animal].self, from: jsonData)
```

#### 5. **忽略某些字段**
不在 `CodingKeys` 中声明的属性会被自动忽略：
```swift
struct User: Codable {
    let id: Int
    var tempToken: String? // 不参与编解码

    enum CodingKeys: String, CodingKey {
        case id
    }
}
```

---

### **四、错误处理**
#### 1. **常见错误类型**
- `DecodingError.dataCorrupted`: JSON 格式错误。
- `DecodingError.keyNotFound`: 缺少必需的键。
- `DecodingError.typeMismatch`: 类型不匹配。

#### 2. **捕获并处理错误**
```swift
do {
    let user = try decoder.decode(User.self, from: jsonData)
} catch let DecodingError.keyNotFound(key, context) {
    print("缺少键: \(key.stringValue), 上下文: \(context.debugDescription)")
} catch {
    print("其他错误: \(error)")
}
```

---

### **五、性能优化**
#### 1. **减少 `Codable` 开销**
- 避免频繁编解码大型数据，缓存结果。
- 使用 `JSONSerialization` 预解析后选择性解码。

#### 2. **自定义编解码逻辑**
手动实现 `init(from:)` 和 `encode(to:)` 优化性能：
```swift
struct HighPerformanceModel: Codable {
    let values: [Int]

    init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        values = try container.decode([Int].self)
    }

    func encode(to encoder: Encoder) throws {
        var container = encoder.singleValueContainer()
        try container.encode(values)
    }
}
```

---

### **六、与其他库的对比**
| **特性**         | **Codable**                          | **第三方库（如 SwiftyJSON）**      |
|------------------|--------------------------------------|-----------------------------------|
| **类型安全**     | 强类型，编译时检查                   | 运行时类型推断，可能崩溃          |
| **性能**         | 高效（编译器优化）                   | 依赖反射，性能略低                |
| **代码量**       | 自动合成，代码简洁                   | 需手动解析，代码量大              |
| **灵活性**       | 需遵守协议，灵活性受限               | 动态解析，处理不规则 JSON 更灵活  |

---

### **七、实战技巧**
#### 1. **处理可选值**
```swift
struct Product: Codable {
    let id: Int
    let price: Double? // 允许 JSON 中缺失该字段
}
```

#### 2. **默认值设置**
通过自定义 `init(from:)` 实现：
```swift
struct Settings: Codable {
    let fontSize: Int

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        fontSize = try container.decodeIfPresent(Int.self, forKey: .fontSize) ?? 14
    }
}
```

#### 3. **继承类的编解码**
父类需实现 `Codable`，子类手动处理：
```swift
class Vehicle: Codable {
    var wheels: Int
}

class Car: Vehicle {
    var brand: String

    enum CodingKeys: String, CodingKey {
        case brand
    }

    required init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        brand = try container.decode(String.self, forKey: .brand)
        try super.init(from: decoder)
    }

    override func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(brand, forKey: .brand)
        try super.encode(to: encoder)
    }
}
```

---

### **八、适用场景**
1. **REST API 响应解析**  
   结合 `URLSession` 或 `Alamofire` 解析网络请求结果。
2. **本地数据持久化**  
   存储 `UserDefaults` 或文件时，将对象序列化为 JSON/PLIST。
3. **跨平台数据交换**  
   与后端或其他客户端（如 Android、Web）交换结构化数据。

---

**总结**  
Codable 通过编译器自动合成和灵活的自定义能力，成为 Swift 生态中处理数据序列化的首选方案。对于复杂场景（如动态类型、不规则 JSON），可结合手动编解码逻辑或第三方库（如 `SwiftyJSON`）补充其不足。掌握其核心机制和最佳实践，能显著提升开发效率和代码健壮性。