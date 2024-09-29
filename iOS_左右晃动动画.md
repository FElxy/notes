在用户没有勾选协议时，需要通过一个左右晃动的动画提醒用户查看

```swift
    func shakeCommonView(view: UIView) {
        let animation = CAKeyframeAnimation(keyPath: "position")
        animation.duration = 0.2
        animation.repeatCount = 1
        animation.autoreverses = true
        let points: [CGPoint] = [
            CGPoint(x: view.center.x, y: view.center.y),
            CGPoint(x: view.center.x - 5, y: view.center.y),
            CGPoint(x: view.center.x + 10, y: view.center.y),
            CGPoint(x: view.center.x - 5, y: view.center.y)
        ]

        animation.values = points.map { NSValue(cgPoint: $0) }
        
        view.layer.add(animation, forKey: "position")
    }

```

调用时

```swift
    func shakeView() {
        shakeCommonView(view: agreeBtn)
        shakeCommonView(view: privacyLabel)
        
        // 下面的用来做checkbox勾选图像替换，另一种提醒
        // agreeBtn.setImage(UIImage.vv_imageNamed("file_unselect_remind", inBundle: "VSFile"), for: .normal)
        // 延迟 500 毫秒后执行图像更改
        // DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
        //    self.agreeBtn.setImage(UIImage.vv_imageNamed("file_multi_unselect", inBundle: "VSFile"), for: .normal)
        // }
    }

```