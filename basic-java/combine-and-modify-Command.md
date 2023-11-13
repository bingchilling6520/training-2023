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
