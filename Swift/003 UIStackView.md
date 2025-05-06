以下是关于 `UIStackView` 的详细介绍，结合其核心特性、使用场景和 Swift 实现示例：

---

### 一、UIStackView 是什么？
`UIStackView` 是 UIKit 中用于 **自动布局** 的容器视图，它能以 **线性排列方式**（水平或垂直）管理子视图的布局。通过抽象 Auto Layout 约束，大幅简化了视图层级的组织复杂度。

---

### 二、核心特性（对比传统 Auto Layout）
| **特性**               | **传统 Auto Layout**                     | **UIStackView**                          |
|------------------------|-----------------------------------------|-----------------------------------------|
| **布局方式**           | 需手动设置每个视图的约束                | 自动管理子视图的排列和间距              |
| **代码量**             | 约束代码冗长（尤其多视图）              | 只需设置排列方向和间距                  |
| **动态调整**           | 修改约束需额外操作                      | 动态增删子视图时自动调整布局            |
| **嵌套复杂度**         | 多层嵌套时约束易冲突                    | 天然支持嵌套，层级更清晰                |

---

### 三、关键属性详解（Swift 语法）
#### 1. **排列方向 (`axis`)**
```swift
stackView.axis = .horizontal // 水平排列（默认）
stackView.axis = .vertical   // 垂直排列
```

#### 2. **子视图分布方式 (`distribution`)**
```swift
// 控制子视图在主轴上的尺寸分配
stackView.distribution = .fill           // 填充可用空间（默认）
stackView.distribution = .fillEqually    // 等宽/等高
stackView.distribution = .fillProportionally // 按比例填充
stackView.distribution = .equalSpacing   // 等间距
stackView.distribution = .equalCentering // 等中心距
```

#### 3. **对齐方式 (`alignment`)**
```swift
// 控制子视图在交叉轴上的对齐
stackView.alignment = .fill     // 填充（默认）
stackView.alignment = .leading  // 左对齐（垂直排列时）
stackView.alignment = .top      // 顶部对齐（水平排列时）
stackView.alignment = .center   // 居中对齐
```

#### 4. **间距控制**
```swift
stackView.spacing = 8 // 子视图间固定间距
```

---

### 四、实战代码示例
#### 场景：创建水平排列的标签+输入框组合
```swift
let stackView = UIStackView()
stackView.axis = .horizontal
stackView.spacing = 10
stackView.distribution = .fillProportionally

let label = UILabel()
label.text = "用户名:"
label.setContentHuggingPriority(.required, for: .horizontal) // 阻止拉伸

let textField = UITextField()
textField.placeholder = "请输入"
textField.borderStyle = .roundedRect

stackView.addArrangedSubview(label)
stackView.addArrangedSubview(textField)

// 添加约束（通常需要固定 stackView 的位置）
stackView.translatesAutoresizingMaskIntoConstraints = false
NSLayoutConstraint.activate([
    stackView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
    stackView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
    stackView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20)
])
```

---

### 五、高级使用技巧
#### 1. **嵌套 StackView**
```swift
// 垂直 StackView 包裹两个水平 StackView
let verticalStack = UIStackView()
verticalStack.axis = .vertical
verticalStack.spacing = 20

let row1 = createHorizontalRow()
let row2 = createHorizontalRow()

verticalStack.addArrangedSubview(row1)
verticalStack.addArrangedSubview(row2)
```

#### 2. **动态增删子视图**
```swift
// 添加视图
let newView = UIView()
stackView.addArrangedSubview(newView)

// 移除视图
stackView.removeArrangedSubview(oldView)
oldView.removeFromSuperview() // 必须手动移除
```

#### 3. **隐藏子视图的自动布局**
当子视图 `isHidden = true` 时，StackView 会自动调整布局，无需修改约束。

---

### 六、注意事项（坑点总结）
1. **不要直接操作 `frame`**  
   StackView 通过 Auto Layout 管理布局，手动设置 `frame` 无效。

2. **性能优化**  
   嵌套过深（如超过 10 层）会影响渲染性能，建议结合 `UITableView` 使用。

3. **版本兼容性**  
   最低支持 iOS 9，如需兼容更老系统需使用第三方库（如 [OAStackView](https://github.com/oarrabi/OAStackView)）。

---

### 七、经典使用场景
| **场景**           | **实现方案**                         |
|--------------------|-------------------------------------|
| 表单布局           | 垂直 StackView + 多个水平 StackView  |
| 按钮工具栏         | 水平 StackView + `.fillEqually`     |
| 动态标签云         | 嵌套 StackView + 自动换行处理       |
| 图文混排           | 水平 StackView + UIImageView + UILabel |

---

### 八、官方学习资源
- [Apple 官方文档](https://developer.apple.com/documentation/uikit/uistackview)
- [UIStackView 入门视频教程](https://developer.apple.com/videos/play/wwdc2015/218/)
- [RayWenderlich 实战教程](https://www.raywenderlich.com/2195990-uistackview-tutorial-for-ios)

---

通过合理使用 `UIStackView`，可以减少 50% 以上的布局代码量。建议结合 Interface Builder 的 StackView 可视化操作，能进一步提升开发效率。