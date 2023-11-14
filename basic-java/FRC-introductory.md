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
