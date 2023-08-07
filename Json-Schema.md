# JSON config

- [1. Property `JSON config > $schema`](#schema)
- [2. Property `JSON config > logo`](#logo)
  - [2.1. Property `JSON config > logo > oneOf > Disable logo`](#logo_oneOf_i0)
  - [2.2. Property `JSON config > logo > oneOf > Set the source file of the logo`](#logo_oneOf_i1)
  - [2.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations`](#logo_oneOf_i2)
    - [2.3.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > type`](#logo_oneOf_i2_type)
    - [2.3.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > source`](#logo_oneOf_i2_source)
    - [2.3.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color`](#logo_oneOf_i2_color)
      - [2.3.3.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 1`](#logo_oneOf_i2_color_1)
      - [2.3.3.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 2`](#logo_oneOf_i2_color_2)
      - [2.3.3.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 3`](#logo_oneOf_i2_color_3)
      - [2.3.3.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 4`](#logo_oneOf_i2_color_4)
      - [2.3.3.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 5`](#logo_oneOf_i2_color_5)
      - [2.3.3.6. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 6`](#logo_oneOf_i2_color_6)
      - [2.3.3.7. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 7`](#logo_oneOf_i2_color_7)
      - [2.3.3.8. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 8`](#logo_oneOf_i2_color_8)
      - [2.3.3.9. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 9`](#logo_oneOf_i2_color_9)
    - [2.3.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > width`](#logo_oneOf_i2_width)
    - [2.3.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > height`](#logo_oneOf_i2_height)
    - [2.3.6. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding`](#logo_oneOf_i2_padding)
      - [2.3.6.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > top`](#logo_oneOf_i2_padding_top)
      - [2.3.6.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > left`](#logo_oneOf_i2_padding_left)
      - [2.3.6.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > right`](#logo_oneOf_i2_padding_right)
    - [2.3.7. Property `JSON config > logo > oneOf > Fastfetch logo configurations > printRemaining`](#logo_oneOf_i2_printRemaining)
    - [2.3.8. Property `JSON config > logo > oneOf > Fastfetch logo configurations > preserveAspectRadio`](#logo_oneOf_i2_preserveAspectRadio)
    - [2.3.9. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa`](#logo_oneOf_i2_chafa)
      - [2.3.9.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > fgOnly`](#logo_oneOf_i2_chafa_fgOnly)
      - [2.3.9.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > symbols`](#logo_oneOf_i2_chafa_symbols)
      - [2.3.9.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > canvasMode`](#logo_oneOf_i2_chafa_canvasMode)
      - [2.3.9.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > colorSpace`](#logo_oneOf_i2_chafa_colorSpace)
      - [2.3.9.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > ditherMode`](#logo_oneOf_i2_chafa_ditherMode)
- [3. Property `JSON config > general`](#general)
  - [3.1. Property `JSON config > general > allowSlowOperations`](#general_allowSlowOperations)
  - [3.2. Property `JSON config > general > multithreading`](#general_multithreading)
  - [3.3. Property `JSON config > general > stat`](#general_stat)
  - [3.4. Property `JSON config > general > escapeBedrock`](#general_escapeBedrock)
  - [3.5. Property `JSON config > general > pipe`](#general_pipe)
  - [3.6. Property `JSON config > general > playerName`](#general_playerName)
  - [3.7. Property `JSON config > general > osFile`](#general_osFile)
  - [3.8. Property `JSON config > general > dsForceDrm`](#general_dsForceDrm)
  - [3.9. Property `JSON config > general > wmiTimeout`](#general_wmiTimeout)
- [4. Property `JSON config > display`](#display)
  - [4.1. Property `JSON config > display > showErrors`](#display_showErrors)
  - [4.2. Property `JSON config > display > disableLinewrap`](#display_disableLinewrap)
  - [4.3. Property `JSON config > display > hideCursor`](#display_hideCursor)
  - [4.4. Property `JSON config > display > separator`](#display_separator)
  - [4.5. Property `JSON config > display > color`](#display_color)
    - [4.5.1. Property `JSON config > display > color > oneOf > Set the both color of the keys and title`](#display_color_oneOf_i0)
    - [4.5.2. Property `JSON config > display > color > oneOf > item 1`](#display_color_oneOf_i1)
      - [4.5.2.1. Property `JSON config > display > color > oneOf > item 1 > keys`](#display_color_oneOf_i1_keys)
      - [4.5.2.2. Property `JSON config > display > color > oneOf > item 1 > title`](#display_color_oneOf_i1_title)
  - [4.6. Property `JSON config > display > brightColor`](#display_brightColor)
  - [4.7. Property `JSON config > display > binaryPrefix`](#display_binaryPrefix)
  - [4.8. Property `JSON config > display > sizeNdigits`](#display_sizeNdigits)
  - [4.9. Property `JSON config > display > sizeMaxPrefix`](#display_sizeMaxPrefix)
  - [4.10. Property `JSON config > display > temperatureUnit`](#display_temperatureUnit)
  - [4.11. Property `JSON config > display > percentType`](#display_percentType)
  - [4.12. Property `JSON config > display > noBuffer`](#display_noBuffer)
- [5. Property `JSON config > library`](#library)
  - [5.1. Property `JSON config > library > pci`](#library_pci)
  - [5.2. Property `JSON config > library > vulkan`](#library_vulkan)
  - [5.3. Property `JSON config > library > freetype`](#library_freetype)
  - [5.4. Property `JSON config > library > wayland`](#library_wayland)
  - [5.5. Property `JSON config > library > xcbRandr`](#library_xcbRandr)
  - [5.6. Property `JSON config > library > xcb`](#library_xcb)
  - [5.7. Property `JSON config > library > xrandr`](#library_xrandr)
  - [5.8. Property `JSON config > library > x11`](#library_x11)
  - [5.9. Property `JSON config > library > gio`](#library_gio)
  - [5.10. Property `JSON config > library > dconf`](#library_dconf)
  - [5.11. Property `JSON config > library > dbus`](#library_dbus)
  - [5.12. Property `JSON config > library > xfconf`](#library_xfconf)
  - [5.13. Property `JSON config > library > sqlite3`](#library_sqlite3)
  - [5.14. Property `JSON config > library > rpm`](#library_rpm)
  - [5.15. Property `JSON config > library > imagemagick`](#library_imagemagick)
  - [5.16. Property `JSON config > library > z`](#library_z)
  - [5.17. Property `JSON config > library > chafa`](#library_chafa)
  - [5.18. Property `JSON config > library > egl`](#library_egl)
  - [5.19. Property `JSON config > library > glx`](#library_glx)
  - [5.20. Property `JSON config > library > osmesa`](#library_osmesa)
  - [5.21. Property `JSON config > library > opencl`](#library_opencl)
  - [5.22. Property `JSON config > library > pulse`](#library_pulse)
  - [5.23. Property `JSON config > library > nm`](#library_nm)
  - [5.24. Property `JSON config > library > ddcutil`](#library_ddcutil)
- [6. Property `JSON config > modules`](#modules)
  - [6.1. JSON config > modules > modules items](#autogenerated_heading_2)
    - [6.1.1. Property `JSON config > modules > modules items > anyOf > Run module with default configurations`](#modules_items_anyOf_i0)
    - [6.1.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations`](#modules_items_anyOf_i1)
      - [6.1.2.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Break`](#modules_items_anyOf_i1_oneOf_i0)
        - [6.1.2.1.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Break > type`](#modules_items_anyOf_i1_oneOf_i0_type)
      - [6.1.2.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties`](#modules_items_anyOf_i1_oneOf_i1)
        - [6.1.2.2.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > type`](#modules_items_anyOf_i1_oneOf_i1_type)
        - [6.1.2.2.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > key`](#modules_items_anyOf_i1_oneOf_i1_key)
        - [6.1.2.2.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > keyColor`](#modules_items_anyOf_i1_oneOf_i1_keyColor)
        - [6.1.2.2.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > format`](#modules_items_anyOf_i1_oneOf_i1_format)
      - [6.1.2.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery`](#modules_items_anyOf_i1_oneOf_i2)
        - [6.1.2.3.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > type`](#modules_items_anyOf_i1_oneOf_i2_type)
        - [6.1.2.3.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > dir`](#modules_items_anyOf_i1_oneOf_i2_dir)
        - [6.1.2.3.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > temp`](#modules_items_anyOf_i1_oneOf_i2_temp)
        - [6.1.2.3.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > key`](#modules_items_anyOf_i1_oneOf_i2_key)
        - [6.1.2.3.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > keyColor`](#modules_items_anyOf_i1_oneOf_i2_keyColor)
        - [6.1.2.3.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > format`](#modules_items_anyOf_i1_oneOf_i2_format)
      - [6.1.2.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth`](#modules_items_anyOf_i1_oneOf_i3)
        - [6.1.2.4.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > type`](#modules_items_anyOf_i1_oneOf_i3_type)
        - [6.1.2.4.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > showDisconnected`](#modules_items_anyOf_i1_oneOf_i3_showDisconnected)
        - [6.1.2.4.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > key`](#modules_items_anyOf_i1_oneOf_i3_key)
        - [6.1.2.4.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > keyColor`](#modules_items_anyOf_i1_oneOf_i3_keyColor)
        - [6.1.2.4.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > format`](#modules_items_anyOf_i1_oneOf_i3_format)
      - [6.1.2.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU`](#modules_items_anyOf_i1_oneOf_i4)
        - [6.1.2.5.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > type`](#modules_items_anyOf_i1_oneOf_i4_type)
        - [6.1.2.5.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > temp`](#modules_items_anyOf_i1_oneOf_i4_temp)
        - [6.1.2.5.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > key`](#modules_items_anyOf_i1_oneOf_i4_key)
        - [6.1.2.5.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > keyColor`](#modules_items_anyOf_i1_oneOf_i4_keyColor)
        - [6.1.2.5.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > format`](#modules_items_anyOf_i1_oneOf_i4_format)
      - [6.1.2.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors`](#modules_items_anyOf_i1_oneOf_i5)
        - [6.1.2.6.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > type`](#modules_items_anyOf_i1_oneOf_i5_type)
        - [6.1.2.6.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > symbol`](#modules_items_anyOf_i1_oneOf_i5_symbol)
        - [6.1.2.6.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > paddingLeft`](#modules_items_anyOf_i1_oneOf_i5_paddingLeft)
      - [6.1.2.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command`](#modules_items_anyOf_i1_oneOf_i6)
        - [6.1.2.7.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > type`](#modules_items_anyOf_i1_oneOf_i6_type)
        - [6.1.2.7.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > shell`](#modules_items_anyOf_i1_oneOf_i6_shell)
        - [6.1.2.7.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > text`](#modules_items_anyOf_i1_oneOf_i6_text)
        - [6.1.2.7.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > key`](#modules_items_anyOf_i1_oneOf_i6_key)
        - [6.1.2.7.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > keyColor`](#modules_items_anyOf_i1_oneOf_i6_keyColor)
        - [6.1.2.7.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > format`](#modules_items_anyOf_i1_oneOf_i6_format)
      - [6.1.2.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom`](#modules_items_anyOf_i1_oneOf_i7)
        - [6.1.2.8.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > type`](#modules_items_anyOf_i1_oneOf_i7_type)
        - [6.1.2.8.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > key`](#modules_items_anyOf_i1_oneOf_i7_key)
        - [6.1.2.8.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > keyColor`](#modules_items_anyOf_i1_oneOf_i7_keyColor)
        - [6.1.2.8.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > format`](#modules_items_anyOf_i1_oneOf_i7_format)
      - [6.1.2.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display`](#modules_items_anyOf_i1_oneOf_i8)
        - [6.1.2.9.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > type`](#modules_items_anyOf_i1_oneOf_i8_type)
        - [6.1.2.9.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > compactType`](#modules_items_anyOf_i1_oneOf_i8_compactType)
        - [6.1.2.9.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > preciseRefreshRate`](#modules_items_anyOf_i1_oneOf_i8_preciseRefreshRate)
        - [6.1.2.9.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > key`](#modules_items_anyOf_i1_oneOf_i8_key)
        - [6.1.2.9.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > keyColor`](#modules_items_anyOf_i1_oneOf_i8_keyColor)
        - [6.1.2.9.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > format`](#modules_items_anyOf_i1_oneOf_i8_format)
      - [6.1.2.10. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk`](#modules_items_anyOf_i1_oneOf_i9)
        - [6.1.2.10.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > type`](#modules_items_anyOf_i1_oneOf_i9_type)
        - [6.1.2.10.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > folders`](#modules_items_anyOf_i1_oneOf_i9_folders)
        - [6.1.2.10.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showExternal`](#modules_items_anyOf_i1_oneOf_i9_showExternal)
        - [6.1.2.10.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showHidden`](#modules_items_anyOf_i1_oneOf_i9_showHidden)
        - [6.1.2.10.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showSubvolumes`](#modules_items_anyOf_i1_oneOf_i9_showSubvolumes)
        - [6.1.2.10.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showUnknown`](#modules_items_anyOf_i1_oneOf_i9_showUnknown)
        - [6.1.2.10.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > key`](#modules_items_anyOf_i1_oneOf_i9_key)
        - [6.1.2.10.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > keyColor`](#modules_items_anyOf_i1_oneOf_i9_keyColor)
        - [6.1.2.10.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > format`](#modules_items_anyOf_i1_oneOf_i9_format)
      - [6.1.2.11. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU`](#modules_items_anyOf_i1_oneOf_i10)
        - [6.1.2.11.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > type`](#modules_items_anyOf_i1_oneOf_i10_type)
        - [6.1.2.11.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > temp`](#modules_items_anyOf_i1_oneOf_i10_temp)
        - [6.1.2.11.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > forceVulkan`](#modules_items_anyOf_i1_oneOf_i10_forceVulkan)
        - [6.1.2.11.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > hideType`](#modules_items_anyOf_i1_oneOf_i10_hideType)
        - [6.1.2.11.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > key`](#modules_items_anyOf_i1_oneOf_i10_key)
        - [6.1.2.11.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > keyColor`](#modules_items_anyOf_i1_oneOf_i10_keyColor)
        - [6.1.2.11.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > format`](#modules_items_anyOf_i1_oneOf_i10_format)
      - [6.1.2.12. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP`](#modules_items_anyOf_i1_oneOf_i11)
        - [6.1.2.12.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > type`](#modules_items_anyOf_i1_oneOf_i11_type)
        - [6.1.2.12.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showIpv4`](#modules_items_anyOf_i1_oneOf_i11_showIpv4)
        - [6.1.2.12.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showIpv6`](#modules_items_anyOf_i1_oneOf_i11_showIpv6)
        - [6.1.2.12.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showMac`](#modules_items_anyOf_i1_oneOf_i11_showMac)
        - [6.1.2.12.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showLoop`](#modules_items_anyOf_i1_oneOf_i11_showLoop)
        - [6.1.2.12.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > compact`](#modules_items_anyOf_i1_oneOf_i11_compact)
        - [6.1.2.12.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > namePrefix`](#modules_items_anyOf_i1_oneOf_i11_namePrefix)
        - [6.1.2.12.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showDefaultRouteOnly`](#modules_items_anyOf_i1_oneOf_i11_showDefaultRouteOnly)
        - [6.1.2.12.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > key`](#modules_items_anyOf_i1_oneOf_i11_key)
        - [6.1.2.12.10. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > keyColor`](#modules_items_anyOf_i1_oneOf_i11_keyColor)
        - [6.1.2.12.11. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > format`](#modules_items_anyOf_i1_oneOf_i11_format)
      - [6.1.2.13. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL`](#modules_items_anyOf_i1_oneOf_i12)
        - [6.1.2.13.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > type`](#modules_items_anyOf_i1_oneOf_i12_type)
        - [6.1.2.13.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > library`](#modules_items_anyOf_i1_oneOf_i12_library)
        - [6.1.2.13.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > key`](#modules_items_anyOf_i1_oneOf_i12_key)
        - [6.1.2.13.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > keyColor`](#modules_items_anyOf_i1_oneOf_i12_keyColor)
        - [6.1.2.13.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > format`](#modules_items_anyOf_i1_oneOf_i12_format)
      - [6.1.2.14. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP`](#modules_items_anyOf_i1_oneOf_i13)
        - [6.1.2.14.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > type`](#modules_items_anyOf_i1_oneOf_i13_type)
        - [6.1.2.14.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > url`](#modules_items_anyOf_i1_oneOf_i13_url)
        - [6.1.2.14.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > timeout`](#modules_items_anyOf_i1_oneOf_i13_timeout)
        - [6.1.2.14.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > key`](#modules_items_anyOf_i1_oneOf_i13_key)
        - [6.1.2.14.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > keyColor`](#modules_items_anyOf_i1_oneOf_i13_keyColor)
        - [6.1.2.14.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > format`](#modules_items_anyOf_i1_oneOf_i13_format)
      - [6.1.2.15. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator`](#modules_items_anyOf_i1_oneOf_i14)
        - [6.1.2.15.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator > type`](#modules_items_anyOf_i1_oneOf_i14_type)
        - [6.1.2.15.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator > string`](#modules_items_anyOf_i1_oneOf_i14_string)
      - [6.1.2.16. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound`](#modules_items_anyOf_i1_oneOf_i15)
        - [6.1.2.16.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > type`](#modules_items_anyOf_i1_oneOf_i15_type)
        - [6.1.2.16.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > soundType`](#modules_items_anyOf_i1_oneOf_i15_soundType)
        - [6.1.2.16.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > key`](#modules_items_anyOf_i1_oneOf_i15_key)
        - [6.1.2.16.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > keyColor`](#modules_items_anyOf_i1_oneOf_i15_keyColor)
        - [6.1.2.16.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > format`](#modules_items_anyOf_i1_oneOf_i15_format)
      - [6.1.2.17. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title`](#modules_items_anyOf_i1_oneOf_i16)
        - [6.1.2.17.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > type`](#modules_items_anyOf_i1_oneOf_i16_type)
        - [6.1.2.17.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > fqdn`](#modules_items_anyOf_i1_oneOf_i16_fqdn)
        - [6.1.2.17.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color`](#modules_items_anyOf_i1_oneOf_i16_color)
          - [6.1.2.17.3.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > user`](#modules_items_anyOf_i1_oneOf_i16_color_user)
          - [6.1.2.17.3.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > at`](#modules_items_anyOf_i1_oneOf_i16_color_at)
          - [6.1.2.17.3.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > host`](#modules_items_anyOf_i1_oneOf_i16_color_host)
        - [6.1.2.17.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > key`](#modules_items_anyOf_i1_oneOf_i16_key)
        - [6.1.2.17.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > keyColor`](#modules_items_anyOf_i1_oneOf_i16_keyColor)
        - [6.1.2.17.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > format`](#modules_items_anyOf_i1_oneOf_i16_format)
      - [6.1.2.18. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather`](#modules_items_anyOf_i1_oneOf_i17)
        - [6.1.2.18.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > type`](#modules_items_anyOf_i1_oneOf_i17_type)
        - [6.1.2.18.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > location`](#modules_items_anyOf_i1_oneOf_i17_location)
        - [6.1.2.18.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > timeout`](#modules_items_anyOf_i1_oneOf_i17_timeout)
        - [6.1.2.18.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > outputFormat`](#modules_items_anyOf_i1_oneOf_i17_outputFormat)
        - [6.1.2.18.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > key`](#modules_items_anyOf_i1_oneOf_i17_key)
        - [6.1.2.18.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > keyColor`](#modules_items_anyOf_i1_oneOf_i17_keyColor)
        - [6.1.2.18.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > format`](#modules_items_anyOf_i1_oneOf_i17_format)
      - [6.1.2.19. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > type`](#modules_items_anyOf_i1_type)

**Title:** JSON config

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** JSON config file for fastfetch. Usually be `~/.config/fastfetch.jsonc`

| Property               | Pattern | Type        | Deprecated | Definition | Title/Description                                         |
| ---------------------- | ------- | ----------- | ---------- | ---------- | --------------------------------------------------------- |
| - [$schema](#schema )  | No      | string      | No         | -          | JSON schema URL, for JSON validation and IDE intelligence |
| - [logo](#logo )       | No      | Combination | No         | -          | -                                                         |
| - [general](#general ) | No      | object      | No         | -          | Fastfetch general configurations                          |
| - [display](#display ) | No      | object      | No         | -          | Configure how things to be displayed                      |
| - [library](#library ) | No      | object      | No         | -          | Set the path of a library to load                         |
| - [modules](#modules ) | No      | array       | No         | -          | Fastfetch modules to run                                  |

## <a name="schema"></a>1. Property `JSON config > $schema`

**Title:** JSON schema URL, for JSON validation and IDE intelligence

|              |                                                                             |
| ------------ | --------------------------------------------------------------------------- |
| **Type**     | `string`                                                                    |
| **Required** | No                                                                          |
| **Format**   | `uri`                                                                       |
| **Default**  | `"https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json"` |

## <a name="logo"></a>2. Property `JSON config > logo`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| One of(Option)                                    |
| ------------------------------------------------- |
| [Disable logo](#logo_oneOf_i0)                    |
| [Set the source file of the logo](#logo_oneOf_i1) |
| [Fastfetch logo configurations](#logo_oneOf_i2)   |

### <a name="logo_oneOf_i0"></a>2.1. Property `JSON config > logo > oneOf > Disable logo`

**Title:** Disable logo

|              |        |
| ------------ | ------ |
| **Type**     | `null` |
| **Required** | No     |

### <a name="logo_oneOf_i1"></a>2.2. Property `JSON config > logo > oneOf > Set the source file of the logo`

**Title:** Set the source file of the logo

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="logo_oneOf_i2"></a>2.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations`

**Title:** Fastfetch logo configurations

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                     | Pattern | Type             | Deprecated | Definition | Title/Description                                                                   |
| ------------------------------------------------------------ | ------- | ---------------- | ---------- | ---------- | ----------------------------------------------------------------------------------- |
| - [type](#logo_oneOf_i2_type )                               | No      | enum (of string) | No         | -          | Set the type of the logo given                                                      |
| - [source](#logo_oneOf_i2_source )                           | No      | string           | No         | -          | Set the source file of the logo                                                     |
| - [color](#logo_oneOf_i2_color )                             | No      | object           | No         | -          | Overwrite a color in the logo                                                       |
| - [width](#logo_oneOf_i2_width )                             | No      | integer          | No         | -          | Set the width of the logo (in characters). Required for iTerm image protocol        |
| - [height](#logo_oneOf_i2_height )                           | No      | integer          | No         | -          | Set the height of the logo (in characters). Required for iTerm image protocol       |
| - [padding](#logo_oneOf_i2_padding )                         | No      | object           | No         | -          | Set the padding of the logo                                                         |
| - [printRemaining](#logo_oneOf_i2_printRemaining )           | No      | boolean          | No         | -          | Whether to print the remaining logo, if it has more lines than modules to display   |
| - [preserveAspectRadio](#logo_oneOf_i2_preserveAspectRadio ) | No      | boolean          | No         | -          | Whether to preserve the aspect ratio of the logo. Supported by iTerm image protocol |
| - [chafa](#logo_oneOf_i2_chafa )                             | No      | object           | No         | -          | Chafa configuration. See chafa document for details                                 |

#### <a name="logo_oneOf_i2_type"></a>2.3.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > type`

**Title:** Set the type of the logo given

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"auto"`           |

Must be one of:
* "auto"
* "builtin"
* "small"
* "file"
* "file-raw"
* "data"
* "data-raw"
* "sixel"
* "kitty"
* "kitty-direct"
* "iterm"
* "chafa"
* "raw"
* "none"

#### <a name="logo_oneOf_i2_source"></a>2.3.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > source`

**Title:** Set the source file of the logo

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

#### <a name="logo_oneOf_i2_color"></a>2.3.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color`

**Title:** Overwrite a color in the logo

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                       | Pattern | Type             | Deprecated | Definition                               | Title/Description |
| ------------------------------ | ------- | ---------------- | ---------- | ---------------------------------------- | ----------------- |
| - [1](#logo_oneOf_i2_color_1 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 1           |
| - [2](#logo_oneOf_i2_color_2 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 2           |
| - [3](#logo_oneOf_i2_color_3 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 3           |
| - [4](#logo_oneOf_i2_color_4 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 4           |
| - [5](#logo_oneOf_i2_color_5 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 5           |
| - [6](#logo_oneOf_i2_color_6 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 6           |
| - [7](#logo_oneOf_i2_color_7 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 7           |
| - [8](#logo_oneOf_i2_color_8 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 8           |
| - [9](#logo_oneOf_i2_color_9 ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Color 9           |

##### <a name="logo_oneOf_i2_color_1"></a>2.3.3.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 1`

**Title:** Color 1

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_2"></a>2.3.3.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 2`

**Title:** Color 2

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_3"></a>2.3.3.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 3`

**Title:** Color 3

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_4"></a>2.3.3.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 4`

**Title:** Color 4

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_5"></a>2.3.3.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 5`

**Title:** Color 5

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_6"></a>2.3.3.6. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 6`

**Title:** Color 6

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_7"></a>2.3.3.7. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 7`

**Title:** Color 7

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_8"></a>2.3.3.8. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 8`

**Title:** Color 8

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="logo_oneOf_i2_color_9"></a>2.3.3.9. Property `JSON config > logo > oneOf > Fastfetch logo configurations > color > 9`

**Title:** Color 9

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

#### <a name="logo_oneOf_i2_width"></a>2.3.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > width`

**Title:** Set the width of the logo (in characters). Required for iTerm image protocol

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

#### <a name="logo_oneOf_i2_height"></a>2.3.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > height`

**Title:** Set the height of the logo (in characters). Required for iTerm image protocol

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

#### <a name="logo_oneOf_i2_padding"></a>2.3.6. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding`

**Title:** Set the padding of the logo

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                 | Pattern | Type    | Deprecated | Definition | Title/Description                 |
| ---------------------------------------- | ------- | ------- | ---------- | ---------- | --------------------------------- |
| - [top](#logo_oneOf_i2_padding_top )     | No      | integer | No         | -          | Set the top padding of the logo   |
| - [left](#logo_oneOf_i2_padding_left )   | No      | integer | No         | -          | Set the left padding of the logo  |
| - [right](#logo_oneOf_i2_padding_right ) | No      | integer | No         | -          | Set the right padding of the logo |

##### <a name="logo_oneOf_i2_padding_top"></a>2.3.6.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > top`

**Title:** Set the top padding of the logo

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="logo_oneOf_i2_padding_left"></a>2.3.6.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > left`

**Title:** Set the left padding of the logo

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="logo_oneOf_i2_padding_right"></a>2.3.6.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > padding > right`

**Title:** Set the right padding of the logo

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

#### <a name="logo_oneOf_i2_printRemaining"></a>2.3.7. Property `JSON config > logo > oneOf > Fastfetch logo configurations > printRemaining`

**Title:** Whether to print the remaining logo, if it has more lines than modules to display

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

#### <a name="logo_oneOf_i2_preserveAspectRadio"></a>2.3.8. Property `JSON config > logo > oneOf > Fastfetch logo configurations > preserveAspectRadio`

**Title:** Whether to preserve the aspect ratio of the logo. Supported by iTerm image protocol

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

#### <a name="logo_oneOf_i2_chafa"></a>2.3.9. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa`

**Title:** Chafa configuration. See chafa document for details

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                         | Pattern | Type             | Deprecated | Definition | Title/Description                                                                                        |
| ------------------------------------------------ | ------- | ---------------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------- |
| - [fgOnly](#logo_oneOf_i2_chafa_fgOnly )         | No      | boolean          | No         | -          | Produce character-cell output using foreground colors only                                               |
| - [symbols](#logo_oneOf_i2_chafa_symbols )       | No      | string           | No         | -          | Specify character symbols to employ in final output                                                      |
| - [canvasMode](#logo_oneOf_i2_chafa_canvasMode ) | No      | enum (of string) | No         | -          | Determine how colors are used in the output. This value maps the value of enum ChafaCanvasMode.          |
| - [colorSpace](#logo_oneOf_i2_chafa_colorSpace ) | No      | enum (of string) | No         | -          | Set color space used for quantization. This value maps the value of enum ChafaColorSpace.                |
| - [ditherMode](#logo_oneOf_i2_chafa_ditherMode ) | No      | enum (of string) | No         | -          | Set output dither mode (No effect with 24-bit color). This value maps the value of enum ChafaDitherMode. |

##### <a name="logo_oneOf_i2_chafa_fgOnly"></a>2.3.9.1. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > fgOnly`

**Title:** Produce character-cell output using foreground colors only

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="logo_oneOf_i2_chafa_symbols"></a>2.3.9.2. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > symbols`

**Title:** Specify character symbols to employ in final output

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="logo_oneOf_i2_chafa_canvasMode"></a>2.3.9.3. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > canvasMode`

**Title:** Determine how colors are used in the output. This value maps the value of enum ChafaCanvasMode.

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "TRUECOLOR"
* "INDEXED_256"
* "INDEXED_240"
* "INDEXED_16"
* "FGBG_BGFG"
* "FGBG"
* "INDEXED_8"
* "INDEXED_16_8"

##### <a name="logo_oneOf_i2_chafa_colorSpace"></a>2.3.9.4. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > colorSpace`

**Title:** Set color space used for quantization. This value maps the value of enum ChafaColorSpace.

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "RGB"
* "DIN99D"

##### <a name="logo_oneOf_i2_chafa_ditherMode"></a>2.3.9.5. Property `JSON config > logo > oneOf > Fastfetch logo configurations > chafa > ditherMode`

**Title:** Set output dither mode (No effect with 24-bit color). This value maps the value of enum ChafaDitherMode.

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "NONE"
* "ORDERED"
* "DIFFUSION"

## <a name="general"></a>3. Property `JSON config > general`

**Title:** Fastfetch general configurations

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                               | Pattern | Type    | Deprecated | Definition | Title/Description                                                         |
| ------------------------------------------------------ | ------- | ------- | ---------- | ---------- | ------------------------------------------------------------------------- |
| - [allowSlowOperations](#general_allowSlowOperations ) | No      | boolean | No         | -          | Allow operations that can be very slow for more detailed output           |
| - [multithreading](#general_multithreading )           | No      | boolean | No         | -          | Use multiple threads to detect values                                     |
| - [stat](#general_stat )                               | No      | boolean | No         | -          | Show time usage (in ms) for individual modules                            |
| - [escapeBedrock](#general_escapeBedrock )             | No      | boolean | No         | -          | On Bedrock Linux, whether to escape the bedrock jail                      |
| - [pipe](#general_pipe )                               | No      | boolean | No         | -          | Whether to enable pipe mode (disable logo and all escape sequences)       |
| - [playerName](#general_playerName )                   | No      | string  | No         | -          | The name of the player to use for module Media and Player. Linux only     |
| - [osFile](#general_osFile )                           | No      | string  | No         | -          | Set the path to the file containing OS information. Linux only            |
| - [dsForceDrm](#general_dsForceDrm )                   | No      | boolean | No         | -          | Force display detection to use \`/sys/class/drm\`. Linux only             |
| - [wmiTimeout](#general_wmiTimeout )                   | No      | integer | No         | -          | Set the timeout (ms) for WMI queries, \`-1\` for no timeout. Windows only |

### <a name="general_allowSlowOperations"></a>3.1. Property `JSON config > general > allowSlowOperations`

**Title:** Allow operations that can be very slow for more detailed output

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

### <a name="general_multithreading"></a>3.2. Property `JSON config > general > multithreading`

**Title:** Use multiple threads to detect values

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

### <a name="general_stat"></a>3.3. Property `JSON config > general > stat`

**Title:** Show time usage (in ms) for individual modules

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

### <a name="general_escapeBedrock"></a>3.4. Property `JSON config > general > escapeBedrock`

**Title:** On Bedrock Linux, whether to escape the bedrock jail

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

### <a name="general_pipe"></a>3.5. Property `JSON config > general > pipe`

**Title:** Whether to enable pipe mode (disable logo and all escape sequences)

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

### <a name="general_playerName"></a>3.6. Property `JSON config > general > playerName`

**Title:** The name of the player to use for module Media and Player. Linux only

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="general_osFile"></a>3.7. Property `JSON config > general > osFile`

**Title:** Set the path to the file containing OS information. Linux only

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="general_dsForceDrm"></a>3.8. Property `JSON config > general > dsForceDrm`

**Title:** Force display detection to use `/sys/class/drm`. Linux only

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

### <a name="general_wmiTimeout"></a>3.9. Property `JSON config > general > wmiTimeout`

**Title:** Set the timeout (ms) for WMI queries, `-1` for no timeout. Windows only

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `5000`    |

## <a name="display"></a>4. Property `JSON config > display`

**Title:** Configure how things to be displayed

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                       | Pattern | Type             | Deprecated | Definition | Title/Description                                                                                                    |
| ---------------------------------------------- | ------- | ---------------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------------------- |
| - [showErrors](#display_showErrors )           | No      | boolean          | No         | -          | Print occurring errors to the console. False to ignore errored modules                                               |
| - [disableLinewrap](#display_disableLinewrap ) | No      | boolean          | No         | -          | Whether to disable line wrap during the run                                                                          |
| - [hideCursor](#display_hideCursor )           | No      | boolean          | No         | -          | Whether to hide the cursor during the run                                                                            |
| - [separator](#display_separator )             | No      | string           | No         | -          | Set the separator between key and value                                                                              |
| - [color](#display_color )                     | No      | Combination      | No         | -          | Set the color of the keys and title                                                                                  |
| - [brightColor](#display_brightColor )         | No      | boolean          | No         | -          | Set if the keys, title and ASCII logo should be printed in bright color                                              |
| - [binaryPrefix](#display_binaryPrefix )       | No      | enum (of string) | No         | -          | Set the binary prefix to used when printing bytes                                                                    |
| - [sizeNdigits](#display_sizeNdigits )         | No      | integer          | No         | -          | Set the number of digits to keep after the decimal point when formatting sizes                                       |
| - [sizeMaxPrefix](#display_sizeMaxPrefix )     | No      | enum (of string) | No         | -          | Set the largest binary prefix to use when formatting sizes                                                           |
| - [temperatureUnit](#display_temperatureUnit ) | No      | enum (of string) | No         | -          | Set the unit of the temperature                                                                                      |
| - [percentType](#display_percentType )         | No      | number           | No         | -          | Set the percentage output type. 1 for percentage number, 2 for bar, 3 for both, 6 for bar only, 9 for colored number |
| - [noBuffer](#display_noBuffer )               | No      | boolean          | No         | -          | Whether to disable the stdout application buffer                                                                     |

### <a name="display_showErrors"></a>4.1. Property `JSON config > display > showErrors`

**Title:** Print occurring errors to the console. False to ignore errored modules

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

### <a name="display_disableLinewrap"></a>4.2. Property `JSON config > display > disableLinewrap`

**Title:** Whether to disable line wrap during the run

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

### <a name="display_hideCursor"></a>4.3. Property `JSON config > display > hideCursor`

**Title:** Whether to hide the cursor during the run

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

### <a name="display_separator"></a>4.4. Property `JSON config > display > separator`

**Title:** Set the separator between key and value

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `": "`   |

### <a name="display_color"></a>4.5. Property `JSON config > display > color`

**Title:** Set the color of the keys and title

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| One of(Option)                                                      |
| ------------------------------------------------------------------- |
| [Set the both color of the keys and title](#display_color_oneOf_i0) |
| [item 1](#display_color_oneOf_i1)                                   |

#### <a name="display_color_oneOf_i0"></a>4.5.1. Property `JSON config > display > color > oneOf > Set the both color of the keys and title`

**Title:** Set the both color of the keys and title

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

#### <a name="display_color_oneOf_i1"></a>4.5.2. Property `JSON config > display > color > oneOf > item 1`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                  | Pattern | Type             | Deprecated | Definition                               | Title/Description          |
| ----------------------------------------- | ------- | ---------------- | ---------- | ---------------------------------------- | -------------------------- |
| - [keys](#display_color_oneOf_i1_keys )   | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Set the color of the keys  |
| - [title](#display_color_oneOf_i1_title ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Set the color of the title |

##### <a name="display_color_oneOf_i1_keys"></a>4.5.2.1. Property `JSON config > display > color > oneOf > item 1 > keys`

**Title:** Set the color of the keys

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="display_color_oneOf_i1_title"></a>4.5.2.2. Property `JSON config > display > color > oneOf > item 1 > title`

**Title:** Set the color of the title

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

### <a name="display_brightColor"></a>4.6. Property `JSON config > display > brightColor`

**Title:** Set if the keys, title and ASCII logo should be printed in bright color

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

### <a name="display_binaryPrefix"></a>4.7. Property `JSON config > display > binaryPrefix`

**Title:** Set the binary prefix to used when printing bytes

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "iec"
* "si"
* "jedec"

### <a name="display_sizeNdigits"></a>4.8. Property `JSON config > display > sizeNdigits`

**Title:** Set the number of digits to keep after the decimal point when formatting sizes

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `2`       |

### <a name="display_sizeMaxPrefix"></a>4.9. Property `JSON config > display > sizeMaxPrefix`

**Title:** Set the largest binary prefix to use when formatting sizes

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"YB"`             |

Must be one of:
* "B"
* "kB"
* "MB"
* "GB"
* "TB"
* "PB"
* "EB"
* "ZB"
* "YB"

### <a name="display_temperatureUnit"></a>4.10. Property `JSON config > display > temperatureUnit`

**Title:** Set the unit of the temperature

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"C"`              |

Must be one of:
* "CELSIUS"
* "C"
* "FAHRENHEIT"
* "F"
* "KELVIN"
* "K"

### <a name="display_percentType"></a>4.11. Property `JSON config > display > percentType`

**Title:** Set the percentage output type. 1 for percentage number, 2 for bar, 3 for both, 6 for bar only, 9 for colored number

|              |          |
| ------------ | -------- |
| **Type**     | `number` |
| **Required** | No       |
| **Default**  | `1`      |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

### <a name="display_noBuffer"></a>4.12. Property `JSON config > display > noBuffer`

**Title:** Whether to disable the stdout application buffer

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

## <a name="library"></a>5. Property `JSON config > library`

**Title:** Set the path of a library to load

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                               | Pattern | Type   | Deprecated | Definition | Title/Description                                                                                                                                                                              |
| -------------------------------------- | ------- | ------ | ---------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - [pci](#library_pci )                 | No      | string | No         | -          | GPU output (Linux and FreeBSD)                                                                                                                                                                 |
| - [vulkan](#library_vulkan )           | No      | string | No         | -          | Vulkan module & fallback for GPU output                                                                                                                                                        |
| - [freetype](#library_freetype )       | No      | string | No         | -          | Used for Termux font detection (Android)                                                                                                                                                       |
| - [wayland](#library_wayland )         | No      | string | No         | -          | Better display performance and output in wayland sessions.<br />Supports different refresh rates per monitor.<br />(Linux)                                                                     |
| - [xcbRandr](#library_xcbRandr )       | No      | string | No         | -          | -                                                                                                                                                                                              |
| - [xcb](#library_xcb )                 | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [xrandr](#library_xrandr )           | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [x11](#library_x11 )                 | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [gio](#library_gio )                 | No      | string | No         | -          | Needed for values that are only stored GSettings (Linux, FreeBSD)                                                                                                                              |
| - [dconf](#library_dconf )             | No      | string | No         | -          | Needed for values that are only stored in DConf + Fallback for GSettings (Linux, FreeBSD)                                                                                                      |
| - [dbus](#library_dbus )               | No      | string | No         | -          | Bluetooth, Player & Media detection (Linux, FreeBSD)                                                                                                                                           |
| - [xfconf](#library_xfconf )           | No      | string | No         | -          | Needed for XFWM theme and XFCE Terminal font (Linux, FreeBSD)                                                                                                                                  |
| - [sqlite3](#library_sqlite3 )         | No      | string | No         | -          | Needed for pkg & rpm package count (Linux, FreeBSD)                                                                                                                                            |
| - [rpm](#library_rpm )                 | No      | string | No         | -          | Slower fallback for rpm package count. Needed on openSUSE & old CentOS / Redhat. (Linux)                                                                                                       |
| - [imagemagick](#library_imagemagick ) | No      | string | No         | -          | Images in terminal using sixel or kitty graphics protocol (Linux, FreeBSD, macOS)                                                                                                              |
| - [z](#library_z )                     | No      | string | No         | -          | Libz. Faster image output when using kitty graphics protocol (Linux, FreeBSD, macOS)                                                                                                           |
| - [chafa](#library_chafa )             | No      | string | No         | -          | Image output as ascii art (Linux, FreeBSD, macOS)                                                                                                                                              |
| - [egl](#library_egl )                 | No      | string | No         | -          | Needed by the OpenGL module for gl context creation (Linux, FreeBSD)                                                                                                                           |
| - [glx](#library_glx )                 | No      | string | No         | -          | Needed by the OpenGL module for gl context creation (Linux, FreeBSD)                                                                                                                           |
| - [osmesa](#library_osmesa )           | No      | string | No         | -          | Needed by the OpenGL module for gl context creation (Linux, FreeBSD)                                                                                                                           |
| - [opencl](#library_opencl )           | No      | string | No         | -          | OpenCL module (Linux, FreeBSD, Windows)                                                                                                                                                        |
| - [pulse](#library_pulse )             | No      | string | No         | -          | Pulseaudio. Used for Sound detection (Linux, FreeBSD)                                                                                                                                          |
| - [nm](#library_nm )                   | No      | string | No         | -          | NetworkManager. Used for Wifi detection (Linux)                                                                                                                                                |
| - [ddcutil](#library_ddcutil )         | No      | string | No         | -          | Used for brightness detection of external displays (Linux)                                                                                                                                     |

### <a name="library_pci"></a>5.1. Property `JSON config > library > pci`

**Title:** GPU output (Linux and FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_vulkan"></a>5.2. Property `JSON config > library > vulkan`

**Title:** Vulkan module & fallback for GPU output

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_freetype"></a>5.3. Property `JSON config > library > freetype`

**Title:** Used for Termux font detection (Android)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_wayland"></a>5.4. Property `JSON config > library > wayland`

**Title:** Better display performance and output in wayland sessions.
Supports different refresh rates per monitor.
(Linux)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_xcbRandr"></a>5.5. Property `JSON config > library > xcbRandr`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_xcb"></a>5.6. Property `JSON config > library > xcb`

**Title:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_xrandr"></a>5.7. Property `JSON config > library > xrandr`

**Title:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_x11"></a>5.8. Property `JSON config > library > x11`

**Title:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_gio"></a>5.9. Property `JSON config > library > gio`

**Title:** Needed for values that are only stored GSettings (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_dconf"></a>5.10. Property `JSON config > library > dconf`

**Title:** Needed for values that are only stored in DConf + Fallback for GSettings (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_dbus"></a>5.11. Property `JSON config > library > dbus`

**Title:** Bluetooth, Player & Media detection (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_xfconf"></a>5.12. Property `JSON config > library > xfconf`

**Title:** Needed for XFWM theme and XFCE Terminal font (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_sqlite3"></a>5.13. Property `JSON config > library > sqlite3`

**Title:** Needed for pkg & rpm package count (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_rpm"></a>5.14. Property `JSON config > library > rpm`

**Title:** Slower fallback for rpm package count. Needed on openSUSE & old CentOS / Redhat. (Linux)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_imagemagick"></a>5.15. Property `JSON config > library > imagemagick`

**Title:** Images in terminal using sixel or kitty graphics protocol (Linux, FreeBSD, macOS)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_z"></a>5.16. Property `JSON config > library > z`

**Title:** Libz. Faster image output when using kitty graphics protocol (Linux, FreeBSD, macOS)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_chafa"></a>5.17. Property `JSON config > library > chafa`

**Title:** Image output as ascii art (Linux, FreeBSD, macOS)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_egl"></a>5.18. Property `JSON config > library > egl`

**Title:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_glx"></a>5.19. Property `JSON config > library > glx`

**Title:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_osmesa"></a>5.20. Property `JSON config > library > osmesa`

**Title:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_opencl"></a>5.21. Property `JSON config > library > opencl`

**Title:** OpenCL module (Linux, FreeBSD, Windows)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_pulse"></a>5.22. Property `JSON config > library > pulse`

**Title:** Pulseaudio. Used for Sound detection (Linux, FreeBSD)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_nm"></a>5.23. Property `JSON config > library > nm`

**Title:** NetworkManager. Used for Wifi detection (Linux)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

### <a name="library_ddcutil"></a>5.24. Property `JSON config > library > ddcutil`

**Title:** Used for brightness detection of external displays (Linux)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

## <a name="modules"></a>6. Property `JSON config > modules`

**Title:** Fastfetch modules to run

|              |         |
| ------------ | ------- |
| **Type**     | `array` |
| **Required** | No      |

|                      | Array restrictions |
| -------------------- | ------------------ |
| **Min items**        | N/A                |
| **Max items**        | N/A                |
| **Items unicity**    | False              |
| **Additional items** | False              |
| **Tuple validation** | See below          |

| Each item of this array must be | Description |
| ------------------------------- | ----------- |
| [modules items](#modules_items) | -           |

### <a name="autogenerated_heading_2"></a>6.1. JSON config > modules > modules items

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| Any of(Option)                                                    |
| ----------------------------------------------------------------- |
| [Run module with default configurations](#modules_items_anyOf_i0) |
| [Run module with custom configurations](#modules_items_anyOf_i1)  |

#### <a name="modules_items_anyOf_i0"></a>6.1.1. Property `JSON config > modules > modules items > anyOf > Run module with default configurations`

**Title:** Run module with default configurations

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "battery"
* "bios"
* "bluetooth"
* "board"
* "break"
* "brightness"
* "chassis"
* "cpu"
* "cpuusage"
* "command"
* "colors"
* "cursor"
* "datetime"
* "display"
* "disk"
* "de"
* "font"
* "gamepad"
* "gpu"
* "host"
* "icons"
* "kernel"
* "lm"
* "locale"
* "localip"
* "media"
* "memory"
* "opencl"
* "opengl"
* "os"
* "packages"
* "player"
* "poweradapter"
* "processes"
* "publicip"
* "separator"
* "shell"
* "sound"
* "swap"
* "terminal"
* "terminalfont"
* "terminalsize"
* "title"
* "theme"
* "uptime"
* "users"
* "vulkan"
* "wallpaper"
* "weather"
* "wm"
* "wifi"
* "wmtheme"

#### <a name="modules_items_anyOf_i1"></a>6.1.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations`

**Title:** Run module with custom configurations

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| Property                                | Pattern | Type   | Deprecated | Definition | Title/Description |
| --------------------------------------- | ------- | ------ | ---------- | ---------- | ----------------- |
| + [type](#modules_items_anyOf_i1_type ) | No      | string | No         | -          | Module type       |

| One of(Option)                                               |
| ------------------------------------------------------------ |
| [Break](#modules_items_anyOf_i1_oneOf_i0)                    |
| [No additional properties](#modules_items_anyOf_i1_oneOf_i1) |
| [Battery](#modules_items_anyOf_i1_oneOf_i2)                  |
| [Bluetooth](#modules_items_anyOf_i1_oneOf_i3)                |
| [CPU](#modules_items_anyOf_i1_oneOf_i4)                      |
| [Colors](#modules_items_anyOf_i1_oneOf_i5)                   |
| [Command](#modules_items_anyOf_i1_oneOf_i6)                  |
| [Custom](#modules_items_anyOf_i1_oneOf_i7)                   |
| [Display](#modules_items_anyOf_i1_oneOf_i8)                  |
| [Disk](#modules_items_anyOf_i1_oneOf_i9)                     |
| [GPU](#modules_items_anyOf_i1_oneOf_i10)                     |
| [Local IP](#modules_items_anyOf_i1_oneOf_i11)                |
| [OpenGL](#modules_items_anyOf_i1_oneOf_i12)                  |
| [Public IP](#modules_items_anyOf_i1_oneOf_i13)               |
| [Separator](#modules_items_anyOf_i1_oneOf_i14)               |
| [Sound](#modules_items_anyOf_i1_oneOf_i15)                   |
| [Title](#modules_items_anyOf_i1_oneOf_i16)                   |
| [Weather](#modules_items_anyOf_i1_oneOf_i17)                 |

##### <a name="modules_items_anyOf_i1_oneOf_i0"></a>6.1.2.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Break`

**Title:** Break

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Print a empty line

| Property                                         | Pattern | Type  | Deprecated | Definition | Title/Description |
| ------------------------------------------------ | ------- | ----- | ---------- | ---------- | ----------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i0_type ) | No      | const | No         | -          | -                 |

##### <a name="modules_items_anyOf_i1_oneOf_i0_type"></a>6.1.2.1.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Break > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"break"`

##### <a name="modules_items_anyOf_i1_oneOf_i1"></a>6.1.2.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties`

**Title:** No additional properties

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                 | Pattern | Type             | Deprecated | Definition          | Title/Description |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | ------------------- | ----------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i1_type )         | No      | enum (of string) | No         | -                   | -                 |
| - [key](#modules_items_anyOf_i1_oneOf_i1_key )           | No      | string           | No         | In #/$defs/key      | -                 |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | No      | enum (of string) | No         | In #/$defs/keyColor | -                 |
| - [format](#modules_items_anyOf_i1_oneOf_i1_format )     | No      | string           | No         | In #/$defs/format   | -                 |

##### <a name="modules_items_anyOf_i1_oneOf_i1_type"></a>6.1.2.2.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > type`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "bios"
* "board"
* "brightness"
* "chassis"
* "cpuusage"
* "cursor"
* "datetime"
* "de"
* "font"
* "gamepad"
* "host"
* "icons"
* "kernel"
* "lm"
* "locale"
* "media"
* "memory"
* "opencl"
* "os"
* "packages"
* "player"
* "poweradapter"
* "processes"
* "shell"
* "swap"
* "terminal"
* "terminalfont"
* "terminalsize"
* "theme"
* "uptime"
* "users"
* "vulkan"
* "wallpaper"
* "wm"
* "wifi"
* "wmtheme"

##### <a name="modules_items_anyOf_i1_oneOf_i1_key"></a>6.1.2.2.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > key`

|                |             |
| -------------- | ----------- |
| **Type**       | `string`    |
| **Required**   | No          |
| **Defined in** | #/$defs/key |

##### <a name="modules_items_anyOf_i1_oneOf_i1_keyColor"></a>6.1.2.2.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > keyColor`

|                |                    |
| -------------- | ------------------ |
| **Type**       | `enum (of string)` |
| **Required**   | No                 |
| **Defined in** | #/$defs/keyColor   |

##### <a name="modules_items_anyOf_i1_oneOf_i1_format"></a>6.1.2.2.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > No additional properties > format`

|                |                |
| -------------- | -------------- |
| **Type**       | `string`       |
| **Required**   | No             |
| **Defined in** | #/$defs/format |

##### <a name="modules_items_anyOf_i1_oneOf_i2"></a>6.1.2.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery`

**Title:** Battery

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                 | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                                               |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i2_type )         | No      | const            | No         | -                                                              | -                                                                                               |
| - [dir](#modules_items_anyOf_i1_oneOf_i2_dir )           | No      | string           | No         | -                                                              | The directory where the battery folders are. Standard: \`/sys/class/power_supply/\`. Linux only |
| - [temp](#modules_items_anyOf_i1_oneOf_i2_temp )         | No      | boolean          | No         | -                                                              | Detect and display Battery temperature if supported                                             |
| - [key](#modules_items_anyOf_i1_oneOf_i2_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                                               |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i2_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                                               |
| - [format](#modules_items_anyOf_i1_oneOf_i2_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                                               |

##### <a name="modules_items_anyOf_i1_oneOf_i2_type"></a>6.1.2.3.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"battery"`

##### <a name="modules_items_anyOf_i1_oneOf_i2_dir"></a>6.1.2.3.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > dir`

**Title:** The directory where the battery folders are. Standard: `/sys/class/power_supply/`. Linux only

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i2_temp"></a>6.1.2.3.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > temp`

**Title:** Detect and display Battery temperature if supported

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i2_key"></a>6.1.2.3.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i2_keyColor"></a>6.1.2.3.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i2_format"></a>6.1.2.3.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Battery > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i3"></a>6.1.2.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth`

**Title:** Bluetooth

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                 | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                       |
| ------------------------------------------------------------------------ | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i3_type )                         | No      | const            | No         | -                                                              | -                                                       |
| - [showDisconnected](#modules_items_anyOf_i1_oneOf_i3_showDisconnected ) | No      | boolean          | No         | -                                                              | Set if disconnected bluetooth devices should be printed |
| - [key](#modules_items_anyOf_i1_oneOf_i3_key )                           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                       |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i3_keyColor )                 | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                       |
| - [format](#modules_items_anyOf_i1_oneOf_i3_format )                     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                       |

##### <a name="modules_items_anyOf_i1_oneOf_i3_type"></a>6.1.2.4.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"bluetooth"`

##### <a name="modules_items_anyOf_i1_oneOf_i3_showDisconnected"></a>6.1.2.4.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > showDisconnected`

**Title:** Set if disconnected bluetooth devices should be printed

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i3_key"></a>6.1.2.4.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i3_keyColor"></a>6.1.2.4.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i3_format"></a>6.1.2.4.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Bluetooth > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i4"></a>6.1.2.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU`

**Title:** CPU

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                 | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                               |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ----------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i4_type )         | No      | const            | No         | -                                                              | -                                               |
| - [temp](#modules_items_anyOf_i1_oneOf_i4_temp )         | No      | boolean          | No         | -                                                              | Detect and display CPU temperature if supported |
| - [key](#modules_items_anyOf_i1_oneOf_i4_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                               |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i4_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                               |
| - [format](#modules_items_anyOf_i1_oneOf_i4_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                               |

##### <a name="modules_items_anyOf_i1_oneOf_i4_type"></a>6.1.2.5.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"cpu"`

##### <a name="modules_items_anyOf_i1_oneOf_i4_temp"></a>6.1.2.5.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > temp`

**Title:** Detect and display CPU temperature if supported

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i4_key"></a>6.1.2.5.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i4_keyColor"></a>6.1.2.5.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i4_format"></a>6.1.2.5.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > CPU > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i5"></a>6.1.2.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors`

**Title:** Colors

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `object`                                                                  |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition | Title/Description                                         |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | ---------- | --------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i5_type )               | No      | const            | No         | -          | -                                                         |
| - [symbol](#modules_items_anyOf_i1_oneOf_i5_symbol )           | No      | enum (of string) | No         | -          | Set the symbol to use                                     |
| - [paddingLeft](#modules_items_anyOf_i1_oneOf_i5_paddingLeft ) | No      | integer          | No         | -          | Set the number of white spaces to print before the symbol |

##### <a name="modules_items_anyOf_i1_oneOf_i5_type"></a>6.1.2.6.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"colors"`

##### <a name="modules_items_anyOf_i1_oneOf_i5_symbol"></a>6.1.2.6.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > symbol`

**Title:** Set the symbol to use

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"block"`          |

Must be one of:
* "block"
* "circle"
* "diamond"
* "triangle"
* "square"
* "star"

##### <a name="modules_items_anyOf_i1_oneOf_i5_paddingLeft"></a>6.1.2.6.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Colors > paddingLeft`

**Title:** Set the number of white spaces to print before the symbol

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `0`       |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="modules_items_anyOf_i1_oneOf_i6"></a>6.1.2.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command`

**Title:** Command

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                 | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                                                 |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i6_type )         | No      | const            | No         | -                                                              | -                                                                                                 |
| - [shell](#modules_items_anyOf_i1_oneOf_i6_shell )       | No      | string           | No         | -                                                              | Set the shell program to execute the command text<br />Default: cmd for Windows, /bin/sh for *nix |
| - [text](#modules_items_anyOf_i1_oneOf_i6_text )         | No      | string           | No         | -                                                              | Set the command text to be executed                                                               |
| - [key](#modules_items_anyOf_i1_oneOf_i6_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                                                 |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i6_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                                                 |
| - [format](#modules_items_anyOf_i1_oneOf_i6_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                                                 |

##### <a name="modules_items_anyOf_i1_oneOf_i6_type"></a>6.1.2.7.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"command"`

##### <a name="modules_items_anyOf_i1_oneOf_i6_shell"></a>6.1.2.7.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > shell`

**Title:** Set the shell program to execute the command text
Default: cmd for Windows, /bin/sh for *nix

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i6_text"></a>6.1.2.7.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > text`

**Title:** Set the command text to be executed

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i6_key"></a>6.1.2.7.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i6_keyColor"></a>6.1.2.7.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i6_format"></a>6.1.2.7.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Command > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i7"></a>6.1.2.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom`

**Title:** Custom

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Print a custom string, with or without key

| Property                                                 | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | -------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i7_type )         | No      | const            | No         | -                                                              | -                                |
| - [key](#modules_items_anyOf_i1_oneOf_i7_key )           | No      | string           | No         | -                                                              | Leave empty not to print the key |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i7_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                |
| + [format](#modules_items_anyOf_i1_oneOf_i7_format )     | No      | string           | No         | -                                                              | Text to print                    |

##### <a name="modules_items_anyOf_i1_oneOf_i7_type"></a>6.1.2.8.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"custom"`

##### <a name="modules_items_anyOf_i1_oneOf_i7_key"></a>6.1.2.8.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > key`

**Title:** Leave empty not to print the key

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i7_keyColor"></a>6.1.2.8.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i7_format"></a>6.1.2.8.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Custom > format`

**Title:** Text to print

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | Yes      |

##### <a name="modules_items_anyOf_i1_oneOf_i8"></a>6.1.2.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display`

**Title:** Display

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                     | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                              |
| ---------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i8_type )                             | No      | const            | No         | -                                                              | -                                                                              |
| - [compactType](#modules_items_anyOf_i1_oneOf_i8_compactType )               | No      | enum (of string) | No         | -                                                              | Set if all displays should be printed in one line                              |
| - [preciseRefreshRate](#modules_items_anyOf_i1_oneOf_i8_preciseRefreshRate ) | No      | boolean          | No         | -                                                              | Set if decimal refresh rates should not be rounded into integers when printing |
| - [key](#modules_items_anyOf_i1_oneOf_i8_key )                               | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                              |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i8_keyColor )                     | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                              |
| - [format](#modules_items_anyOf_i1_oneOf_i8_format )                         | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                              |

##### <a name="modules_items_anyOf_i1_oneOf_i8_type"></a>6.1.2.9.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"display"`

##### <a name="modules_items_anyOf_i1_oneOf_i8_compactType"></a>6.1.2.9.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > compactType`

**Title:** Set if all displays should be printed in one line

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"none"`           |

Must be one of:
* "none"
* "original"
* "scaled"

##### <a name="modules_items_anyOf_i1_oneOf_i8_preciseRefreshRate"></a>6.1.2.9.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > preciseRefreshRate`

**Title:** Set if decimal refresh rates should not be rounded into integers when printing

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

##### <a name="modules_items_anyOf_i1_oneOf_i8_key"></a>6.1.2.9.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i8_keyColor"></a>6.1.2.9.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i8_format"></a>6.1.2.9.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Display > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i9"></a>6.1.2.10. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk`

**Title:** Disk

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                             | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                                                                                      |
| -------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i9_type )                     | No      | const            | No         | -                                                              | -                                                                                                                                      |
| - [folders](#modules_items_anyOf_i1_oneOf_i9_folders )               | No      | string           | No         | -                                                              | A colon (semicolon on Windows) separated list of folder paths for the disk output<br />Default: "/:/home" ("C:\\;D:\\ ..." on Windows) |
| - [showExternal](#modules_items_anyOf_i1_oneOf_i9_showExternal )     | No      | boolean          | No         | -                                                              | Set if external volume should be printed                                                                                               |
| - [showHidden](#modules_items_anyOf_i1_oneOf_i9_showHidden )         | No      | boolean          | No         | -                                                              | Set if hidden volumes should be printed                                                                                                |
| - [showSubvolumes](#modules_items_anyOf_i1_oneOf_i9_showSubvolumes ) | No      | boolean          | No         | -                                                              | Set if subvolumes should be printed                                                                                                    |
| - [showUnknown](#modules_items_anyOf_i1_oneOf_i9_showUnknown )       | No      | boolean          | No         | -                                                              | Set if unknown (unable to detect sizes) volumes should be printed                                                                      |
| - [key](#modules_items_anyOf_i1_oneOf_i9_key )                       | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                                                                                      |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i9_keyColor )             | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                                                                                      |
| - [format](#modules_items_anyOf_i1_oneOf_i9_format )                 | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                                                                                      |

##### <a name="modules_items_anyOf_i1_oneOf_i9_type"></a>6.1.2.10.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"disk"`

##### <a name="modules_items_anyOf_i1_oneOf_i9_folders"></a>6.1.2.10.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > folders`

**Title:** A colon (semicolon on Windows) separated list of folder paths for the disk output
Default: "/:/home" ("C:\\;D:\\ ..." on Windows)

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i9_showExternal"></a>6.1.2.10.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showExternal`

**Title:** Set if external volume should be printed

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

##### <a name="modules_items_anyOf_i1_oneOf_i9_showHidden"></a>6.1.2.10.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showHidden`

**Title:** Set if hidden volumes should be printed

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i9_showSubvolumes"></a>6.1.2.10.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showSubvolumes`

**Title:** Set if subvolumes should be printed

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i9_showUnknown"></a>6.1.2.10.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > showUnknown`

**Title:** Set if unknown (unable to detect sizes) volumes should be printed

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i9_key"></a>6.1.2.10.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i9_keyColor"></a>6.1.2.10.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i9_format"></a>6.1.2.10.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Disk > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i10"></a>6.1.2.11. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU`

**Title:** GPU

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                                                              |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i10_type )               | No      | const            | No         | -                                                              | -                                                                                                              |
| - [temp](#modules_items_anyOf_i1_oneOf_i10_temp )               | No      | boolean          | No         | -                                                              | Detect and display GPU temperature if supported                                                                |
| - [forceVulkan](#modules_items_anyOf_i1_oneOf_i10_forceVulkan ) | No      | boolean          | No         | -                                                              | Force using vulkan to detect GPUs, which support video memory usage detection with \`--allow-slow-operations\` |
| - [hideType](#modules_items_anyOf_i1_oneOf_i10_hideType )       | No      | enum (of string) | No         | -                                                              | Specify the type of GPUs should not be printed                                                                 |
| - [key](#modules_items_anyOf_i1_oneOf_i10_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                                                              |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i10_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                                                              |
| - [format](#modules_items_anyOf_i1_oneOf_i10_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                                                              |

##### <a name="modules_items_anyOf_i1_oneOf_i10_type"></a>6.1.2.11.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"gpu"`

##### <a name="modules_items_anyOf_i1_oneOf_i10_temp"></a>6.1.2.11.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > temp`

**Title:** Detect and display GPU temperature if supported

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i10_forceVulkan"></a>6.1.2.11.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > forceVulkan`

**Title:** Force using vulkan to detect GPUs, which support video memory usage detection with `--allow-slow-operations`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i10_hideType"></a>6.1.2.11.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > hideType`

**Title:** Specify the type of GPUs should not be printed

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"none"`           |

Must be one of:
* "integrated"
* "discrete"
* "none"

##### <a name="modules_items_anyOf_i1_oneOf_i10_key"></a>6.1.2.11.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i10_keyColor"></a>6.1.2.11.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i10_format"></a>6.1.2.11.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > GPU > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i11"></a>6.1.2.12. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP`

**Title:** Local IP

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                          | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                               |
| --------------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ----------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i11_type )                                 | No      | const            | No         | -                                                              | -                                               |
| - [showIpv4](#modules_items_anyOf_i1_oneOf_i11_showIpv4 )                         | No      | boolean          | No         | -                                                              | Show IPv4 addresses                             |
| - [showIpv6](#modules_items_anyOf_i1_oneOf_i11_showIpv6 )                         | No      | boolean          | No         | -                                                              | Show IPv6 addresses                             |
| - [showMac](#modules_items_anyOf_i1_oneOf_i11_showMac )                           | No      | boolean          | No         | -                                                              | Show MAC addresses                              |
| - [showLoop](#modules_items_anyOf_i1_oneOf_i11_showLoop )                         | No      | boolean          | No         | -                                                              | Show loop back addresses (127.0.0.1)            |
| - [compact](#modules_items_anyOf_i1_oneOf_i11_compact )                           | No      | boolean          | No         | -                                                              | Show all IPs in one line                        |
| - [namePrefix](#modules_items_anyOf_i1_oneOf_i11_namePrefix )                     | No      | string           | No         | -                                                              | Show IPs with given name prefix only            |
| - [showDefaultRouteOnly](#modules_items_anyOf_i1_oneOf_i11_showDefaultRouteOnly ) | No      | boolean          | No         | -                                                              | Show ips that are used for default routing only |
| - [key](#modules_items_anyOf_i1_oneOf_i11_key )                                   | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                               |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i11_keyColor )                         | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                               |
| - [format](#modules_items_anyOf_i1_oneOf_i11_format )                             | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                               |

##### <a name="modules_items_anyOf_i1_oneOf_i11_type"></a>6.1.2.12.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"localip"`

##### <a name="modules_items_anyOf_i1_oneOf_i11_showIpv4"></a>6.1.2.12.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showIpv4`

**Title:** Show IPv4 addresses

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

##### <a name="modules_items_anyOf_i1_oneOf_i11_showIpv6"></a>6.1.2.12.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showIpv6`

**Title:** Show IPv6 addresses

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i11_showMac"></a>6.1.2.12.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showMac`

**Title:** Show MAC addresses

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i11_showLoop"></a>6.1.2.12.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showLoop`

**Title:** Show loop back addresses (127.0.0.1)

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i11_compact"></a>6.1.2.12.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > compact`

**Title:** Show all IPs in one line

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i11_namePrefix"></a>6.1.2.12.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > namePrefix`

**Title:** Show IPs with given name prefix only

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i11_showDefaultRouteOnly"></a>6.1.2.12.8. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > showDefaultRouteOnly`

**Title:** Show ips that are used for default routing only

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i11_key"></a>6.1.2.12.9. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i11_keyColor"></a>6.1.2.12.10. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i11_format"></a>6.1.2.12.11. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Local IP > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i12"></a>6.1.2.13. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL`

**Title:** OpenGL

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                  | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                          |
| --------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ---------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i12_type )         | No      | const            | No         | -                                                              | -                                                          |
| - [library](#modules_items_anyOf_i1_oneOf_i12_library )   | No      | enum (of string) | No         | -                                                              | Set the OpenGL context creation library to use. Linux only |
| - [key](#modules_items_anyOf_i1_oneOf_i12_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                          |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i12_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                          |
| - [format](#modules_items_anyOf_i1_oneOf_i12_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                          |

##### <a name="modules_items_anyOf_i1_oneOf_i12_type"></a>6.1.2.13.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"opengl"`

##### <a name="modules_items_anyOf_i1_oneOf_i12_library"></a>6.1.2.13.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > library`

**Title:** Set the OpenGL context creation library to use. Linux only

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"auto"`           |

Must be one of:
* "auto"
* "egl"
* "glx"
* "osmesa"

##### <a name="modules_items_anyOf_i1_oneOf_i12_key"></a>6.1.2.13.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i12_keyColor"></a>6.1.2.13.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i12_format"></a>6.1.2.13.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > OpenGL > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i13"></a>6.1.2.14. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP`

**Title:** Public IP

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                  | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                                                 |
| --------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i13_type )         | No      | const            | No         | -                                                              | -                                                                                 |
| - [url](#modules_items_anyOf_i1_oneOf_i13_url )           | No      | string           | No         | -                                                              | The URL of public IP detection server to be used. Only HTTP protocol is supported |
| - [timeout](#modules_items_anyOf_i1_oneOf_i13_timeout )   | No      | integer          | No         | -                                                              | Time in milliseconds to wait for the public ip server to respond                  |
| - [key](#modules_items_anyOf_i1_oneOf_i13_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                                                 |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i13_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                                                 |
| - [format](#modules_items_anyOf_i1_oneOf_i13_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                                                 |

##### <a name="modules_items_anyOf_i1_oneOf_i13_type"></a>6.1.2.14.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"publicip"`

##### <a name="modules_items_anyOf_i1_oneOf_i13_url"></a>6.1.2.14.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > url`

**Title:** The URL of public IP detection server to be used. Only HTTP protocol is supported

|              |                         |
| ------------ | ----------------------- |
| **Type**     | `string`                |
| **Required** | No                      |
| **Format**   | `url`                   |
| **Default**  | `"http://ipinfo.io/ip"` |

##### <a name="modules_items_anyOf_i1_oneOf_i13_timeout"></a>6.1.2.14.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > timeout`

**Title:** Time in milliseconds to wait for the public ip server to respond

|              |                  |
| ------------ | ---------------- |
| **Type**     | `integer`        |
| **Required** | No               |
| **Default**  | `"disabled (0)"` |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="modules_items_anyOf_i1_oneOf_i13_key"></a>6.1.2.14.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i13_keyColor"></a>6.1.2.14.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i13_format"></a>6.1.2.14.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Public IP > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i14"></a>6.1.2.15. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator`

**Title:** Separator

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                              | Pattern | Type   | Deprecated | Definition | Title/Description            |
| ----------------------------------------------------- | ------- | ------ | ---------- | ---------- | ---------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i14_type )     | No      | const  | No         | -          | -                            |
| - [string](#modules_items_anyOf_i1_oneOf_i14_string ) | No      | string | No         | -          | Set the string to be printed |

##### <a name="modules_items_anyOf_i1_oneOf_i14_type"></a>6.1.2.15.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"separator"`

##### <a name="modules_items_anyOf_i1_oneOf_i14_string"></a>6.1.2.15.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Separator > string`

**Title:** Set the string to be printed

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `"-"`    |

##### <a name="modules_items_anyOf_i1_oneOf_i15"></a>6.1.2.16. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound`

**Title:** Sound

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                    | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                |
| ----------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i15_type )           | No      | const            | No         | -                                                              | -                                                |
| - [soundType](#modules_items_anyOf_i1_oneOf_i15_soundType ) | No      | enum (of string) | No         | -                                                              | Set what type of sound devices should be printed |
| - [key](#modules_items_anyOf_i1_oneOf_i15_key )             | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i15_keyColor )   | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                |
| - [format](#modules_items_anyOf_i1_oneOf_i15_format )       | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                |

##### <a name="modules_items_anyOf_i1_oneOf_i15_type"></a>6.1.2.16.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"sound"`

##### <a name="modules_items_anyOf_i1_oneOf_i15_soundType"></a>6.1.2.16.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > soundType`

**Title:** Set what type of sound devices should be printed

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"main"`           |

Must be one of:
* "main"
* "active"
* "all"

##### <a name="modules_items_anyOf_i1_oneOf_i15_key"></a>6.1.2.16.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i15_keyColor"></a>6.1.2.16.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i15_format"></a>6.1.2.16.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Sound > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i16"></a>6.1.2.17. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title`

**Title:** Title

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                  | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                       |
| --------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | ------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i16_type )         | No      | const            | No         | -                                                              | -                                                       |
| - [fqdn](#modules_items_anyOf_i1_oneOf_i16_fqdn )         | No      | boolean          | No         | -                                                              | Set if the title should use fully qualified domain name |
| - [color](#modules_items_anyOf_i1_oneOf_i16_color )       | No      | object           | No         | -                                                              | Set colors of the different part of title               |
| - [key](#modules_items_anyOf_i1_oneOf_i16_key )           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                       |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i16_keyColor ) | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                       |
| - [format](#modules_items_anyOf_i1_oneOf_i16_format )     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                       |

##### <a name="modules_items_anyOf_i1_oneOf_i16_type"></a>6.1.2.17.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"title"`

##### <a name="modules_items_anyOf_i1_oneOf_i16_fqdn"></a>6.1.2.17.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > fqdn`

**Title:** Set if the title should use fully qualified domain name

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

##### <a name="modules_items_anyOf_i1_oneOf_i16_color"></a>6.1.2.17.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color`

**Title:** Set colors of the different part of title

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                | Pattern | Type             | Deprecated | Definition                               | Title/Description                       |
| ------------------------------------------------------- | ------- | ---------------- | ---------- | ---------------------------------------- | --------------------------------------- |
| - [user](#modules_items_anyOf_i1_oneOf_i16_color_user ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Set color of the user name (left part)  |
| - [at](#modules_items_anyOf_i1_oneOf_i16_color_at )     | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Set color of the @ symbol (middle part) |
| - [host](#modules_items_anyOf_i1_oneOf_i16_color_host ) | No      | enum (of string) | No         | Same as [defs_keyColor](#defs_keyColor ) | Set color of the host name (right part) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_color_user"></a>6.1.2.17.3.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > user`

**Title:** Set color of the user name (left part)

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_color_at"></a>6.1.2.17.3.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > at`

**Title:** Set color of the @ symbol (middle part)

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_color_host"></a>6.1.2.17.3.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > color > host`

**Title:** Set color of the host name (right part)

|                        |                                 |
| ---------------------- | ------------------------------- |
| **Type**               | `enum (of string)`              |
| **Required**           | No                              |
| **Same definition as** | [defs_keyColor](#defs_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_key"></a>6.1.2.17.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_keyColor"></a>6.1.2.17.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i16_format"></a>6.1.2.17.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Title > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_oneOf_i17"></a>6.1.2.18. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather`

**Title:** Weather

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                          | Pattern | Type             | Deprecated | Definition                                                     | Title/Description                                              |
| ----------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------- | -------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i17_type )                 | No      | const            | No         | -                                                              | -                                                              |
| - [location](#modules_items_anyOf_i1_oneOf_i17_location )         | No      | string           | No         | -                                                              | The location to display                                        |
| - [timeout](#modules_items_anyOf_i1_oneOf_i17_timeout )           | No      | integer          | No         | -                                                              | Time in milliseconds to wait for the weather server to respond |
| - [outputFormat](#modules_items_anyOf_i1_oneOf_i17_outputFormat ) | No      | string           | No         | -                                                              | The output weather format to be used (must be URI encoded)     |
| - [key](#modules_items_anyOf_i1_oneOf_i17_key )                   | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )           | -                                                              |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i17_keyColor )         | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor ) | -                                                              |
| - [format](#modules_items_anyOf_i1_oneOf_i17_format )             | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )     | -                                                              |

##### <a name="modules_items_anyOf_i1_oneOf_i17_type"></a>6.1.2.18.1. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

Specific value: `"weather"`

##### <a name="modules_items_anyOf_i1_oneOf_i17_location"></a>6.1.2.18.2. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > location`

**Title:** The location to display

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

##### <a name="modules_items_anyOf_i1_oneOf_i17_timeout"></a>6.1.2.18.3. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > timeout`

**Title:** Time in milliseconds to wait for the weather server to respond

|              |                  |
| ------------ | ---------------- |
| **Type**     | `integer`        |
| **Required** | No               |
| **Default**  | `"disabled (0)"` |

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="modules_items_anyOf_i1_oneOf_i17_outputFormat"></a>6.1.2.18.4. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > outputFormat`

**Title:** The output weather format to be used (must be URI encoded)

|              |                  |
| ------------ | ---------------- |
| **Type**     | `string`         |
| **Required** | No               |
| **Default**  | `"%t+-+%C+(%l)"` |

##### <a name="modules_items_anyOf_i1_oneOf_i17_key"></a>6.1.2.18.5. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

##### <a name="modules_items_anyOf_i1_oneOf_i17_keyColor"></a>6.1.2.18.6. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

##### <a name="modules_items_anyOf_i1_oneOf_i17_format"></a>6.1.2.18.7. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > oneOf > Weather > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

##### <a name="modules_items_anyOf_i1_type"></a>6.1.2.19. Property `JSON config > modules > modules items > anyOf > Run module with custom configurations > type`

**Title:** Module type

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | Yes      |

----------------------------------------------------------------------------------------------------------------------------
Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) on 2023-08-07 at 13:32:21 +0800
