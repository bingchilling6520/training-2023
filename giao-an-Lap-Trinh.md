# Giải thích một trận đầu FRC và một vài thuật ngữ
- Trong một trấn đấu FRC sẽ có hai phần:
  - Auto: Bot chỉ được điều khiển bằng code, không được nhận bất cứ tín hiệu từ Driver (người điều khiển).
  - TeleOperated: Bot đã được nhận tín hiệu và điều khiển từ Driver (sử dụng app Driver Station).
# WPILib
- Đây là thư viện chứa hầu hết các chức năng cần thiết của con Robot FRC.
- Link tải: https://github.com/wpilibsuite/allwpilib/releases
# FRC Game tool
- Các app khác sử dụng hỗ trợ trong việc lập trình con Bot và các bộ phận điện tử của con Bot.
- Link tải: https://www.ni.com/en/support/downloads/drivers/download.frc-game-tools.html#479842
# Lập trình trong FRC
- Có hai paradigm (mẫu hình) thường được sử dụng trong code FRC
  - TimedRobot: Chạy giống mạch UNO, có một hàm khởi tạo, được gọi một lần duy nhất khi con Robot bắt đầu và một hàm lặp, chạy đi chạy lại, lặp liên tục trong lúc Robot được chạy. 
  - CommandRobot: Viết theo dạng viết các phần riêng biệt: Subsystem, xử lý tất cả logic phần cứng; Command và Trigger, Trigger sẽ xử lý khi nào Command bắt đầu và Command sẽ chạy các đoạn code cần thiết.
## Subsystem
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
## Command
- Không giống như các chương trình mà chúng ta hay code bình thường, viết 1 đoạn code xong rồi MT sẽ chạy nó tuần tự từ trên xuống dưới, các dòng code trong FRC thường sẽ chạy song song với nhau, ta chỉ cần viết đoạn code cần chạy, code khi nào nó chạy, và code khi nào nó dừng.
- VD: Khi code con bot đi thẳng, ta sẽ để điều kiện bắt đầu là ngay khi con bot được bật, viết đoạn code cần thiết để con bot đi thẳng, và để điều kiện dừng lại là khi nó đi được quãng đường/thời gian mong muốn.
- Trong FRC, ta sẽ viết code để chạy và điều kiện dừng ở một Class tự tạo mới mà `extends` class `CommandBase`, và tất cả các Class này gọi chung là Command
### Cách để tạo 1 Command
![image](https://github.com/bingchilling6520/training-2023/assets/86175803/c4575fb2-1860-4334-bb92-6df554b9f5c5)
- Chuột phải vào folder Command (ta nên để tất cả class Command vào folder này để cho nó gọn chứ tạo Command ở chỗ nào cũng được)
- Click vào `Create a new Class/Command` ở dưới cùng
![image](https://github.com/bingchilling6520/training-2023/assets/86175803/7fe07837-e599-4599-aace-0fb7ad31f64a)
- Chọn `Command (A command.)`
- Đặt tên cho Command đó (đặt 1 cái tên nào dễ nhớ, dễ viết nhé) rồi ấn Enter
- Ta sẽ có được một đoạn code tạo sẵn như sau:
``` java
// Copyright (c) FIRST and other WPILib contributors.
// Open Source Software; you can modify and/or share it under the terms of
// the WPILib BSD license file in the root directory of this project.

package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.CommandBase;

public class DriveStraight extends CommandBase {
  /** Creates a new DriveStraight. */
  public DriveStraight() {
    // Use addRequirements() here to declare subsystem dependencies.
  }

  // Called when the command is initially scheduled.
  @Override
  public void initialize() {}

  // Called every time the scheduler runs while the command is scheduled.
  @Override
  public void execute() {}

  // Called once the command ends or is interrupted.
  @Override
  public void end(boolean interrupted) {}

  // Returns true when the command should end.
  @Override
  public boolean isFinished() {
    return false;
  }
}

```
### Cách viết class Command
- Đầu tiên ta sẽ có Constructor của class này (trong VD đang là command để đi thẳng vĩnh viễn, với các command khác làm tương tự) nơi ta có thể yêu cầu những thông số cần thiết để tạo nên class này. Trong VD này, ta sẽ cần tốc độ di chuyển của con bot, Subsystem dùng để điều khiển, lái con bot. Trước khi viết Constructor, ta sẽ cần tạo field trong class để chứa các thông tin đó đã.
``` java
  float speed; //Tốc độ của con bot (khoảng từ 0 đến 1)
  Drivebase drivebase; //Subsystem điều khiển drivebase để lái con bot
```
``` java
public DriveStraight(float _speed, Drivebase _drivebase) {
    speed = _speed;
    drivebase = _drivebase;
  }
```
- Tiếp theo ta sẽ viết 3 Method
``` java
initialize()
```
``` java
execute()
``` 
``` java 
end(boolean interrupted)
```
- Những đoạn code trong initialize() sẽ chỉ chạy khi Command này lần đầu tiên chạy. (chạy trước execute của lần đó)
- Những đoạn code trong execute() sẽ chạy mỗi khi Command này được chạy.
- Những đoạn code trong end() sẽ chỉ chạy sau khi Command kết thúc. (chạy sau execute của lần đó)
- VD: Nếu như ta coi Command này như việc chạy bộ thì initialize() ta sẽ cần làm những việc như đi giày, ra khỏi nhà, ... (những việc này chỉ làm 1 lần  trong mỗi buổi chạy bộ và được làm trước khi bắt đầu chạy bộ). execute() thì ta sẽ chạy bộ (việc được lặp đi lặp lại liên tục trong suốt quá trình ta chạy bộ). end() thì ta sẽ trở về nhà, tắm, ... (những việc cũng chỉ làm 1 lần trong mỗi buổi chạy bộ nhưng mà làm sau khi đã chạy bộ xong).
- Cũng có thể coi initialize() là bước chuẩn bị cho việc chúng ta cần làm, execute() là làm cái việc đó, và sau khi làm bày bừa ra rồi thì end() là bước ta phải dọn dẹp lại những hậu quả xảy ra do ta làm cái việc đó.
- Trong VD về Command đi thẳng, đầu tiên ta cần viết initialize(). Muốn con Robot đi thẳng thì cần chuẩn bị cái gì trước không? Task này khá đơn giản nên ta không cần chuẩn bị gì cả, để trống hàm initialize():
``` java
@Override
public void initialize() {}
```
- Sau đó ta viết hàm execute(), chúng ta đang muốn đi thẳng, vậy ta sẽ viết đoạn code giúp con bot đi thẳng trong hàm này:
``` java
@Override
public void execute() {
    drivebase.driveStraight(speed); //Hàm driveStraight sẽ lái con bot đi thẳng với vận tốc là 'speed'
  }
```
- Cuối cùng ta viết hàm end(), khi mà con bot đi thẳng xong rồi, đến được đích đến rồi thì ta sẽ cần nó dừng lại, không đi thẳng tiếp nữa:
``` java
  @Override
  public void end(boolean interrupted) {
    drivebase.stop(); //Hàm stop sẽ dừng mọi động cơ và khiến con bot dừng lại
  }
```
- Vậy nếu Command này được chạy, nó sẽ bắt đầu đi thẳng... nhưng nó chưa có điều kiện dừng lại nên nó sẽ đi thẳng vĩnh viễn, ta có thể viết hàm isFinished() để ra điều kiện khi nào nó phải dừng:
``` java
@Override
  public boolean isFinished() {
    return false; //Không bao giờ dừng, đi thẳng vĩnh viễn
  }
```
``` java
  @Override
  public boolean isFinished() {
    return drivebase.distanceTravelled() >= 5; //Dừng khi con bot đi được 5m, hàm distanceTravelled() trả về khoảng cách con bot đã đi được với đơn vị là mét
  }
```
- Mỗi khi hàm execute() chạy thì ngay đấy hàm isFinished() sẽ được check, nếu hàm này trả về `true`, nghĩa là cái command này đã "finished" rồi, thì nó sẽ chạy end() và sau đó dừng lại, không chạy nữa.
### Bắt đầu một command
- Để bắt đầu một command, ta có thể dùng Trigger hoặc tự schedule command.
- Để tự schedule một command, ta lấy 1 object của class command đó và gọi hàm `schedule()`.
- VD:
``` java
  @Override
  public void teleopInit() { //Hàm này sẽ được giới thiệu ở phần Robot.java sau, chỉ cần biết nó giống như initialize() nhưng thay vì chạy trước 1 command thì nó chạy trước phần teleop (phần điều khiển bằng tay, ngược lại với phần auto, chạy bằng code)
    driveJoystickCommand.schedule(); //Bắt đầu command driveJoystick, nó đầu tiên sẽ chạy initalize() rồi chạy execute() đến khi nào isFinished() thì chạy end().
  }
```
## Trigger
- Như đã nói ở trong phần Command, để bắt đầu nó ta có thể sử dụng một Trigger. Ở phần này ta sẽ nói kĩ hơn về Trigger.
- Trigger nó giống như một cái còi báo hiệu trong thể thao, khi tiếng còi được cất lên thì trận đầu bắt đầu. Tương tự, khi nào một Trigger được "kích hoạt" thì các Command sẽ bắt đầu chạy.
- Tuy nhiên, ta sẽ muốn các Command chạy vào các thời điểm hoặc điều kiện khác nhau, vậy nên một Command sẽ chỉ nghe theo một Trigger mà thôi, các Trigger khác có được kích hoạt thì Command đó cũng sẽ không bắt đầu chạy.
- **Lưu ý:** Một Trigger có thể dùng cho nhiều Command khác nhau. Khi Trigger này được kích hoạt, tất cả các Command đang liên kết với nó sẽ cùng bắt đầu chạy.
- Các Trigger sẽ được kích hoạt vào thời điểm ngay khi một sự kiện nào đó gắn với Trigger đó xảy ra.
- VD: Một `BumpIntoWallTrigger` sẽ kích hoạt khi và chỉ khi con bot đâm vào tường (Trigger này được gắn với sự kiện bot đâm vào tường). Từ trigger này ta có thể bắt đầu các Command để dừng bot/lùi bot lại/... để con bot không tiếp tục đâm vào tường.
- Trigger thực ra chỉ đơn giản là 1 class chứa 1 cái boolean, nếu nó True thì là Trigger được kích hoạt, nếu nó False thì Trigger không được kích hoạt nên từ đây trở đi, ta sẽ gọi trạng thái kích hoạt của Trigger là True còn trạng thái không kích hoạt là False.
### Controller Trigger
- Một trong những sự kiện mà ta có thể gắn vào các Trigger đó là các sự kiện nút trên Controller (Controller XBox, PS, ...) được bấm.
- Class `Joystick` sẽ là class dùng để quản lí tất cả các input, đầu vào từ controller. Muốn tạo một `Joystick` ta chỉ cần đưa vào constructor ID của Joystick nó trên driverstation.
``` java
    public static final Joystick JOYSTICK0 = new Joystick(0); //ID 0: Joystick ở slot đầu tiên ở trên driverstation
    public static final Joystick JOYSTICK1 = new Joystick(1); //ID 1: Joystick ở slot thứ 2
```
- Class `JoystickButton` là class `extends` Trigger. Khi tạo 1 `JoystickButton` chỉ cần đưa vào đó ID của nút bấm muốn được gắn với Trigger này (VD: Nếu ID là 5 thì bất cứ khi nào nút có ID là 5 đó được bấm, Trigger đó sẽ được kích hoạt và tất cả các Command liên kết với Trigger đó sẽ bắt đầu chạy) và Object `Joystick` là cái controller của cái nút mà ta muốn gắn với Trigger
```java
  private JoystickButton button1= new JoystickButton(JOYSTICK0,1); //Trigger gắn với sự kiện nút có ID 1 ở trên JOYSTICK0 (Joystick ở slot đầu tiên) được bấm.
```
![Driverstation Tutorial](https://github.com/bingchilling6520/training-2023/assets/86175803/948ea73f-df16-4783-934c-f90e5bd5f0a6)

### Trigger từ `BooleanSupplier` (yêu cầu biết về [lambda](https://www.w3schools.com/java/java_lambda.asp) trước khi đọc phần này)
- Ngoài các sự kiện nút bấm trên Joystick ra, ta cũng có thể tự tạo một Trigger bằng `BooleanSupplier`.
- Ta có thể hiểu `BooleanSupplier` là một hàm với return type, loại trả về là boolean. Nếu boolean trả về là True, thì Trigger ấy sẽ được kích hoạt. Còn nếu là False, Trigger ấy sẽ không được kích hoạt.
- Sự kiện gắn với Trigger này sẽ là sự kiện khi mà `BooleanSupplier` gắn với Trigger này trả về True.
- Ta có thể viết `BooleanSupplier` là một hàm hoặc tiện hơn, một biểu thức lambda.
VD:
``` java
private Trigger HavingHighSpeedTrigger = new Trigger(() -> (speed > 100)); //Trigger gắn với sự kiện con bot đi quá nhanh (tốc độ có giá trị hơn 100);
```
### Liên kết Trigger với Command
- Ta sẽ liên kết Trigger với Command ở trong class `RobotContainer` và ở hàm `configureBindings()`.
- Để liên kết 1 Trigger với 1 Command, ta sử dụng các method ở trong class Trigger.
- `onTrue(Command)`: Liên kết Trigger này với Command được đưa vào làm parameter. Command sẽ chạy khi Trigger True.
- `onFalse(Command)`: Ngược lại với cái bên trên, Command sẽ chạy khi Trigger False.
- `toggleOnTrue(Command)`: Command sẽ chạy khi và chỉ khi Trigger chuyển từ False sang True. Nếu liên tục True thì Command sẽ không chạy.
- `toggleOnFalse(Command)`: Ngược lại với cái bên trên, Command sẽ chạy khi và chỉ khi Trigger chuyển từ True sang False. Nếu liên tục False thì Command sẽ không chạy.
- `whileTrue(Command)`/`whileFalse(Command)`: Command sẽ chạy khi Trigger đang True/False và bị buộc kết thúc (không cần điều kiện viết ở `isFinished()`) khi nó ở trạng thái ngược lại False/True.
VD:
``` java
  private void configureBindings() {
    button1.whileTrue(DriveStraightCommand); //Khi và chỉ khi nút có ID 1 trên Joystick0 (slot đầu tiên) được bấm (Trigger button1 True) thì command đi thẳng sẽ chạy
  }
```
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
## `Robot`
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
## `Constant`
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
# CommandGroup
- CommandGroup là cách để ta kết hợp các Command với nhau. Kết hợp các Command với nhau rất quan trọng vì hàm `getAutonomousCommand()` chỉ có thể trả về 1 Command (một CommandGroup gồm rất nhiều Command khác nhau cũng tính là một Command duy nhất).
## Các loại CommandGroup quan trọng
- `SequentialCommandGroup` là kết hợp các Command lại với nhau bằng cách chạy chúng một cách tuần tự.
- VD: Một `SequentialCommandGroup` kết hợp các Command: DriveStraight, TurnRight, RotateArm sẽ khiến con bot đi thắng, quay phải, rồi xoay tay Robot.
- `ParallelCommandGroup` là kết hợp các Command lại với nhau bằng cách chạy chúng đồng thời cùng nhau, (chạy song song với nhau).
- VD: Một `ParallelCommandGroup` kết hợp các Command: DriveStraight, RotateArm sẽ khiến bot vừa đi thẳng, vừa xoay tay Robot.
## Cách tạo ComamndGroup
- `extends` 1 trong các class CommandGroup kia và sử dụng hàm `addCommands(Command...)` để add các Command muốn kết hợp lại vào CommandGroup. Sau đó khai báo CommandGroup này và sử dụng nó như một cái Command bình thường.
- VD:
``` java
public class ComplexAuto extends SequentialCommandGroup {
  public ComplexAuto(DriveSubsystem drive, HatchSubsystem hatch) {
    addCommands(
        // Đi thẳng đằng trước một khoảng cách
        new DriveDistance(AutoConstants.kAutoDriveDistance, AutoConstants.kAutoDriveSpeed,
                          drive),

        // Mở hatch
        new ReleaseHatch(hatch),

        // Lùi về đằng sau một khoảng cách
        new DriveDistance(AutoConstants.kAutoBackupDistance, -AutoConstants.kAutoDriveSpeed,
                          drive));
  }
}
```
- Ngoài ta ta cũng có thể tạo 1 CommandGroup bằng constructor của nó. Constructor có thể thêm bao nhiêu Command vào cũng được.
- VD:
``` java
new SequentialCommandGroup( //Tạo một CommandGroup đầu tiên vừa đi thắng vừa mở Hatch, sau đó đóng Hatch.
      new ParallelCommandGroup(
        driveStraight,
        openHatch
      ),
      closeHatch
    );
```
- Lưu ý, vì CommandGroup cũng là một loại Command, ta có thể thêm các CommandGroup vào 1 CommandGroup khác.
# Các Command chuyên dụng
- WPILib còn tạo ra các Command chuyên dụng cho các trường hợp hay dùng khác nhau.
- Để tạo các Command này ta có thể khai báo một Object của class hoặc tương ứng, dùng một static method (hay được gọi là một factory) của class `Commands` (chú ý không nhầm với class `Command`). Dưới đây sẽ ghi cả hai gộp chung với nhau, sự khác biệt duy nhất là thay vì ta đưa các parameter vào Constructor của Object thì ta sẽ đưa các parameter vào method tương ứng.
- Các Command này thường yêu cầu lambda function (tất cả những lần nhắc tới lambda function dưới đây sẽ tương đương với một `Runnable`). Ngoài ra, ta còn có thể thêm các Subsystem vào cuối các Command, đó sẽ là requirement cho của các Command này.
## Một số Command chuyên dụng thường dùng
- `InstantCommand`, `runOnce()`: Đưa vào trong đó một lambda function, và cái lambda function đó sẽ chạy một lần duy nhất rồi dừng lại. VD:
``` java
  /** Đóng hatch */
  public CommandBase grabHatchCommand() {
    return new InstantCommand(() -> m_hatchSolenoid.set(kForward));
  }

  /** Mở hatch. */
  public CommandBase releaseHatchCommand() 
    return new InstantCommand(() -> m_hatchSolenoid.set(kReverse));
  }
```
- `RunCommand`, `run()`: Đưa vào trong đó một lambda function, và cái lambda function đó sẽ chạy liên tục vĩnh viễn, không dừng lại (giống như kiểu khi `isFinished()` luôn trả về false). VD:
``` java
new RunCommand(() -> arcadeDrive()); //Một Command để chạy lái arcade
```
- `StartEndCommand`, `startEnd()`: Đưa vào trong này 2 lambda function, lambda function đầu tiên sẽ chạy khi Command bắt đầu còn lambda function thứ hai sẽ chạy khi Command kết thúc. VD:
``` java
Commands.StartEnd(
    // Bắt đầu xoay shooter với vận tốc 50%
    () -> m_shooter.shooterSpeed(0.5),
    // Dừng shooter khi Command kết thúc
    () -> m_shooter.shooterSpeed(0.0),
    // Thêm requirement cần thiết
    m_shooter
)
```
- `FunctionalCommand`: Đưa vào trong này 4 lambda function: 3 `Runnable` tương ứng với các hàm `void initialize()`, `void execute()`, `void end(boolean)` và một `BooleanSupplier` tương ứng với `boolean isFinished()`. Đây là cách để viết toàn bộ một Command đầy đủ nhưng mà không cần tạo một class riêng. VD:
``` java
new FunctionalCommand(
    // Reset encoder (một loại sensor) khi command bắt đầu
    m_robotDrive::resetEncoders,
    // Liên tục chạy con Robot đi thẳng trong khi Command đang chạy
    () -> m_robotDrive.arcadeDrive(kAutoDriveSpeed, 0),
    // Dừng lái khi Command kết thúc
    interrupted -> m_robotDrive.arcadeDrive(0, 0),
    // Điều kiện kết thúc là con robot đi được một khoảng cách cần thiết
    () -> m_robotDrive.getAverageEncoderDistance() >= kAutoDriveDistance,
    // Thêm requirement cần thiết
    m_robotDrive
)
```
- `WaitCommand`, `waitSeconds(double)`: Đưa vào trong này một `double`, Command này sẽ đợi chính xác số giây đó. Hay sử dụng để căn thời gian chính xác. VD:
``` java
return new SequentialCommandGroup(
      driveForward, // Con bot sẽ đi thẳng
      new WaitCommand(5.0); //Sau đó đợi 5 giây
      driveBackward //Sau đó đi lùi và kết thúc
    );
```

