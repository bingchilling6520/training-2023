# Những class quan trọng khác trong code FRC
- Ngoài những class được ta tự tạo ra (Command, Subsystem,...) thì cũng có những class được tạo sẵn trong template và cũng rất quan trọng.
##  `RobotContainer`
- Class này là nơi dùng để liên kết Trigger với Command và tạo AutonomousCommand (Command lúc chạy tự động).
- Liên kết Trigger với Command thường được đặt ở hàm `void configureBindings()`. (Liên kết như thế nào đã được nói ở phần Trigger).
- VD:
``` java
  private void configureBindings() {
    new JoystickButton(JOY0, 1).whileTrue(intake_cmd); //Liên kết trigger của các nút (các nút có ID là 1, 2, 3 và 
    new JoystickButton(JOY0, 2).whileTrue(flywheel_cmd); // trên tay cầm JOY0) với các Command tuỳ ý.
    new JoystickButton(JOY0, 3).whileTrue(rotator_cmd);
  }
```
- Ở đây ta cũng có thể khai báo các Command cần sử dụng (ở trong VD bên trên là các Command `intake_cmd`, `flywheel_cmd`, ... cũng được khai báo ở class này)
- Hàm `Command getAutonomousCommand()` sẽ return cái Command mà sẽ chạy khi phần tự động bắt đầu. VD:
``` java
  public Command getAutonomousCommand() {
    return DriveStraightCmd; //Khi phần tự động bắt đầu, cái Command DriveStraightCmd sẽ chạy, khiến con bot đi thẳng.
  }
```
- Ta có thể thấy vì ta chỉ có thể trả về một command duy nhất ở hàm này, Command này phải thực hiện tất cả các tác vụ cần thiết ở phần tự động của chúng ta (di chuyển, nhặt bóng, ...) vì vậy ta thường sẽ phải kết hợp các Command với nhau bằng CommandGroup (được nói kĩ hơn ở phần CommandGroup).
## Robot
- Class này chứa các hàm mà sẽ được gọi vào các thời điểm, chế độ hoạt động khác nhau mà con Robot trải qua trên sân. (Chế độ lái tay, tự động trên sân, test,...).
- *Lưu ý*: Trong template thì các hàm này đã có code viết sẵn (những dòng này rất quan trọng), không được xoá những dòng code này nếu như không biết đang làm gì.
- Các hàm này đều sẽ có dạng:
  - `[Chế độ]Init()`: Được chạy một lần duy nhất ngay sau khi Robot vào chế độ này. (Giống hàm `initialize()` trong Command)
  - `[Chế độ]Periodic()`: Được chạy liên tục khi Robot đang ở trong chế độ này. (Giống hàm `execute()` trong Command)
- Các chế độ quan trọng bao gồm:
 - `robot`: bao gồm tất cả các chế độ, hay nói cách khác dù robot đang ở chế độ nào thì hàm `robotPeriodic` sẽ luôn chạy và `robotInit` sẽ chỉ chạy khi con bot được khởi động.
 - `disabled`: chế độ con bot được tắt/vô hiệu hoá (không phải là khi con bot bị ngắt điện mà chỉ đơn giản là một chế độ khi muốn con bot không thể di chuyển).
 - `autonomous`: chế độ bot chạy tự động.
 - `teleop`: chế độ điều khiển bot thủ công (dùng tay cầm).
- Các chế độ này có thể được chỉnh trên DriverStation:
![image](https://github.com/bingchilling6520/training-2023/assets/86175803/49e20ffc-745b-4f91-8614-89856e5eeadd)
- `teleop` chính là chế độ TeleOperated, `autonomous` tương ứng với Autonomous, còn chế độ `disabled` bắt đầu khi ấn nút Disable và kết thúc khi ấn nút Enable.
## Constant
- Class này là nơi để ta lưu những biến toàn cục (global variable) (và các biến thường cũng là hằng số luôn) mà ta muốn sử dụng ở tất cả các class khác nhau. Thay vì ở mỗi class ta lại phải khai báo các biến này, thì ta chỉ cần khai báo biến ở class `Constant` và rồi sử dụng `import static` để sử dụng ở tất cả các class.
- Ta còn có thể tạo 1 class con bên trong class constant này nếu ta muốn phân chia rạch ròi các loại biến ra với nhau hơn.
- VD: 1 class con chỉ khai báo các hằng số là ID của motor robot, 1 class con chỉ khai báo các hằng số là tốc độ tối đa của motor robot,...
- Khi khai báo các biến thì các biến này phải là `static` (để không cần tạo 1 Object class `Constant` mà vẫn truy cập vào dữ liệu được). [Đọc thêm](https://www.freecodecamp.org/news/static-variables-in-java/#:~:text=When%20a%20variable%20is%20declared,of%20the%20class%20are%20created.) về `static` nếu cần. Nếu biến là hằng số thì thêm cả `final` vào.
- Khi tạo class con thì class con đó cũng phải là 1 static class.
VD:
``` java
public final class Constants {
  public static class CAN_ID {
    // Drivebase (Talon SRX)
    public static final int LEFT_MASTER = 1; //ID của các động cơ lái drivebase
    public static final int LEFT_FOLLOW = 2;
    public static final int RIGHT_MASTER = 3;
    public static final int RIGHT_FOLLOW = 4;
  }

  public static class SPEED {
    // Drivebase
    public static double DRIVEBASE_LINEAR_SPEED = 0.4; //Tốc độ tối đa của các động cơ khác nhau
    public static double DRIVEBASE_ROTATE_SPEED = 0.4;
  
    // Intake
    public static double INTAKE_IN_SPD = -0.6;
    public static double INTAKE_OUT_SPD = 0.6;
  }
}
 
```
VD về import: 
``` java
import static frc.robot.Constants.CAN_ID.*; //Lưu ý phải có '.*'
```
- Bên dưới, ta gọi các biến trong `CAN_ID` mà không cần gọi `Constant.CAN_ID` trước đó nữa.
``` java
private WPI_TalonSRX leftMaster = new WPI_TalonSRX(LEFT_MASTER); //Gọi LEFT_MASTER thay vì Constant.CAN_ID.LEFT_MASTER
```
