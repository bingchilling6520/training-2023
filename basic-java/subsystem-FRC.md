# Lập trình trong FRC
### Subsystem
- Song song với code logic cho con Robot, ta cũng cần phải tương tác vật lý với môi trường bằng các motors và nhận thông tin từ môi trường bằng các loại cảm biến, sensors.
- Tất cả những cái gì liên quan đến giao tiếp trực tiếp với motor, sensor thì nên được viết code ở subsystem.
- Trong FRC, ta sẽ viết code để điều khiển trực tiếp motor và sensor vào một Class tự tạo mới mà `extends` class `SubsystemBase`, và tất cả các Class này gọi chung là Subsystem.
### Cách để tạo 1 Subsystem
- ![image](https://github.com/bingchilling6520/training-2023/assets/86175803/c4575fb2-1860-4334-bb92-6df554b9f5c5)
- Chuột phải vào folder Subsystem (ta nên để tất cả class Subsystem vào folder này để cho nó gọn chứ tạo Subsystem ở chỗ nào cũng được)
- Click vào `Create a new Class/Command` ở dưới cùng
![image](https://github.com/bingchilling6520/training-2023/assets/86175803/7fe07837-e599-4599-aace-0fb7ad31f64a)
- Chọn `Subsystem (A robot subsystem)`
![image](https://github.com/bingchilling6520/training-2023/assets/86175803/7c927353-a12e-4145-b0b7-d100663c97b8)
- Đặt tên cho Subsystem đó (đặt 1 cái tên nào dễ nhớ, dễ viết nhé) rồi ấn Enter
- Ta sẽ có được một đoạn code tạo sẵn như sau:
``` java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot.subsystems;

import edu.wpi.first.wpilibj2.command.SubsystemBase;

public class test extends SubsystemBase {
  /** Creates a new test. */
  public test() {}

  @Override
  public void periodic() {
    // This method will be called once per scheduler run
  }
}
```
### Cách viết class Subsystem
- *Subsystem trong các VD dưới đây là subsystem điều khiển các motor drivebase (các motor dùng để lái bot).*
- Constructor của class Subsystem là nơi ta có thể yêu cầu những thông số cần thiết để tạo nên class này, thường là các Subsystem khác. Trước khi viết Constructor, ta sẽ cần tạo field trong class để chứa các thông tin đó đã.
- VD:
``` java
  Gyro gyro; //Một subsystem khác lấy thông tin từ Gyro (cảm biến góc)
  public PIDSubsystem(Gyro _gyro) { //Constructor của class PIDSubsystem (PIDController là gì thì sẽ được học sau)
    gyro = _gyro;
  }
```
- Ngoài field subsystem ra, ta cũng thường có các field là Object chứa các API để giao tiếp với động cơ, sensor.
``` java
  private WPI_TalonSRX leftFrontMotor = new WPI_TalonSRX(LEFT_FRONT); //Tạo các Object WPI_TalonSRX, các Object này dùng để trực tiếp điều khiển
  private WPI_TalonSRX leftBackMotor = new WPI_TalonSRX(LEFT_BACK); //các motor mà dùng Talon. API của các motor này sẽ được nói kĩ hơn ở bài khác.
  private WPI_TalonSRX rightFrontMotor = new WPI_TalonSRX(RIGHT_FRONT);
  private WPI_TalonSRX rightBackMotor = new WPI_TalonSRX(RIGHT_BACK);
  private AHRS NavX = new AHRS(); //Tạo Object AHRS, dùng để lấy thông tin từ cảm biến góc (Gyro, Navx). API của NavX cũng sẽ được nói kĩ hơn ở bài sau
```
- **LƯU Ý**: Cần phải phân biệt một Object dùng để giao tiếp trực tiếp với phần cứng của con Robot (motor, sensors) và một Subsystem, một class ta tạo ra để khiến việc điều khiển những phần cứng đó dễ hơn. Trong một subsystem sẽ có một hoặc nhiều Object để giao tiếp trực tiếp với phần cứng.
- VD để phân biệt: Mỗi object giao tiếp với motor (của class WPI_TalonSRX cho Talon, WPI_VictorSPX cho Victor, ...) chỉ có thể dùng Method `set()` để thay đổi vận tốc quay của Motor tương ứng. Tuy nhiên một Subsystem Drivebase mà chúng ta sắp viết nên có các method không để trực tiếp điều chỉnh tốc độ của từng động cơ mà chỉ chỉnh vận tốc của bot và Subsystem nên tự biến đổi tốc độ bot đó thành tốc độ mỗi động cơ bánh xe rồi tự chỉnh tốc độ của các động cơ đó.
- Về constructor, các subsystem thường hoạt động độc lập với nhau nên hiếm có trường hợp ta ta dùng constructor để làm việc này. Ví dụ Subsystem Drivebase này cũng sẽ không cần đến subsystem khác.
- Ngoài ra, Constructor của Subsystem, cũng như Constructor của các class khác dùng để initialize (khởi tạo) các biến cần thiết nữa.
- Constructor của Subsystem drivebase hoàn chỉnh (nhiều Method ở đây trông có thể hơi lạ nên có thể vừa đọc code vừa đọc API động cơ nếu chưa nhớ hết):
``` java
public Drivebase() {
    //Brake mode
    leftFrontMotor.setNeutralMode(NeutralMode.Brake); //Chỉnh chế độ phanh động cơ thành phanh thay vì không phanh.
    leftBackMotor.setNeutralMode(NeutralMode.Brake);
    rightFrontMotor.setNeutralMode(NeutralMode.Brake);
    rightBackMotor.setNeutralMode(NeutralMode.Brake);

    rightFrontMotor.setInverted(true); //Đảo ngược chiều của động cơ bên phải (vì động cơ bên phải và bên trái ngược chiều nhau 
    rightBackMotor.setInverted(true); //nên phải để 1 trong 2 cái bị đảo ngược chiều).

    leftBackMotor.follow(leftFrontMotor); //Động cơ đằng sau làm giống y hệt động cơ đằng trước. (Nếu nó không có cùng tốc độ thì có thể hỏng bot)
    rightBackMotor.follow(rightFrontMotor);
  }
```
- Tiếp theo ta cần tự tạo các method cần thiết để hoàn thành các nhiệm vụ ta cần làm với con bot mà không phải giao tiếp trực tiếp với phần cứng khi viết code logic.
- VD: Subsystem Driverbase thì cần các method để di chuyển lên xuống, xoay, ... (cách lái đơn giản nhất, cơ khí dễ nhất và cũng dễ code nhất là tank drive thì thay vì các method để di chuyển lên xuống, xoay thì method sẽ xoay bánh bên trái và bên phải độc lập. Đọc thêm [tại đây](https://xiaoxiae.github.io/Robotics-Simplified-Website/drivetrain-control/tank-drive/)), bên dưới ta sẽ viết code cho ví dụ này.
- VD: Subsystem Robot Arm thì cần method để xoay Robot arm 1 góc nhất định và xoay đến 1 góc nhất định.
- VD một method để lái tank drive:
``` java
  public void drive (double leftSpeed, double rightSpeed) //Hàm drive nhận 2 tham số (parameter), vận tốc bánh trái, vận tốc bánh phải 
  {
    leftFrontMotor.set(leftSpeed); //Xoay motor bên trái với vận tốc yêu cầu
    rightFrontMotor.set(rightSpeed); //Xoay motor bên phải với vận tốc yêu cầu
  }
```
- Một subsystem có thể có nhiều các Method tự viết khác nhau, và khi xử lý logic thì KHÔNG BAO GIỜ được trực tiếp giao tiếp với phần cứng mà bắt buộc phải qua một Subsystem và sử dụng các method của nó.
- Method `periodic` trong 1 Subsystem sẽ được chạy lặp đi lặp lại mỗi loop của con Robot. Method này thường dùng để cập nhật thông tin liên tục về 1 cái gì đó (tốc độ, góc xoay, nhiệt độ động cơ, ...) hoặc xử lý thông tin liên quan đến subsystem liên tục. Lưu ý, vì hàm này được chạy mỗi loop nên là nên viết hàm này nhỏ gọn, nếu để nó quá phức tạp có thể khiến bot "lag".
VD:
``` java
  @Override
  public void periodic() {
    // Put velocity
    SmartDashboard.putNumber("Motor Left Front", leftFrontMotor.get()); //Cập nhật vận tốc của các motor drivebase lên ứng dụng SmartDashBoard
    SmartDashboard.putNumber("Motor Left Back", leftBackMotor.get());
    SmartDashboard.putNumber("Motor Right Front", rightFrontMotor.get());
    SmartDashboard.putNumber("Motor Right Back", rightBackMotor.get());
  }
```
