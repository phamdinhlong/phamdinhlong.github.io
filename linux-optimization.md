# Linux: Optimization

Tags: debian

Reduce disk I/O for the /tmp folder

Cài đặt tmpfs cho các hoạt động đọc và ghi tạm. Chuyển các hoạt động này lên RAM nhằm tăng tốc độ đọc ghi các các thông tin tải về để chuẩn bị xử lý. Ví dụ như thông tin cá cược bóng đá.

You can simply use your system RAM instead of your hard disk to speed up read/write operations for the /tmp folder. We talked about /tmp in article Directory System Structure.

```
nano /etc/fstab

### At the end of the file, add these two lines:
tmpfs /tmp tmpfs defaults,noexec,nosuid 0 0

### Save and reboot
```
