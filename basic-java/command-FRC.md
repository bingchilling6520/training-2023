# Lập trình trong FRC
### Command
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
- Để bắt đầu một command, ta có thể dùng Trigger hoặc tự schedule command.
