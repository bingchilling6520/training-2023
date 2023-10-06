# Lập trình trong FRC
### Trigger
- Như đã nói ở trong phần Command, để bắt đầu nó ta có thể sử dụng một Trigger. Ở phần này ta sẽ nói kĩ hơn về Trigger.
- Trigger nó giống như một cái còi báo hiệu trong thể thao, khi tiếng còi được cất lên thì trận đầu bắt đầu. Tương tự, khi nào một Trigger được "kích hoạt" thì các Command sẽ bắt đầu chạy.
- Tuy nhiên, ta sẽ muốn các Command chạy vào các thời điểm hoặc điều kiện khác nhau, vậy nên một Command sẽ chỉ nghe theo một Trigger mà thôi, các Trigger khác có được kích hoạt thì Command đó cũng sẽ không bắt đầu chạy.
- **Lưu ý:** Một Command nên chỉ lắng nghe một Trigger nhưng mà một Trigger có thể dùng cho nhiều Command khác nhau.
