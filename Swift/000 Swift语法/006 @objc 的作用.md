> 说明：在[[005 代码优化]]中用到 `@objc`.

在 Swift 中，`@objc` 是一个特性（Attribute），它的核心作用是 **将 Swift 的代码暴露给 Objective-C 运行时**。在 `handleBackgroundTap` 方法中使用 `@objc` 的原因和具体作用如下：

---

### **`@objc` 的作用**
#### 1. **支持 Objective-C 调用**
   - 允许 Objective-C 代码调用该方法（例如：通过选择器 `#selector`）。
   - 本例中，`UITapGestureRecognizer` 的初始化需要传递一个 Objective-C 兼容的选择器（Selector），因此必须标记为 `@objc`：
     ```swift
     addGestureRecognizer(UITapGestureRecognizer(
         target: self, 
         action: #selector(handleBackgroundTap) // 需要 @objc 支持
     ))
     ```

#### 2. **兼容 Objective-C 运行时特性**
   - 用于需要动态派发（Dynamic Dispatch）的场景，例如：
     - KVO（键值观察）
     - 实现 Objective-C 协议（Protocol）
     - 使用 `#selector` 获取方法引用

---

### **为什么这里必须用 `@objc`？**
- **手势识别器的回调机制**  
  `UITapGestureRecognizer` 的 `action` 参数依赖 Objective-C 的 **消息发送机制**，通过字符串形式的方法名（Selector）来绑定回调。  
  Swift 方法默认对 Objective-C 不可见，必须通过 `@objc` 显式暴露。

- **错误示例**（省略 `@objc` 时）：
  ```swift
  private func handleBackgroundTap() { ... }
  
  // 编译报错：Argument of '#selector' refers to a method that is not exposed to Objective-C
  ```

---

### **`@objc` 的常见使用场景**
| **场景**                | **示例**                                                                 |
|-------------------------|--------------------------------------------------------------------------|
| **选择器（Selector）**  | `#selector(handleTap)`                                                   |
| **KVO 监听**            | `@objc dynamic var progress: CGFloat`                                    |
| **实现 ObjC 协议**      | `class MyClass: NSObject, UITableViewDelegate { ... }`                   |
| **混合编程调用**         | Swift 方法需要被 Objective-C 类调用时                                     |

---

### **扩展知识**
#### 1. **`@objc` vs `@IBAction`**
   - `@IBAction` 会隐式添加 `@objc`，因此 Interface Builder 中的按钮事件回调不需要额外添加 `@objc`：
     ```swift
     @IBAction func buttonTapped(_ sender: UIButton) { ... } // 自动支持 Objective-C
     ```

#### 2. **动态性控制**
   - 使用 `@objc dynamic` 可以让属性支持 KVO：
     ```swift
     @objc dynamic var value: Int = 0
     ```

#### 3. **选择性暴露**
   - 可以限制只暴露特定方法给 Objective-C：
     ```swift
     @objc private func internalMethod() { ... } // 仅当前文件可见
     ```

---

### **代码优化建议**
如果方法仅在 Swift 内部使用且不涉及 Objective-C 交互，可以省略 `@objc`。但在需要与 Objective-C 运行时交互时（如手势回调、代理方法），必须显式添加。

```swift
// 正确写法（需要 Objective-C 可见性）
@objc private func handleBackgroundTap() {
    dismiss(animated: true)
}
```

---

### **总结**
`@objc` 是 Swift 与 Objective-C 混合编程的桥梁，在本例中它确保了手势识别器能正确绑定到 Swift 方法。理解这一机制对处理 iOS 框架的底层交互至关重要。