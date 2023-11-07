# Các hàm của `WPI_TalonSRX`
- `setNeutralMode(NeutralMode)`: Khi tắt động cơ sẽ làm gì,
 - `NeutralMode.Brake`: Phanh
 - `NeutralMode.Coast`: Sau khi tắt động cơ thì động cơ sẽ trượt.
- VD:
```` java
    leftMaster.setNeutralMode(NeutralMode.Brake);
    leftFollow.setNeutralMode(NeutralMode.Brake);
    rightMaster.setNeutralMode(NeutralMode.Brake);
    rightFollow.setNeutralMode(NeutralMode.Brake);
````
- `setInverted(boolean)`: Có đổi chiều động cơ không, True là có, False là không. VD: `setInverted(true)` và `set(0.6)` sẽ tương đương với `setInverted(false)` và `set(-0.6)`.
- VD:
``` java
    rightMaster.setInverted(true); //Invert bánh bên phải để cả 2 bên trái phải sẽ 
    rightFollow.setInverted(true); //xoay cùng hướng với cùng một giá trị set()
```
- `follow(IMotorController)`: Hai động cơ follow sẽ luôn xoay cùng chiều và cùng tốc độ với nhau, hay dùng khi hai động cơ xoay cùng 1 thứ (VD như trong drivebase thì nếu 2 động cơ xoay lệch thì hỏng luôn hộp số)
``` java
    leftFollow.follow(leftMaster); //Motor đằng sau drivebase follow motor đằng trước
    rightFollow.follow(rightMaster); //Để ngược lại ra cùng một kết quả
```
