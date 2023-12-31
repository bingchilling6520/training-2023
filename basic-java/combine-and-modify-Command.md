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
