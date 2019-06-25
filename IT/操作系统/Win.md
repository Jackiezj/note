# Win7使用问题

## 一. win7初始化操作

### 1. win7键位互换

- 修改注册表

  Win+R --> 键入regedit --> HKEY_LOCAL_MACHINE -> System -> CurrentControlSet -> Control -> KeyBoard Layout --> 右键New -> Binary value --> 重命名为Scancode Map --> 右键修改

  以下修改为 两个win与ctrl互换, caps与esc互换

  0000 00 00 00 00 00 00 00 00 
  0008 07 00 00 00 1D 00 5B E0 
  0010 5B E0 1D 00 01 00 3A 00

  0018 3A 00 01 00 1D E0 5C E0

  0020 5C E0 1D E0 00 00 00 00

  0028

  - 自定义修改方案

      解释：

      ```bash
      "Scancode Map"=hex:
      00,00,00,00,
      00,00,00,00,
      03,00,00,00,
      38,00,1D,00,
      1D,00,38,00,
      00,00,00,00
      ```

      > 头两行和最后一行，是固定的，都是8个0。
      >
      > 第3行，表示共更改了多少个按键，本例为3个，怎么算的？从第4行开始，到最末尾行（虽然严格来说末行不算），每行算1个，4、5、6行，刚好3个。
      >
      > 第4行，前后各4位，分别代表某个按键。本例为将38,00的按键映射为1D,00。38,00代表Left alt键，1D,00代表Left ctrl键。
      >
      > 第5行，同理，下一行即Left ctrl映射为Left alt。
      >
      > 第6行，已经讲了，末行，固定8个0。如果需要增加更多的映射，可以在此行之上，不断加类似4、5行格式的。当然，最后的结果，还是要将各行合为一行的。

      - 按键映射码表

      ```
      Escape             01 00
      Tab          　　　 0F 00
      Caps Lock          3A 00
      Left Alt           38 00
      Left Ctrl          1D 00
      Left Shift         2A 00
      Left Windows       5B E0
      Right Alt          38 E0
      Right Ctrl         1D E0
      Right Shift        36 00
      Right Windows      5C E0
      Backspace          0E 00
      Delete             53 E0
      Enter              1C 00
      Space              39 00
      Insert             52 E0
      HOME               47 E0
      End                4F E0
      Num Lock           45 00
      Page Down          51 E0
      Page Up            49 E0
      Scroll Lock        46 00
      ```

- 重启生效

# windows下载安装

- **win10下载地址**

  <https://www.microsoft.com/en-us/software-download/windows10ISO>

- **win7下载地址**

  ```
  ed2k://|file|cn_windows_7_ultimate_with_sp1_x64_dvd_u_677408.iso|3420557312|B58548681854236C7939003B583A8078|/
  ```

