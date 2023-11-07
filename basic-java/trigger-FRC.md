# Lập trình trong FRC
### Trigger
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
