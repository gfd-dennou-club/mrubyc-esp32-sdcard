# mrubyc-gem-sdcard
ESP32 SPI SD Card library.

## Description
This commit adds ESP32's SPI SD Host Controller support for mruby/c.  
You can use `stdio.h` and `driver/sdspi_host.h` APIs from mruby/c with this commit.  

## Warning
This program doesn't check arguments.  
So if you use wrongly(e.g. you passed wrong typed values or not enough count of arguments), it segfaults.

## Additional Information
(C) Matsue College of Technology.  
このプログラムは松江高専の授業中に作成されました．  

# Added APIs
## SDSPI class
This class have methods about SPI initialization and FileSystem mounting.  
#### Limitation

This class only have one SD card and first partition.  
You want to extend to be able to use multiple SD card, you should create instancing-able class about SD card.  
That class must hold `sdmmc_host_t` and `sdmmc_card_t`.  
mruby/c seems to have special method to create instance with additional space to remember C structures, but it is not documented and cannot guarantee the class have additional space DEFINITELY.  

If you want to see second and later partitions, you may create vfs program yourself because I can't find any API to look other partitions.

### SDSPI#spi_bus_initialize(mosi : Integer, miso : Integer, sclk : Integer) -> bool
Pass SPI pin numbers, MOSI(Master out slave in), MISO(Master in slave out), SCLK(SPI Clock).  
This method initializes SPI Bus.  
If success, return `true`, otherwise `false`.

### SDSPI#esp_vfs_fat_sdcard_mount(cs : Integer, mount_point : String) -> bool
Pass CS(Chip Select, also called Slave Select).  
2nd argument is a path where to mount on. e.g. `"/sdcard"`.  
This method initializes vfs module and mount file system.  
If success, return `true`, otherwise `false`.

### SDSPI#esp_vfs_fat_sdcard_unmount() -> bool
This method unmounts file system.  
If success, return `true`, otherwise `false`.

### SDSPI#spi_bus_free() : bool
This method frees the SPI Bus.  
This returns nothing.

## ESP32_STDIO class
This class provides `stdio.h` functions.  
#### Limitation
You can open up to 8 files.

### ESP32_STDIO#fopen(filename : String , openmode : String) -> Integer
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fopen.html  
This returns `file id` instead of `FILE` pointer. If fails, return `-1`.  

### ESP32_STDIO#fclose(fid : Integer) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fclose.html  
This need `file id` instead of `FILE` pointer.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fputs(fid : Integer, content : String) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fputs.html  
This need `file id` instead of `FILE` pointer and `String` instead of `char` pointer.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fputc(fid : Integer, content : String) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fputc.html  
This need `file id` instead of `FILE` pointer and `String` instead of `char`.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fwrite(fid : Integer, content : String, size : Integer, nitems : Integer) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fwrite.html  
This need `file id` instead of `FILE` pointer and `String` instead of `char`.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fputc(fid : Integer, content : String, size : Integer, nitems : Integer) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fputc.html  
This need `file id` instead of `FILE` pointer and `String` instead of `char`.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fgets(fid : Integer, length : Integer) -> String
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fgets.html  
This need `file id` instead of `FILE` pointer.  
If success, return content typed `String`, otherwise `nil`.

### ESP32_STDIO#fread(fid : Integer, size : Integer, nitems : Integer) -> String
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fread.html  
This need `file id` instead of `FILE` pointer.  
If success, return content typed `String`, otherwise `nil`.

### ESP32_STDIO#fgetc(fid : Integer, length : Integer) -> String
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fgetc.html  
This need `file id` instead of `FILE` pointer.  
If success, return content typed `String`, otherwise `nil`.

### ESP32_STDIO#fflush(fid : Integer) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fflush.html  
This need `file id` instead of `FILE` pointer.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fgetpos(fid : Integer) -> Integer
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fgetpos.html  
This need `file id` instead of `FILE` pointer.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fsetpos(fid : Integer) -> Integer
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fsetpos.html  
This need `file id` instead of `FILE` pointer.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#fseek(fid : Integer, offset : Integer, whence : Integer) -> Integer
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/fseek.html  
This need `file id` instead of `FILE` pointer.  
`SEEK_SET` is `0`, `SEEK_CUR` is `1`, `SEEK_END` is `2`.  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#remove(filename : String) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/remove.html  
If success, return `true`, otherwise `false`.

### ESP32_STDIO#rename(src : String, dst : String) -> bool
See https://pubs.opengroup.org/onlinepubs/9699919799/functions/rename.html  
If success, return `true`, otherwise `false`.

## ESP32_DIRENT class
This class provides directory information and file time stamp API.  
### ESP32_DIRENT#childrenFiles(directory : Name) -> String[]
Enumerated files contained selected directory(argument `directory`).  
If succeeded, return array of String, name of files contained, otherwise `nil`.

### ESP32_DIRENT#childrenDirs(directory : Name) -> String[]
Enumerated directories contained selected directory(argument `directory`).  
If succeeded, return array of String, name of directories contained, otherwise `nil`.

### ESP32_DIRENT#children(directory : Name) -> String[]
Enumerated both files and directories contained selected directory(argument `directory`).  
If succeeded, return array of String, name of entries contained, otherwise `nil`.


### ESP32_DIRENT#fileTime(entryName : Name) -> ESP32_FILETIME
Get created date of given file.  
If succeeded, return `ESP32_FILETIME`, otherwise `nil`.  
`ESP32_FILETIME` have these members, all return values are `Integer`.  
`getYear()`, `getMonth()`, `getDay()`, `getDayOfWeek()`, `getHour()`, `getMinute()`, `getSecond()`.
