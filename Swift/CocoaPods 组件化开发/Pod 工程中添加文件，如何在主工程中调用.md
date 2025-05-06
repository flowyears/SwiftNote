在Pod 工程中添加文件，如何在主工程中调用
1.类和方法声明为 `public`
```Swift
public class RegionSelectorController: UIViewController {  // 使用 public 或 open
    public override func viewDidLoad() {  // 重写方法也需 public
        super.viewDidLoad()
    }
}
```
2.要使用的类要添加到对应的Target
![[1745232607713.png]]