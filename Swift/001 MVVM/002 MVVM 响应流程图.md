以下是使用 Mermaid 绘制的[[001 MVVM]]代码示例 **MVVM 响应流程图**，清晰展示代码示例中的交互流程：
![[MVVM 响应流程图.svg]]
%%

```mermaid
sequenceDiagram
    participant User as 用户
    participant View as UserViewController
    participant ViewModel as UserViewModel
    participant Model as User

    Note over User,Model: 初始状态
    ViewModel->>Model: 初始化 User 数据
    ViewModel->>ViewModel: processDisplayData()
    ViewModel-->>View: 通过 updateUI 回调传递 UserDisplayData
    View->>View: 更新 UI 显示

    Note over User,Model: 用户点击按钮
    User->>View: 点击「切换VIP」按钮
    View->>ViewModel: 调用 toggleVIPStatus()
    ViewModel->>Model: 修改 isVIP 状态
    Model-->>ViewModel: 触发 didSet 监听
    ViewModel->>ViewModel: processDisplayData()
    ViewModel-->>View: 通过 updateUI 回调传递新 UserDisplayData
    View->>View: 更新 UI 显示新状态
```
%%


---

### 流程关键步骤解析

1. **初始化阶段**  
   - ViewModel 初始化时加载 Model 数据
   - 处理数据生成 `UserDisplayData`
   - 通过闭包将数据传递给 View 更新 UI

2. **用户交互阶段**  
   - 用户点击按钮触发 View 层事件
   - View 调用 ViewModel 的业务方法 `toggleVIPStatus()`
   - ViewModel 修改 Model 的 `isVIP` 状态
   - Model 的 `didSet` 监听触发数据处理逻辑

3. **数据反馈阶段**  
   - ViewModel 将处理后的 `UserDisplayData` 通过闭包回调传递给 View
   - View 根据最新数据更新界面元素

---

### 技术细节说明
1. **数据流向**  
   ```mermaid
   graph LR
   A[用户操作] --> B(View)
   B --> C{ViewModel}
   C --> D[Model]
   D --> C
   C --> B
   B --> E[UI更新]
   ```

2. **响应式机制**  
   - **ViewModel → View**：通过闭包 `updateUI` 实现数据推送
   - **Model → ViewModel**：通过 `didSet` 属性观察者监听变化

3. **无反向依赖**  
   - ViewModel 不持有 View 的强引用
   - 所有通信通过协议或闭包完成

---

### 扩展：完整组件交互图
```mermaid
graph TD
    A[UserViewController] -->|绑定| B[UserViewModel]
    B -->|操作| C[User Model]
    C -->|触发 didSet| B
    B -->|通过闭包通知| A
    A -->|显示数据| D[UI Labels]
    A -->|用户交互| E[UI Button]
    E -->|调用方法| B
```

通过以上图表，可以清晰理解 MVVM 模式中 **数据驱动** 和 **单向流动** 的核心设计思想。