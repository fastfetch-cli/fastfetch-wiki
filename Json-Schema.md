# JSON config

- [1. Property `JSON config > $schema`](#schema)
- [2. Property `JSON config > logo`](#logo)
  - [2.1. Property `JSON config > logo > oneOf > item 0`](#logo_oneOf_i0)
  - [2.2. Property `JSON config > logo > oneOf > item 1`](#logo_oneOf_i1)
  - [2.3. Property `JSON config > logo > oneOf > item 2`](#logo_oneOf_i2)
    - [2.3.1. Property `JSON config > logo > oneOf > item 2 > type`](#logo_oneOf_i2_type)
    - [2.3.2. Property `JSON config > logo > oneOf > item 2 > source`](#logo_oneOf_i2_source)
    - [2.3.3. Property `JSON config > logo > oneOf > item 2 > color`](#logo_oneOf_i2_color)
      - [2.3.3.1. Property `JSON config > logo > oneOf > item 2 > color > 1`](#logo_oneOf_i2_color_1)
      - [2.3.3.2. Property `JSON config > logo > oneOf > item 2 > color > 2`](#logo_oneOf_i2_color_2)
      - [2.3.3.3. Property `JSON config > logo > oneOf > item 2 > color > 3`](#logo_oneOf_i2_color_3)
      - [2.3.3.4. Property `JSON config > logo > oneOf > item 2 > color > 4`](#logo_oneOf_i2_color_4)
      - [2.3.3.5. Property `JSON config > logo > oneOf > item 2 > color > 5`](#logo_oneOf_i2_color_5)
      - [2.3.3.6. Property `JSON config > logo > oneOf > item 2 > color > 6`](#logo_oneOf_i2_color_6)
      - [2.3.3.7. Property `JSON config > logo > oneOf > item 2 > color > 7`](#logo_oneOf_i2_color_7)
      - [2.3.3.8. Property `JSON config > logo > oneOf > item 2 > color > 8`](#logo_oneOf_i2_color_8)
      - [2.3.3.9. Property `JSON config > logo > oneOf > item 2 > color > 9`](#logo_oneOf_i2_color_9)
    - [2.3.4. Property `JSON config > logo > oneOf > item 2 > width`](#logo_oneOf_i2_width)
    - [2.3.5. Property `JSON config > logo > oneOf > item 2 > height`](#logo_oneOf_i2_height)
    - [2.3.6. Property `JSON config > logo > oneOf > item 2 > padding`](#logo_oneOf_i2_padding)
      - [2.3.6.1. Property `JSON config > logo > oneOf > item 2 > padding > top`](#logo_oneOf_i2_padding_top)
      - [2.3.6.2. Property `JSON config > logo > oneOf > item 2 > padding > left`](#logo_oneOf_i2_padding_left)
      - [2.3.6.3. Property `JSON config > logo > oneOf > item 2 > padding > right`](#logo_oneOf_i2_padding_right)
    - [2.3.7. Property `JSON config > logo > oneOf > item 2 > printRemaining`](#logo_oneOf_i2_printRemaining)
    - [2.3.8. Property `JSON config > logo > oneOf > item 2 > preserveAspectRatio`](#logo_oneOf_i2_preserveAspectRatio)
    - [2.3.9. Property `JSON config > logo > oneOf > item 2 > recache`](#logo_oneOf_i2_recache)
    - [2.3.10. Property `JSON config > logo > oneOf > item 2 > separate`](#logo_oneOf_i2_separate)
    - [2.3.11. Property `JSON config > logo > oneOf > item 2 > chafa`](#logo_oneOf_i2_chafa)
      - [2.3.11.1. Property `JSON config > logo > oneOf > item 2 > chafa > fgOnly`](#logo_oneOf_i2_chafa_fgOnly)
      - [2.3.11.2. Property `JSON config > logo > oneOf > item 2 > chafa > symbols`](#logo_oneOf_i2_chafa_symbols)
      - [2.3.11.3. Property `JSON config > logo > oneOf > item 2 > chafa > canvasMode`](#logo_oneOf_i2_chafa_canvasMode)
      - [2.3.11.4. Property `JSON config > logo > oneOf > item 2 > chafa > colorSpace`](#logo_oneOf_i2_chafa_colorSpace)
      - [2.3.11.5. Property `JSON config > logo > oneOf > item 2 > chafa > ditherMode`](#logo_oneOf_i2_chafa_ditherMode)
- [3. Property `JSON config > general`](#general)
  - [3.1. Property `JSON config > general > multithreading`](#general_multithreading)
  - [3.2. Property `JSON config > general > thread`](#general_thread)
  - [3.3. Property `JSON config > general > escapeBedrock`](#general_escapeBedrock)
  - [3.4. Property `JSON config > general > playerName`](#general_playerName)
  - [3.5. Property `JSON config > general > dsForceDrm`](#general_dsForceDrm)
    - [3.5.1. Property `JSON config > general > dsForceDrm > oneOf > item 0`](#general_dsForceDrm_oneOf_i0)
    - [3.5.2. Property `JSON config > general > dsForceDrm > oneOf > item 1`](#general_dsForceDrm_oneOf_i1)
    - [3.5.3. Property `JSON config > general > dsForceDrm > oneOf > item 2`](#general_dsForceDrm_oneOf_i2)
  - [3.6. Property `JSON config > general > wmiTimeout`](#general_wmiTimeout)
  - [3.7. Property `JSON config > general > processingTimeout`](#general_processingTimeout)
- [4. Property `JSON config > display`](#display)
  - [4.1. Property `JSON config > display > stat`](#display_stat)
  - [4.2. Property `JSON config > display > pipe`](#display_pipe)
  - [4.3. Property `JSON config > display > showErrors`](#display_showErrors)
  - [4.4. Property `JSON config > display > disableLinewrap`](#display_disableLinewrap)
  - [4.5. Property `JSON config > display > hideCursor`](#display_hideCursor)
  - [4.6. Property `JSON config > display > separator`](#display_separator)
  - [4.7. Property `JSON config > display > color`](#display_color)
    - [4.7.1. Property `JSON config > display > color > oneOf > colors`](#display_color_oneOf_i0)
    - [4.7.2. Property `JSON config > display > color > oneOf > item 1`](#display_color_oneOf_i1)
      - [4.7.2.1. Property `JSON config > display > color > oneOf > item 1 > keys`](#display_color_oneOf_i1_keys)
      - [4.7.2.2. Property `JSON config > display > color > oneOf > item 1 > title`](#display_color_oneOf_i1_title)
      - [4.7.2.3. Property `JSON config > display > color > oneOf > item 1 > output`](#display_color_oneOf_i1_output)
      - [4.7.2.4. Property `JSON config > display > color > oneOf > item 1 > separator`](#display_color_oneOf_i1_separator)
  - [4.8. Property `JSON config > display > brightColor`](#display_brightColor)
  - [4.9. Property `JSON config > display > keyWidth`](#display_keyWidth)
  - [4.10. Property `JSON config > display > binaryPrefix`](#display_binaryPrefix)
    - [4.10.1. Property `JSON config > display > binaryPrefix > oneOf > item 0`](#display_binaryPrefix_oneOf_i0)
    - [4.10.2. Property `JSON config > display > binaryPrefix > oneOf > item 1`](#display_binaryPrefix_oneOf_i1)
    - [4.10.3. Property `JSON config > display > binaryPrefix > oneOf > item 2`](#display_binaryPrefix_oneOf_i2)
  - [4.11. Property `JSON config > display > size`](#display_size)
    - [4.11.1. Property `JSON config > display > size > maxPrefix`](#display_size_maxPrefix)
    - [4.11.2. Property `JSON config > display > size > ndigits`](#display_size_ndigits)
  - [4.12. Property `JSON config > display > temp`](#display_temp)
    - [4.12.1. Property `JSON config > display > temp > unit`](#display_temp_unit)
    - [4.12.2. Property `JSON config > display > temp > ndigits`](#display_temp_ndigits)
    - [4.12.3. Property `JSON config > display > temp > color`](#display_temp_color)
      - [4.12.3.1. Property `JSON config > display > temp > color > green`](#display_temp_color_green)
      - [4.12.3.2. Property `JSON config > display > temp > color > yellow`](#display_temp_color_yellow)
      - [4.12.3.3. Property `JSON config > display > temp > color > red`](#display_temp_color_red)
  - [4.13. Property `JSON config > display > bar`](#display_bar)
    - [4.13.1. Property `JSON config > display > bar > charElapsed`](#display_bar_charElapsed)
    - [4.13.2. Property `JSON config > display > bar > charTotal`](#display_bar_charTotal)
    - [4.13.3. Property `JSON config > display > bar > borderLeft`](#display_bar_borderLeft)
    - [4.13.4. Property `JSON config > display > bar > borderRight`](#display_bar_borderRight)
    - [4.13.5. Property `JSON config > display > bar > width`](#display_bar_width)
  - [4.14. Property `JSON config > display > percent`](#display_percent)
    - [4.14.1. Property `JSON config > display > percent > type`](#display_percent_type)
    - [4.14.2. Property `JSON config > display > percent > ndigits`](#display_percent_ndigits)
    - [4.14.3. Property `JSON config > display > percent > color`](#display_percent_color)
      - [4.14.3.1. Property `JSON config > display > percent > color > green`](#display_percent_color_green)
      - [4.14.3.2. Property `JSON config > display > percent > color > yellow`](#display_percent_color_yellow)
      - [4.14.3.3. Property `JSON config > display > percent > color > red`](#display_percent_color_red)
  - [4.15. Property `JSON config > display > noBuffer`](#display_noBuffer)
  - [4.16. Property `JSON config > display > tsVersion`](#display_tsVersion)
- [5. Property `JSON config > library`](#library)
  - [5.1. Property `JSON config > library > pci`](#library_pci)
  - [5.2. Property `JSON config > library > vulkan`](#library_vulkan)
  - [5.3. Property `JSON config > library > freetype`](#library_freetype)
  - [5.4. Property `JSON config > library > wayland`](#library_wayland)
  - [5.5. Property `JSON config > library > xcbRandr`](#library_xcbRandr)
  - [5.6. Property `JSON config > library > xcb`](#library_xcb)
  - [5.7. Property `JSON config > library > xrandr`](#library_xrandr)
  - [5.8. Property `JSON config > library > x11`](#library_x11)
  - [5.9. Property `JSON config > library > drm`](#library_drm)
  - [5.10. Property `JSON config > library > gio`](#library_gio)
  - [5.11. Property `JSON config > library > dconf`](#library_dconf)
  - [5.12. Property `JSON config > library > dbus`](#library_dbus)
  - [5.13. Property `JSON config > library > xfconf`](#library_xfconf)
  - [5.14. Property `JSON config > library > sqlite3`](#library_sqlite3)
  - [5.15. Property `JSON config > library > rpm`](#library_rpm)
  - [5.16. Property `JSON config > library > imagemagick`](#library_imagemagick)
  - [5.17. Property `JSON config > library > z`](#library_z)
  - [5.18. Property `JSON config > library > chafa`](#library_chafa)
  - [5.19. Property `JSON config > library > egl`](#library_egl)
  - [5.20. Property `JSON config > library > glx`](#library_glx)
  - [5.21. Property `JSON config > library > osmesa`](#library_osmesa)
  - [5.22. Property `JSON config > library > opencl`](#library_opencl)
  - [5.23. Property `JSON config > library > pulse`](#library_pulse)
  - [5.24. Property `JSON config > library > nm`](#library_nm)
  - [5.25. Property `JSON config > library > ddcutil`](#library_ddcutil)
- [6. Property `JSON config > modules`](#modules)
  - [6.1. JSON config > modules > modules items](#autogenerated_heading_2)
    - [6.1.1. Property `JSON config > modules > modules items > anyOf > item 0`](#modules_items_anyOf_i0)
    - [6.1.2. Property `JSON config > modules > modules items > anyOf > item 1`](#modules_items_anyOf_i1)
      - [6.1.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Break`](#modules_items_anyOf_i1_oneOf_i0)
        - [6.1.2.1.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Break > type`](#modules_items_anyOf_i1_oneOf_i0_type)
      - [6.1.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module`](#modules_items_anyOf_i1_oneOf_i1)
        - [6.1.2.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type`](#modules_items_anyOf_i1_oneOf_i1_type)
          - [6.1.2.2.1.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 0`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i0)
          - [6.1.2.2.1.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 1`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i1)
          - [6.1.2.2.1.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 2`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i2)
          - [6.1.2.2.1.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 3`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i3)
          - [6.1.2.2.1.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 4`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i4)
          - [6.1.2.2.1.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 5`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i5)
          - [6.1.2.2.1.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 6`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i6)
          - [6.1.2.2.1.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 7`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i7)
          - [6.1.2.2.1.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 8`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i8)
          - [6.1.2.2.1.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 9`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i9)
          - [6.1.2.2.1.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 10`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i10)
          - [6.1.2.2.1.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 11`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i11)
          - [6.1.2.2.1.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 12`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i12)
          - [6.1.2.2.1.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 13`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i13)
          - [6.1.2.2.1.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 14`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i14)
          - [6.1.2.2.1.16. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 15`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i15)
          - [6.1.2.2.1.17. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 16`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i16)
          - [6.1.2.2.1.18. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 17`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i17)
          - [6.1.2.2.1.19. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 18`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i18)
          - [6.1.2.2.1.20. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 19`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i19)
          - [6.1.2.2.1.21. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 20`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i20)
          - [6.1.2.2.1.22. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 21`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i21)
          - [6.1.2.2.1.23. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 22`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i22)
          - [6.1.2.2.1.24. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 23`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i23)
          - [6.1.2.2.1.25. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 24`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i24)
          - [6.1.2.2.1.26. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 25`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i25)
          - [6.1.2.2.1.27. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 26`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i26)
          - [6.1.2.2.1.28. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 27`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i27)
          - [6.1.2.2.1.29. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 28`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i28)
          - [6.1.2.2.1.30. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 29`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i29)
          - [6.1.2.2.1.31. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 30`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i30)
          - [6.1.2.2.1.32. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 31`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i31)
          - [6.1.2.2.1.33. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 32`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i32)
          - [6.1.2.2.1.34. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 33`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i33)
          - [6.1.2.2.1.35. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 34`](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i34)
        - [6.1.2.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > key`](#modules_items_anyOf_i1_oneOf_i1_key)
        - [6.1.2.2.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > keyColor`](#modules_items_anyOf_i1_oneOf_i1_keyColor)
        - [6.1.2.2.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > outputColor`](#modules_items_anyOf_i1_oneOf_i1_outputColor)
        - [6.1.2.2.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > keyWidth`](#modules_items_anyOf_i1_oneOf_i1_keyWidth)
        - [6.1.2.2.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > format`](#modules_items_anyOf_i1_oneOf_i1_format)
      - [6.1.2.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery`](#modules_items_anyOf_i1_oneOf_i2)
        - [6.1.2.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > type`](#modules_items_anyOf_i1_oneOf_i2_type)
        - [6.1.2.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > useSetupApi`](#modules_items_anyOf_i1_oneOf_i2_useSetupApi)
        - [6.1.2.3.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp`](#modules_items_anyOf_i1_oneOf_i2_temp)
          - [6.1.2.3.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 0`](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i0)
          - [6.1.2.3.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1`](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1)
            - [6.1.2.3.3.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1 > green`](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_green)
            - [6.1.2.3.3.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1 > yellow`](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_yellow)
        - [6.1.2.3.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent`](#modules_items_anyOf_i1_oneOf_i2_percent)
          - [6.1.2.3.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent > green`](#modules_items_anyOf_i1_oneOf_i2_percent_green)
          - [6.1.2.3.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent > yellow`](#modules_items_anyOf_i1_oneOf_i2_percent_yellow)
        - [6.1.2.3.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > key`](#modules_items_anyOf_i1_oneOf_i2_key)
        - [6.1.2.3.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > keyColor`](#modules_items_anyOf_i1_oneOf_i2_keyColor)
        - [6.1.2.3.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > outputColor`](#modules_items_anyOf_i1_oneOf_i2_outputColor)
        - [6.1.2.3.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > keyWidth`](#modules_items_anyOf_i1_oneOf_i2_keyWidth)
        - [6.1.2.3.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > format`](#modules_items_anyOf_i1_oneOf_i2_format)
      - [6.1.2.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth`](#modules_items_anyOf_i1_oneOf_i3)
        - [6.1.2.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > type`](#modules_items_anyOf_i1_oneOf_i3_type)
        - [6.1.2.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > showDisconnected`](#modules_items_anyOf_i1_oneOf_i3_showDisconnected)
        - [6.1.2.4.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > percent`](#modules_items_anyOf_i1_oneOf_i3_percent)
        - [6.1.2.4.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > key`](#modules_items_anyOf_i1_oneOf_i3_key)
        - [6.1.2.4.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > keyColor`](#modules_items_anyOf_i1_oneOf_i3_keyColor)
        - [6.1.2.4.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > outputColor`](#modules_items_anyOf_i1_oneOf_i3_outputColor)
        - [6.1.2.4.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > keyWidth`](#modules_items_anyOf_i1_oneOf_i3_keyWidth)
        - [6.1.2.4.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > format`](#modules_items_anyOf_i1_oneOf_i3_format)
      - [6.1.2.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness`](#modules_items_anyOf_i1_oneOf_i4)
        - [6.1.2.5.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > type`](#modules_items_anyOf_i1_oneOf_i4_type)
        - [6.1.2.5.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > percent`](#modules_items_anyOf_i1_oneOf_i4_percent)
        - [6.1.2.5.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > ddcciSleep`](#modules_items_anyOf_i1_oneOf_i4_ddcciSleep)
        - [6.1.2.5.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > key`](#modules_items_anyOf_i1_oneOf_i4_key)
        - [6.1.2.5.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > keyColor`](#modules_items_anyOf_i1_oneOf_i4_keyColor)
        - [6.1.2.5.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > outputColor`](#modules_items_anyOf_i1_oneOf_i4_outputColor)
        - [6.1.2.5.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > keyWidth`](#modules_items_anyOf_i1_oneOf_i4_keyWidth)
        - [6.1.2.5.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > format`](#modules_items_anyOf_i1_oneOf_i4_format)
      - [6.1.2.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis`](#modules_items_anyOf_i1_oneOf_i5)
        - [6.1.2.6.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > type`](#modules_items_anyOf_i1_oneOf_i5_type)
        - [6.1.2.6.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > key`](#modules_items_anyOf_i1_oneOf_i5_key)
        - [6.1.2.6.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > keyColor`](#modules_items_anyOf_i1_oneOf_i5_keyColor)
        - [6.1.2.6.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > outputColor`](#modules_items_anyOf_i1_oneOf_i5_outputColor)
        - [6.1.2.6.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > keyWidth`](#modules_items_anyOf_i1_oneOf_i5_keyWidth)
        - [6.1.2.6.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > format`](#modules_items_anyOf_i1_oneOf_i5_format)
      - [6.1.2.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU`](#modules_items_anyOf_i1_oneOf_i6)
        - [6.1.2.7.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > type`](#modules_items_anyOf_i1_oneOf_i6_type)
        - [6.1.2.7.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > temp`](#modules_items_anyOf_i1_oneOf_i6_temp)
        - [6.1.2.7.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > freqNdigits`](#modules_items_anyOf_i1_oneOf_i6_freqNdigits)
        - [6.1.2.7.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > showPeCoreCount`](#modules_items_anyOf_i1_oneOf_i6_showPeCoreCount)
        - [6.1.2.7.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > key`](#modules_items_anyOf_i1_oneOf_i6_key)
        - [6.1.2.7.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > keyColor`](#modules_items_anyOf_i1_oneOf_i6_keyColor)
        - [6.1.2.7.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > outputColor`](#modules_items_anyOf_i1_oneOf_i6_outputColor)
        - [6.1.2.7.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > keyWidth`](#modules_items_anyOf_i1_oneOf_i6_keyWidth)
        - [6.1.2.7.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > format`](#modules_items_anyOf_i1_oneOf_i6_format)
      - [6.1.2.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage`](#modules_items_anyOf_i1_oneOf_i7)
        - [6.1.2.8.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > type`](#modules_items_anyOf_i1_oneOf_i7_type)
        - [6.1.2.8.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > percent`](#modules_items_anyOf_i1_oneOf_i7_percent)
        - [6.1.2.8.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > separate`](#modules_items_anyOf_i1_oneOf_i7_separate)
      - [6.1.2.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors`](#modules_items_anyOf_i1_oneOf_i8)
        - [6.1.2.9.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > type`](#modules_items_anyOf_i1_oneOf_i8_type)
        - [6.1.2.9.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > symbol`](#modules_items_anyOf_i1_oneOf_i8_symbol)
        - [6.1.2.9.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > paddingLeft`](#modules_items_anyOf_i1_oneOf_i8_paddingLeft)
        - [6.1.2.9.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block`](#modules_items_anyOf_i1_oneOf_i8_block)
          - [6.1.2.9.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > width`](#modules_items_anyOf_i1_oneOf_i8_block_width)
          - [6.1.2.9.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > range`](#modules_items_anyOf_i1_oneOf_i8_block_range)
            - [6.1.2.9.4.2.1. JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > range > range items](#autogenerated_heading_3)
        - [6.1.2.9.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > key`](#modules_items_anyOf_i1_oneOf_i8_key)
      - [6.1.2.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command`](#modules_items_anyOf_i1_oneOf_i9)
        - [6.1.2.10.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > type`](#modules_items_anyOf_i1_oneOf_i9_type)
        - [6.1.2.10.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > shell`](#modules_items_anyOf_i1_oneOf_i9_shell)
        - [6.1.2.10.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > text`](#modules_items_anyOf_i1_oneOf_i9_text)
        - [6.1.2.10.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > key`](#modules_items_anyOf_i1_oneOf_i9_key)
        - [6.1.2.10.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > keyColor`](#modules_items_anyOf_i1_oneOf_i9_keyColor)
        - [6.1.2.10.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > outputColor`](#modules_items_anyOf_i1_oneOf_i9_outputColor)
        - [6.1.2.10.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > keyWidth`](#modules_items_anyOf_i1_oneOf_i9_keyWidth)
        - [6.1.2.10.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > format`](#modules_items_anyOf_i1_oneOf_i9_format)
      - [6.1.2.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom`](#modules_items_anyOf_i1_oneOf_i10)
        - [6.1.2.11.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > type`](#modules_items_anyOf_i1_oneOf_i10_type)
        - [6.1.2.11.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > key`](#modules_items_anyOf_i1_oneOf_i10_key)
        - [6.1.2.11.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > keyColor`](#modules_items_anyOf_i1_oneOf_i10_keyColor)
        - [6.1.2.11.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > outputColor`](#modules_items_anyOf_i1_oneOf_i10_outputColor)
        - [6.1.2.11.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > keyWidth`](#modules_items_anyOf_i1_oneOf_i10_keyWidth)
        - [6.1.2.11.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > format`](#modules_items_anyOf_i1_oneOf_i10_format)
      - [6.1.2.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display`](#modules_items_anyOf_i1_oneOf_i11)
        - [6.1.2.12.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > type`](#modules_items_anyOf_i1_oneOf_i11_type)
        - [6.1.2.12.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > compactType`](#modules_items_anyOf_i1_oneOf_i11_compactType)
        - [6.1.2.12.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > preciseRefreshRate`](#modules_items_anyOf_i1_oneOf_i11_preciseRefreshRate)
        - [6.1.2.12.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > order`](#modules_items_anyOf_i1_oneOf_i11_order)
        - [6.1.2.12.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > key`](#modules_items_anyOf_i1_oneOf_i11_key)
        - [6.1.2.12.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > keyColor`](#modules_items_anyOf_i1_oneOf_i11_keyColor)
        - [6.1.2.12.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > outputColor`](#modules_items_anyOf_i1_oneOf_i11_outputColor)
        - [6.1.2.12.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > keyWidth`](#modules_items_anyOf_i1_oneOf_i11_keyWidth)
        - [6.1.2.12.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > format`](#modules_items_anyOf_i1_oneOf_i11_format)
      - [6.1.2.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk`](#modules_items_anyOf_i1_oneOf_i12)
        - [6.1.2.13.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > type`](#modules_items_anyOf_i1_oneOf_i12_type)
        - [6.1.2.13.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > folders`](#modules_items_anyOf_i1_oneOf_i12_folders)
        - [6.1.2.13.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showExternal`](#modules_items_anyOf_i1_oneOf_i12_showExternal)
        - [6.1.2.13.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showHidden`](#modules_items_anyOf_i1_oneOf_i12_showHidden)
        - [6.1.2.13.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showSubvolumes`](#modules_items_anyOf_i1_oneOf_i12_showSubvolumes)
        - [6.1.2.13.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showReadOnly`](#modules_items_anyOf_i1_oneOf_i12_showReadOnly)
        - [6.1.2.13.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showUnknown`](#modules_items_anyOf_i1_oneOf_i12_showUnknown)
        - [6.1.2.13.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > useAvailable`](#modules_items_anyOf_i1_oneOf_i12_useAvailable)
        - [6.1.2.13.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > percent`](#modules_items_anyOf_i1_oneOf_i12_percent)
        - [6.1.2.13.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > key`](#modules_items_anyOf_i1_oneOf_i12_key)
        - [6.1.2.13.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > keyColor`](#modules_items_anyOf_i1_oneOf_i12_keyColor)
        - [6.1.2.13.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > outputColor`](#modules_items_anyOf_i1_oneOf_i12_outputColor)
        - [6.1.2.13.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > keyWidth`](#modules_items_anyOf_i1_oneOf_i12_keyWidth)
        - [6.1.2.13.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > format`](#modules_items_anyOf_i1_oneOf_i12_format)
      - [6.1.2.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO`](#modules_items_anyOf_i1_oneOf_i13)
        - [6.1.2.14.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > type`](#modules_items_anyOf_i1_oneOf_i13_type)
        - [6.1.2.14.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > namePrefix`](#modules_items_anyOf_i1_oneOf_i13_namePrefix)
        - [6.1.2.14.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > detectTotal`](#modules_items_anyOf_i1_oneOf_i13_detectTotal)
        - [6.1.2.14.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > key`](#modules_items_anyOf_i1_oneOf_i13_key)
        - [6.1.2.14.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > keyColor`](#modules_items_anyOf_i1_oneOf_i13_keyColor)
        - [6.1.2.14.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > outputColor`](#modules_items_anyOf_i1_oneOf_i13_outputColor)
        - [6.1.2.14.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > keyWidth`](#modules_items_anyOf_i1_oneOf_i13_keyWidth)
        - [6.1.2.14.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > format`](#modules_items_anyOf_i1_oneOf_i13_format)
      - [6.1.2.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE`](#modules_items_anyOf_i1_oneOf_i14)
        - [6.1.2.15.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > type`](#modules_items_anyOf_i1_oneOf_i14_type)
        - [6.1.2.15.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > slowVersionDetection`](#modules_items_anyOf_i1_oneOf_i14_slowVersionDetection)
        - [6.1.2.15.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > key`](#modules_items_anyOf_i1_oneOf_i14_key)
        - [6.1.2.15.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > keyColor`](#modules_items_anyOf_i1_oneOf_i14_keyColor)
        - [6.1.2.15.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > outputColor`](#modules_items_anyOf_i1_oneOf_i14_outputColor)
        - [6.1.2.15.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > keyWidth`](#modules_items_anyOf_i1_oneOf_i14_keyWidth)
        - [6.1.2.15.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > format`](#modules_items_anyOf_i1_oneOf_i14_format)
      - [6.1.2.16. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS`](#modules_items_anyOf_i1_oneOf_i15)
        - [6.1.2.16.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > type`](#modules_items_anyOf_i1_oneOf_i15_type)
        - [6.1.2.16.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > showType`](#modules_items_anyOf_i1_oneOf_i15_showType)
        - [6.1.2.16.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > key`](#modules_items_anyOf_i1_oneOf_i15_key)
        - [6.1.2.16.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > keyColor`](#modules_items_anyOf_i1_oneOf_i15_keyColor)
        - [6.1.2.16.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > outputColor`](#modules_items_anyOf_i1_oneOf_i15_outputColor)
        - [6.1.2.16.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > keyWidth`](#modules_items_anyOf_i1_oneOf_i15_keyWidth)
        - [6.1.2.16.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > format`](#modules_items_anyOf_i1_oneOf_i15_format)
      - [6.1.2.17. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad`](#modules_items_anyOf_i1_oneOf_i16)
        - [6.1.2.17.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > type`](#modules_items_anyOf_i1_oneOf_i16_type)
        - [6.1.2.17.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > percent`](#modules_items_anyOf_i1_oneOf_i16_percent)
        - [6.1.2.17.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > key`](#modules_items_anyOf_i1_oneOf_i16_key)
        - [6.1.2.17.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > keyColor`](#modules_items_anyOf_i1_oneOf_i16_keyColor)
        - [6.1.2.17.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > outputColor`](#modules_items_anyOf_i1_oneOf_i16_outputColor)
        - [6.1.2.17.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > keyWidth`](#modules_items_anyOf_i1_oneOf_i16_keyWidth)
        - [6.1.2.17.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > format`](#modules_items_anyOf_i1_oneOf_i16_format)
      - [6.1.2.18. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU`](#modules_items_anyOf_i1_oneOf_i17)
        - [6.1.2.18.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > type`](#modules_items_anyOf_i1_oneOf_i17_type)
        - [6.1.2.18.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > temp`](#modules_items_anyOf_i1_oneOf_i17_temp)
        - [6.1.2.18.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > driverSpecific`](#modules_items_anyOf_i1_oneOf_i17_driverSpecific)
        - [6.1.2.18.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > detectionMethod`](#modules_items_anyOf_i1_oneOf_i17_detectionMethod)
        - [6.1.2.18.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > hideType`](#modules_items_anyOf_i1_oneOf_i17_hideType)
        - [6.1.2.18.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > key`](#modules_items_anyOf_i1_oneOf_i17_key)
        - [6.1.2.18.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > keyColor`](#modules_items_anyOf_i1_oneOf_i17_keyColor)
        - [6.1.2.18.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > outputColor`](#modules_items_anyOf_i1_oneOf_i17_outputColor)
        - [6.1.2.18.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > keyWidth`](#modules_items_anyOf_i1_oneOf_i17_keyWidth)
        - [6.1.2.18.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > format`](#modules_items_anyOf_i1_oneOf_i17_format)
      - [6.1.2.19. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP`](#modules_items_anyOf_i1_oneOf_i18)
        - [6.1.2.19.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > type`](#modules_items_anyOf_i1_oneOf_i18_type)
        - [6.1.2.19.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showIpv4`](#modules_items_anyOf_i1_oneOf_i18_showIpv4)
        - [6.1.2.19.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showIpv6`](#modules_items_anyOf_i1_oneOf_i18_showIpv6)
        - [6.1.2.19.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showMac`](#modules_items_anyOf_i1_oneOf_i18_showMac)
        - [6.1.2.19.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showLoop`](#modules_items_anyOf_i1_oneOf_i18_showLoop)
        - [6.1.2.19.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showPrefixLen`](#modules_items_anyOf_i1_oneOf_i18_showPrefixLen)
        - [6.1.2.19.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showAllIps`](#modules_items_anyOf_i1_oneOf_i18_showAllIps)
        - [6.1.2.19.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > compact`](#modules_items_anyOf_i1_oneOf_i18_compact)
        - [6.1.2.19.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > namePrefix`](#modules_items_anyOf_i1_oneOf_i18_namePrefix)
        - [6.1.2.19.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > defaultRouteOnly`](#modules_items_anyOf_i1_oneOf_i18_defaultRouteOnly)
        - [6.1.2.19.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > key`](#modules_items_anyOf_i1_oneOf_i18_key)
        - [6.1.2.19.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > keyColor`](#modules_items_anyOf_i1_oneOf_i18_keyColor)
        - [6.1.2.19.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > outputColor`](#modules_items_anyOf_i1_oneOf_i18_outputColor)
        - [6.1.2.19.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > keyWidth`](#modules_items_anyOf_i1_oneOf_i18_keyWidth)
        - [6.1.2.19.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > format`](#modules_items_anyOf_i1_oneOf_i18_format)
      - [6.1.2.20. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory`](#modules_items_anyOf_i1_oneOf_i19)
        - [6.1.2.20.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > type`](#modules_items_anyOf_i1_oneOf_i19_type)
        - [6.1.2.20.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > percent`](#modules_items_anyOf_i1_oneOf_i19_percent)
        - [6.1.2.20.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > key`](#modules_items_anyOf_i1_oneOf_i19_key)
        - [6.1.2.20.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > keyColor`](#modules_items_anyOf_i1_oneOf_i19_keyColor)
        - [6.1.2.20.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > outputColor`](#modules_items_anyOf_i1_oneOf_i19_outputColor)
        - [6.1.2.20.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > keyWidth`](#modules_items_anyOf_i1_oneOf_i19_keyWidth)
        - [6.1.2.20.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > format`](#modules_items_anyOf_i1_oneOf_i19_format)
      - [6.1.2.21. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg`](#modules_items_anyOf_i1_oneOf_i20)
        - [6.1.2.21.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > type`](#modules_items_anyOf_i1_oneOf_i20_type)
        - [6.1.2.21.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > ndigits`](#modules_items_anyOf_i1_oneOf_i20_ndigits)
        - [6.1.2.21.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > compact`](#modules_items_anyOf_i1_oneOf_i20_compact)
        - [6.1.2.21.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > percent`](#modules_items_anyOf_i1_oneOf_i20_percent)
        - [6.1.2.21.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > key`](#modules_items_anyOf_i1_oneOf_i20_key)
        - [6.1.2.21.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > keyColor`](#modules_items_anyOf_i1_oneOf_i20_keyColor)
        - [6.1.2.21.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > outputColor`](#modules_items_anyOf_i1_oneOf_i20_outputColor)
        - [6.1.2.21.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > keyWidth`](#modules_items_anyOf_i1_oneOf_i20_keyWidth)
        - [6.1.2.21.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > format`](#modules_items_anyOf_i1_oneOf_i20_format)
      - [6.1.2.22. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO`](#modules_items_anyOf_i1_oneOf_i21)
        - [6.1.2.22.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > type`](#modules_items_anyOf_i1_oneOf_i21_type)
        - [6.1.2.22.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > namePrefix`](#modules_items_anyOf_i1_oneOf_i21_namePrefix)
        - [6.1.2.22.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > defaultRouteOnly`](#modules_items_anyOf_i1_oneOf_i21_defaultRouteOnly)
        - [6.1.2.22.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > detectTotal`](#modules_items_anyOf_i1_oneOf_i21_detectTotal)
        - [6.1.2.22.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > key`](#modules_items_anyOf_i1_oneOf_i21_key)
        - [6.1.2.22.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > keyColor`](#modules_items_anyOf_i1_oneOf_i21_keyColor)
        - [6.1.2.22.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > outputColor`](#modules_items_anyOf_i1_oneOf_i21_outputColor)
        - [6.1.2.22.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > keyWidth`](#modules_items_anyOf_i1_oneOf_i21_keyWidth)
        - [6.1.2.22.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > format`](#modules_items_anyOf_i1_oneOf_i21_format)
      - [6.1.2.23. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL`](#modules_items_anyOf_i1_oneOf_i22)
        - [6.1.2.23.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > type`](#modules_items_anyOf_i1_oneOf_i22_type)
        - [6.1.2.23.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > library`](#modules_items_anyOf_i1_oneOf_i22_library)
        - [6.1.2.23.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > key`](#modules_items_anyOf_i1_oneOf_i22_key)
        - [6.1.2.23.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > keyColor`](#modules_items_anyOf_i1_oneOf_i22_keyColor)
        - [6.1.2.23.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > outputColor`](#modules_items_anyOf_i1_oneOf_i22_outputColor)
        - [6.1.2.23.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > keyWidth`](#modules_items_anyOf_i1_oneOf_i22_keyWidth)
        - [6.1.2.23.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > format`](#modules_items_anyOf_i1_oneOf_i22_format)
      - [6.1.2.24. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages`](#modules_items_anyOf_i1_oneOf_i23)
        - [6.1.2.24.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > type`](#modules_items_anyOf_i1_oneOf_i23_type)
        - [6.1.2.24.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > disabled`](#modules_items_anyOf_i1_oneOf_i23_disabled)
          - [6.1.2.24.2.1. JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > disabled > disabled items](#autogenerated_heading_4)
        - [6.1.2.24.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > key`](#modules_items_anyOf_i1_oneOf_i23_key)
        - [6.1.2.24.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > keyColor`](#modules_items_anyOf_i1_oneOf_i23_keyColor)
        - [6.1.2.24.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > outputColor`](#modules_items_anyOf_i1_oneOf_i23_outputColor)
        - [6.1.2.24.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > keyWidth`](#modules_items_anyOf_i1_oneOf_i23_keyWidth)
        - [6.1.2.24.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > format`](#modules_items_anyOf_i1_oneOf_i23_format)
      - [6.1.2.25. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk`](#modules_items_anyOf_i1_oneOf_i24)
        - [6.1.2.25.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > type`](#modules_items_anyOf_i1_oneOf_i24_type)
        - [6.1.2.25.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > namePrefix`](#modules_items_anyOf_i1_oneOf_i24_namePrefix)
        - [6.1.2.25.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > temp`](#modules_items_anyOf_i1_oneOf_i24_temp)
        - [6.1.2.25.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > key`](#modules_items_anyOf_i1_oneOf_i24_key)
        - [6.1.2.25.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > keyColor`](#modules_items_anyOf_i1_oneOf_i24_keyColor)
        - [6.1.2.25.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > outputColor`](#modules_items_anyOf_i1_oneOf_i24_outputColor)
        - [6.1.2.25.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > keyWidth`](#modules_items_anyOf_i1_oneOf_i24_keyWidth)
        - [6.1.2.25.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > format`](#modules_items_anyOf_i1_oneOf_i24_format)
      - [6.1.2.26. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP`](#modules_items_anyOf_i1_oneOf_i25)
        - [6.1.2.26.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > type`](#modules_items_anyOf_i1_oneOf_i25_type)
        - [6.1.2.26.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > url`](#modules_items_anyOf_i1_oneOf_i25_url)
        - [6.1.2.26.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > timeout`](#modules_items_anyOf_i1_oneOf_i25_timeout)
        - [6.1.2.26.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > ipv6`](#modules_items_anyOf_i1_oneOf_i25_ipv6)
        - [6.1.2.26.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > key`](#modules_items_anyOf_i1_oneOf_i25_key)
        - [6.1.2.26.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > keyColor`](#modules_items_anyOf_i1_oneOf_i25_keyColor)
        - [6.1.2.26.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > outputColor`](#modules_items_anyOf_i1_oneOf_i25_outputColor)
        - [6.1.2.26.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > keyWidth`](#modules_items_anyOf_i1_oneOf_i25_keyWidth)
        - [6.1.2.26.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > format`](#modules_items_anyOf_i1_oneOf_i25_format)
      - [6.1.2.27. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator`](#modules_items_anyOf_i1_oneOf_i26)
        - [6.1.2.27.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > type`](#modules_items_anyOf_i1_oneOf_i26_type)
        - [6.1.2.27.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > string`](#modules_items_anyOf_i1_oneOf_i26_string)
        - [6.1.2.27.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > outputColor`](#modules_items_anyOf_i1_oneOf_i26_outputColor)
      - [6.1.2.28. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound`](#modules_items_anyOf_i1_oneOf_i27)
        - [6.1.2.28.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > type`](#modules_items_anyOf_i1_oneOf_i27_type)
        - [6.1.2.28.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > soundType`](#modules_items_anyOf_i1_oneOf_i27_soundType)
        - [6.1.2.28.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > percent`](#modules_items_anyOf_i1_oneOf_i27_percent)
        - [6.1.2.28.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > key`](#modules_items_anyOf_i1_oneOf_i27_key)
        - [6.1.2.28.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > keyColor`](#modules_items_anyOf_i1_oneOf_i27_keyColor)
        - [6.1.2.28.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > outputColor`](#modules_items_anyOf_i1_oneOf_i27_outputColor)
        - [6.1.2.28.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > keyWidth`](#modules_items_anyOf_i1_oneOf_i27_keyWidth)
        - [6.1.2.28.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > format`](#modules_items_anyOf_i1_oneOf_i27_format)
      - [6.1.2.29. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap`](#modules_items_anyOf_i1_oneOf_i28)
        - [6.1.2.29.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > type`](#modules_items_anyOf_i1_oneOf_i28_type)
        - [6.1.2.29.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > percent`](#modules_items_anyOf_i1_oneOf_i28_percent)
        - [6.1.2.29.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > key`](#modules_items_anyOf_i1_oneOf_i28_key)
        - [6.1.2.29.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > keyColor`](#modules_items_anyOf_i1_oneOf_i28_keyColor)
        - [6.1.2.29.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > outputColor`](#modules_items_anyOf_i1_oneOf_i28_outputColor)
        - [6.1.2.29.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > keyWidth`](#modules_items_anyOf_i1_oneOf_i28_keyWidth)
        - [6.1.2.29.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > format`](#modules_items_anyOf_i1_oneOf_i28_format)
      - [6.1.2.30. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title`](#modules_items_anyOf_i1_oneOf_i29)
        - [6.1.2.30.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > type`](#modules_items_anyOf_i1_oneOf_i29_type)
        - [6.1.2.30.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > fqdn`](#modules_items_anyOf_i1_oneOf_i29_fqdn)
        - [6.1.2.30.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color`](#modules_items_anyOf_i1_oneOf_i29_color)
          - [6.1.2.30.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > user`](#modules_items_anyOf_i1_oneOf_i29_color_user)
          - [6.1.2.30.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > at`](#modules_items_anyOf_i1_oneOf_i29_color_at)
          - [6.1.2.30.3.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > host`](#modules_items_anyOf_i1_oneOf_i29_color_host)
        - [6.1.2.30.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > key`](#modules_items_anyOf_i1_oneOf_i29_key)
        - [6.1.2.30.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > keyColor`](#modules_items_anyOf_i1_oneOf_i29_keyColor)
        - [6.1.2.30.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > outputColor`](#modules_items_anyOf_i1_oneOf_i29_outputColor)
        - [6.1.2.30.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > keyWidth`](#modules_items_anyOf_i1_oneOf_i29_keyWidth)
        - [6.1.2.30.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > format`](#modules_items_anyOf_i1_oneOf_i29_format)
      - [6.1.2.31. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users`](#modules_items_anyOf_i1_oneOf_i30)
        - [6.1.2.31.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > type`](#modules_items_anyOf_i1_oneOf_i30_type)
        - [6.1.2.31.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > compact`](#modules_items_anyOf_i1_oneOf_i30_compact)
        - [6.1.2.31.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > myselfOnly`](#modules_items_anyOf_i1_oneOf_i30_myselfOnly)
        - [6.1.2.31.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > key`](#modules_items_anyOf_i1_oneOf_i30_key)
        - [6.1.2.31.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > keyColor`](#modules_items_anyOf_i1_oneOf_i30_keyColor)
        - [6.1.2.31.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > outputColor`](#modules_items_anyOf_i1_oneOf_i30_outputColor)
        - [6.1.2.31.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > keyWidth`](#modules_items_anyOf_i1_oneOf_i30_keyWidth)
        - [6.1.2.31.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > format`](#modules_items_anyOf_i1_oneOf_i30_format)
      - [6.1.2.32. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather`](#modules_items_anyOf_i1_oneOf_i31)
        - [6.1.2.32.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > type`](#modules_items_anyOf_i1_oneOf_i31_type)
        - [6.1.2.32.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > location`](#modules_items_anyOf_i1_oneOf_i31_location)
        - [6.1.2.32.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > timeout`](#modules_items_anyOf_i1_oneOf_i31_timeout)
        - [6.1.2.32.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > outputFormat`](#modules_items_anyOf_i1_oneOf_i31_outputFormat)
        - [6.1.2.32.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > key`](#modules_items_anyOf_i1_oneOf_i31_key)
        - [6.1.2.32.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > keyColor`](#modules_items_anyOf_i1_oneOf_i31_keyColor)
        - [6.1.2.32.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > outputColor`](#modules_items_anyOf_i1_oneOf_i31_outputColor)
        - [6.1.2.32.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > keyWidth`](#modules_items_anyOf_i1_oneOf_i31_keyWidth)
        - [6.1.2.32.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > format`](#modules_items_anyOf_i1_oneOf_i31_format)
      - [6.1.2.33. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM`](#modules_items_anyOf_i1_oneOf_i32)
        - [6.1.2.33.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > type`](#modules_items_anyOf_i1_oneOf_i32_type)
        - [6.1.2.33.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > detectPlugin`](#modules_items_anyOf_i1_oneOf_i32_detectPlugin)
        - [6.1.2.33.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > key`](#modules_items_anyOf_i1_oneOf_i32_key)
        - [6.1.2.33.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > keyColor`](#modules_items_anyOf_i1_oneOf_i32_keyColor)
        - [6.1.2.33.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > outputColor`](#modules_items_anyOf_i1_oneOf_i32_outputColor)
        - [6.1.2.33.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > keyWidth`](#modules_items_anyOf_i1_oneOf_i32_keyWidth)
        - [6.1.2.33.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > format`](#modules_items_anyOf_i1_oneOf_i32_format)
      - [6.1.2.34. Property `JSON config > modules > modules items > anyOf > item 1 > type`](#modules_items_anyOf_i1_type)

**Title:** JSON config

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** JSON config file for fastfetch. Usually be `~/.config/fastfetch.jsonc`

| Property               | Pattern | Type        | Deprecated | Definition | Title/Description                                                                                        |
| ---------------------- | ------- | ----------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------- |
| - [$schema](#schema )  | No      | string      | No         | -          | JSON schema URL, for JSON validation and IDE intelligence                                                |
| - [logo](#logo )       | No      | Combination | No         | -          | Fastfetch logo configurations<br />See also https://github.com/fastfetch-cli/fastfetch/wiki/Logo-options |
| - [general](#general ) | No      | object      | No         | -          | Fastfetch general configurations                                                                         |
| - [display](#display ) | No      | object      | No         | -          | Configure how things to be displayed                                                                     |
| - [library](#library ) | No      | object      | No         | -          | Set the path of a library to load                                                                        |
| - [modules](#modules ) | No      | array       | No         | -          | Fastfetch modules to run                                                                                 |

## <a name="schema"></a>1. Property `JSON config > $schema`

|              |                                                                             |
| ------------ | --------------------------------------------------------------------------- |
| **Type**     | `string`                                                                    |
| **Required** | No                                                                          |
| **Format**   | `uri`                                                                       |
| **Default**  | `"https://github.com/fastfetch-cli/fastfetch/raw/dev/doc/json_schema.json"` |

**Description:** JSON schema URL, for JSON validation and IDE intelligence

## <a name="logo"></a>2. Property `JSON config > logo`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

**Description:** Fastfetch logo configurations
See also https://github.com/fastfetch-cli/fastfetch/wiki/Logo-options

| One of(Option)           |
| ------------------------ |
| [item 0](#logo_oneOf_i0) |
| [item 1](#logo_oneOf_i1) |
| [item 2](#logo_oneOf_i2) |

### <a name="logo_oneOf_i0"></a>2.1. Property `JSON config > logo > oneOf > item 0`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Disable logo

Specific value: `{
    "description": "😅 ERROR in schema generation, a referenced schema could not be loaded, no documentation here unfortunately 🏜️"
}`

### <a name="logo_oneOf_i1"></a>2.2. Property `JSON config > logo > oneOf > item 1`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Set the source file of the logo

### <a name="logo_oneOf_i2"></a>2.3. Property `JSON config > logo > oneOf > item 2`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Fastfetch logo configurations

| Property                                                     | Pattern | Type             | Deprecated | Definition | Title/Description                                                                   |
| ------------------------------------------------------------ | ------- | ---------------- | ---------- | ---------- | ----------------------------------------------------------------------------------- |
| - [type](#logo_oneOf_i2_type )                               | No      | enum (of string) | No         | -          | Set the type of the logo given                                                      |
| - [source](#logo_oneOf_i2_source )                           | No      | string           | No         | -          | Set the source file of the logo                                                     |
| - [color](#logo_oneOf_i2_color )                             | No      | object           | No         | -          | Overwrite a color in the logo                                                       |
| - [width](#logo_oneOf_i2_width )                             | No      | integer          | No         | -          | Set the width of the logo (in characters). Required for iTerm image protocol        |
| - [height](#logo_oneOf_i2_height )                           | No      | integer          | No         | -          | Set the height of the logo (in characters). Required for iTerm image protocol       |
| - [padding](#logo_oneOf_i2_padding )                         | No      | object           | No         | -          | Set the padding of the logo                                                         |
| - [printRemaining](#logo_oneOf_i2_printRemaining )           | No      | boolean          | No         | -          | Whether to print the remaining logo, if it has more lines than modules to display   |
| - [preserveAspectRatio](#logo_oneOf_i2_preserveAspectRatio ) | No      | boolean          | No         | -          | Whether to preserve the aspect ratio of the logo. Supported by iTerm image protocol |
| - [recache](#logo_oneOf_i2_recache )                         | No      | boolean          | No         | -          | If true, regenerate image logo cache                                                |
| - [separate](#logo_oneOf_i2_separate )                       | No      | boolean          | No         | -          | If true, print modules at bottom of the logo                                        |
| - [chafa](#logo_oneOf_i2_chafa )                             | No      | object           | No         | -          | Chafa configuration. See chafa document for details                                 |

#### <a name="logo_oneOf_i2_type"></a>2.3.1. Property `JSON config > logo > oneOf > item 2 > type`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"auto"`           |

**Description:** Set the type of the logo given

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

#### <a name="logo_oneOf_i2_source"></a>2.3.2. Property `JSON config > logo > oneOf > item 2 > source`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Set the source file of the logo

#### <a name="logo_oneOf_i2_color"></a>2.3.3. Property `JSON config > logo > oneOf > item 2 > color`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Overwrite a color in the logo

| Property                       | Pattern | Type             | Deprecated | Definition                           | Title/Description |
| ------------------------------ | ------- | ---------------- | ---------- | ------------------------------------ | ----------------- |
| - [1](#logo_oneOf_i2_color_1 ) | No      | enum (of string) | No         | In #/$defs/colors                    | Color 1           |
| - [2](#logo_oneOf_i2_color_2 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 2           |
| - [3](#logo_oneOf_i2_color_3 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 3           |
| - [4](#logo_oneOf_i2_color_4 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 4           |
| - [5](#logo_oneOf_i2_color_5 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 5           |
| - [6](#logo_oneOf_i2_color_6 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 6           |
| - [7](#logo_oneOf_i2_color_7 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 7           |
| - [8](#logo_oneOf_i2_color_8 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 8           |
| - [9](#logo_oneOf_i2_color_9 ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color 9           |

##### <a name="logo_oneOf_i2_color_1"></a>2.3.3.1. Property `JSON config > logo > oneOf > item 2 > color > 1`

|                |                    |
| -------------- | ------------------ |
| **Type**       | `enum (of string)` |
| **Required**   | No                 |
| **Defined in** | #/$defs/colors     |

**Description:** Color 1

Must be one of:
* "reset_"
* "bright_"
* "dim_"
* "italic_"
* "underline_"
* "blink_"
* "inverse_"
* "hidden_"
* "strike_"
* "light_"
* "black"
* "red"
* "green"
* "yellow"
* "blue"
* "magenta"
* "cyan"
* "white"
* "default"

##### <a name="logo_oneOf_i2_color_2"></a>2.3.3.2. Property `JSON config > logo > oneOf > item 2 > color > 2`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 2

##### <a name="logo_oneOf_i2_color_3"></a>2.3.3.3. Property `JSON config > logo > oneOf > item 2 > color > 3`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 3

##### <a name="logo_oneOf_i2_color_4"></a>2.3.3.4. Property `JSON config > logo > oneOf > item 2 > color > 4`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 4

##### <a name="logo_oneOf_i2_color_5"></a>2.3.3.5. Property `JSON config > logo > oneOf > item 2 > color > 5`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 5

##### <a name="logo_oneOf_i2_color_6"></a>2.3.3.6. Property `JSON config > logo > oneOf > item 2 > color > 6`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 6

##### <a name="logo_oneOf_i2_color_7"></a>2.3.3.7. Property `JSON config > logo > oneOf > item 2 > color > 7`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 7

##### <a name="logo_oneOf_i2_color_8"></a>2.3.3.8. Property `JSON config > logo > oneOf > item 2 > color > 8`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 8

##### <a name="logo_oneOf_i2_color_9"></a>2.3.3.9. Property `JSON config > logo > oneOf > item 2 > color > 9`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color 9

#### <a name="logo_oneOf_i2_width"></a>2.3.4. Property `JSON config > logo > oneOf > item 2 > width`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Set the width of the logo (in characters). Required for iTerm image protocol

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

#### <a name="logo_oneOf_i2_height"></a>2.3.5. Property `JSON config > logo > oneOf > item 2 > height`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Set the height of the logo (in characters). Required for iTerm image protocol

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

#### <a name="logo_oneOf_i2_padding"></a>2.3.6. Property `JSON config > logo > oneOf > item 2 > padding`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set the padding of the logo

| Property                                 | Pattern | Type    | Deprecated | Definition | Title/Description                 |
| ---------------------------------------- | ------- | ------- | ---------- | ---------- | --------------------------------- |
| - [top](#logo_oneOf_i2_padding_top )     | No      | integer | No         | -          | Set the top padding of the logo   |
| - [left](#logo_oneOf_i2_padding_left )   | No      | integer | No         | -          | Set the left padding of the logo  |
| - [right](#logo_oneOf_i2_padding_right ) | No      | integer | No         | -          | Set the right padding of the logo |

##### <a name="logo_oneOf_i2_padding_top"></a>2.3.6.1. Property `JSON config > logo > oneOf > item 2 > padding > top`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Set the top padding of the logo

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="logo_oneOf_i2_padding_left"></a>2.3.6.2. Property `JSON config > logo > oneOf > item 2 > padding > left`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Set the left padding of the logo

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

##### <a name="logo_oneOf_i2_padding_right"></a>2.3.6.3. Property `JSON config > logo > oneOf > item 2 > padding > right`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Set the right padding of the logo

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

#### <a name="logo_oneOf_i2_printRemaining"></a>2.3.7. Property `JSON config > logo > oneOf > item 2 > printRemaining`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Whether to print the remaining logo, if it has more lines than modules to display

#### <a name="logo_oneOf_i2_preserveAspectRatio"></a>2.3.8. Property `JSON config > logo > oneOf > item 2 > preserveAspectRatio`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Whether to preserve the aspect ratio of the logo. Supported by iTerm image protocol

#### <a name="logo_oneOf_i2_recache"></a>2.3.9. Property `JSON config > logo > oneOf > item 2 > recache`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** If true, regenerate image logo cache

#### <a name="logo_oneOf_i2_separate"></a>2.3.10. Property `JSON config > logo > oneOf > item 2 > separate`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** If true, print modules at bottom of the logo

#### <a name="logo_oneOf_i2_chafa"></a>2.3.11. Property `JSON config > logo > oneOf > item 2 > chafa`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Chafa configuration. See chafa document for details

| Property                                         | Pattern | Type             | Deprecated | Definition | Title/Description                                                                                        |
| ------------------------------------------------ | ------- | ---------------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------- |
| - [fgOnly](#logo_oneOf_i2_chafa_fgOnly )         | No      | boolean          | No         | -          | Produce character-cell output using foreground colors only                                               |
| - [symbols](#logo_oneOf_i2_chafa_symbols )       | No      | string           | No         | -          | Specify character symbols to employ in final output                                                      |
| - [canvasMode](#logo_oneOf_i2_chafa_canvasMode ) | No      | enum (of string) | No         | -          | Determine how colors are used in the output. This value maps the value of enum ChafaCanvasMode.          |
| - [colorSpace](#logo_oneOf_i2_chafa_colorSpace ) | No      | enum (of string) | No         | -          | Set color space used for quantization. This value maps the value of enum ChafaColorSpace.                |
| - [ditherMode](#logo_oneOf_i2_chafa_ditherMode ) | No      | enum (of string) | No         | -          | Set output dither mode (No effect with 24-bit color). This value maps the value of enum ChafaDitherMode. |

##### <a name="logo_oneOf_i2_chafa_fgOnly"></a>2.3.11.1. Property `JSON config > logo > oneOf > item 2 > chafa > fgOnly`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Produce character-cell output using foreground colors only

##### <a name="logo_oneOf_i2_chafa_symbols"></a>2.3.11.2. Property `JSON config > logo > oneOf > item 2 > chafa > symbols`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Specify character symbols to employ in final output

##### <a name="logo_oneOf_i2_chafa_canvasMode"></a>2.3.11.3. Property `JSON config > logo > oneOf > item 2 > chafa > canvasMode`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

**Description:** Determine how colors are used in the output. This value maps the value of enum ChafaCanvasMode.

Must be one of:
* "TRUECOLOR"
* "INDEXED_256"
* "INDEXED_240"
* "INDEXED_16"
* "FGBG_BGFG"
* "FGBG"
* "INDEXED_8"
* "INDEXED_16_8"

##### <a name="logo_oneOf_i2_chafa_colorSpace"></a>2.3.11.4. Property `JSON config > logo > oneOf > item 2 > chafa > colorSpace`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

**Description:** Set color space used for quantization. This value maps the value of enum ChafaColorSpace.

Must be one of:
* "RGB"
* "DIN99D"

##### <a name="logo_oneOf_i2_chafa_ditherMode"></a>2.3.11.5. Property `JSON config > logo > oneOf > item 2 > chafa > ditherMode`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

**Description:** Set output dither mode (No effect with 24-bit color). This value maps the value of enum ChafaDitherMode.

Must be one of:
* "NONE"
* "ORDERED"
* "DIFFUSION"

## <a name="general"></a>3. Property `JSON config > general`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Fastfetch general configurations

| Property                                           | Pattern | Type        | Deprecated | Definition | Title/Description                                                            |
| -------------------------------------------------- | ------- | ----------- | ---------- | ---------- | ---------------------------------------------------------------------------- |
| - [multithreading](#general_multithreading )       | No      | boolean     | No         | -          | Use multiple threads to detect values                                        |
| - [thread](#general_thread )                       | No      | boolean     | No         | -          | Alias of multithreading                                                      |
| - [escapeBedrock](#general_escapeBedrock )         | No      | boolean     | No         | -          | On Bedrock Linux, whether to escape the bedrock jail                         |
| - [playerName](#general_playerName )               | No      | string      | No         | -          | The name of the player to use for module Media and Player. Linux only        |
| - [dsForceDrm](#general_dsForceDrm )               | No      | Combination | No         | -          | Force display detection to use DRM. Linux only                               |
| - [wmiTimeout](#general_wmiTimeout )               | No      | integer     | No         | -          | Set the timeout (ms) for WMI queries, \`-1\` for no timeout. Windows only    |
| - [processingTimeout](#general_processingTimeout ) | No      | integer     | No         | -          | Set the timeout (ms) when waiting for child processes, \`-1\` for no timeout |

### <a name="general_multithreading"></a>3.1. Property `JSON config > general > multithreading`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Use multiple threads to detect values

### <a name="general_thread"></a>3.2. Property `JSON config > general > thread`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Alias of multithreading

### <a name="general_escapeBedrock"></a>3.3. Property `JSON config > general > escapeBedrock`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** On Bedrock Linux, whether to escape the bedrock jail

### <a name="general_playerName"></a>3.4. Property `JSON config > general > playerName`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** The name of the player to use for module Media and Player. Linux only

### <a name="general_dsForceDrm"></a>3.5. Property `JSON config > general > dsForceDrm`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |
| **Default**               | `false`                                                                   |

**Description:** Force display detection to use DRM. Linux only

| One of(Option)                         |
| -------------------------------------- |
| [item 0](#general_dsForceDrm_oneOf_i0) |
| [item 1](#general_dsForceDrm_oneOf_i1) |
| [item 2](#general_dsForceDrm_oneOf_i2) |

#### <a name="general_dsForceDrm_oneOf_i0"></a>3.5.1. Property `JSON config > general > dsForceDrm > oneOf > item 0`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Try `wayland`, then `x11`, then `drm`

Specific value: `false`

#### <a name="general_dsForceDrm_oneOf_i1"></a>3.5.2. Property `JSON config > general > dsForceDrm > oneOf > item 1`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Use `/sys/class/drm` only.

Specific value: `"sysfs-only"`

#### <a name="general_dsForceDrm_oneOf_i2"></a>3.5.3. Property `JSON config > general > dsForceDrm > oneOf > item 2`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Try `libdrm` first, then `sysfs` if libdrm failed

Specific value: `true`

### <a name="general_wmiTimeout"></a>3.6. Property `JSON config > general > wmiTimeout`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `5000`    |

**Description:** Set the timeout (ms) for WMI queries, `-1` for no timeout. Windows only

### <a name="general_processingTimeout"></a>3.7. Property `JSON config > general > processingTimeout`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `1000`    |

**Description:** Set the timeout (ms) when waiting for child processes, `-1` for no timeout

## <a name="display"></a>4. Property `JSON config > display`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Configure how things to be displayed

| Property                                       | Pattern | Type        | Deprecated | Definition | Title/Description                                                                        |
| ---------------------------------------------- | ------- | ----------- | ---------- | ---------- | ---------------------------------------------------------------------------------------- |
| - [stat](#display_stat )                       | No      | boolean     | No         | -          | Show time usage (in ms) for individual modules                                           |
| - [pipe](#display_pipe )                       | No      | boolean     | No         | -          | Whether to enable pipe mode (disable logo and all escape sequences)                      |
| - [showErrors](#display_showErrors )           | No      | boolean     | No         | -          | Print occurring errors to the console. False to ignore errored modules                   |
| - [disableLinewrap](#display_disableLinewrap ) | No      | boolean     | No         | -          | Whether to disable line wrap during the run                                              |
| - [hideCursor](#display_hideCursor )           | No      | boolean     | No         | -          | Whether to hide the cursor during the run                                                |
| - [separator](#display_separator )             | No      | string      | No         | -          | Set the separator between key and value                                                  |
| - [color](#display_color )                     | No      | Combination | No         | -          | Set the color of the keys and title                                                      |
| - [brightColor](#display_brightColor )         | No      | boolean     | No         | -          | Set if the keys, title and ASCII logo should be printed in bright color                  |
| - [keyWidth](#display_keyWidth )               | No      | integer     | No         | -          | Align the width of keys to number of characters, 0 to disable                            |
| - [binaryPrefix](#display_binaryPrefix )       | No      | Combination | No         | -          | Set the binary prefix to used when printing bytes                                        |
| - [size](#display_size )                       | No      | object      | No         | -          | Set how a size value should be displayed                                                 |
| - [temp](#display_temp )                       | No      | object      | No         | -          | Set how temperature values should be displayed                                           |
| - [bar](#display_bar )                         | No      | object      | No         | -          | Set the bar configuration                                                                |
| - [percent](#display_percent )                 | No      | object      | No         | -          | Set how a percentage value should be displayed                                           |
| - [noBuffer](#display_noBuffer )               | No      | boolean     | No         | -          | Whether to disable the stdout application buffer                                         |
| - [tsVersion](#display_tsVersion )             | No      | boolean     | No         | -          | Whether to detect and display the version of terminal and shell. Mainly for benchmarking |

### <a name="display_stat"></a>4.1. Property `JSON config > display > stat`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show time usage (in ms) for individual modules

### <a name="display_pipe"></a>4.2. Property `JSON config > display > pipe`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Whether to enable pipe mode (disable logo and all escape sequences)

### <a name="display_showErrors"></a>4.3. Property `JSON config > display > showErrors`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Print occurring errors to the console. False to ignore errored modules

### <a name="display_disableLinewrap"></a>4.4. Property `JSON config > display > disableLinewrap`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Whether to disable line wrap during the run

### <a name="display_hideCursor"></a>4.5. Property `JSON config > display > hideCursor`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Whether to hide the cursor during the run

### <a name="display_separator"></a>4.6. Property `JSON config > display > separator`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `": "`   |

**Description:** Set the separator between key and value

### <a name="display_color"></a>4.7. Property `JSON config > display > color`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

**Description:** Set the color of the keys and title

| One of(Option)                    |
| --------------------------------- |
| [colors](#display_color_oneOf_i0) |
| [item 1](#display_color_oneOf_i1) |

#### <a name="display_color_oneOf_i0"></a>4.7.1. Property `JSON config > display > color > oneOf > colors`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set the both color of the keys and title

#### <a name="display_color_oneOf_i1"></a>4.7.2. Property `JSON config > display > color > oneOf > item 1`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                          | Pattern | Type             | Deprecated | Definition                           | Title/Description                        |
| ------------------------------------------------- | ------- | ---------------- | ---------- | ------------------------------------ | ---------------------------------------- |
| - [keys](#display_color_oneOf_i1_keys )           | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set the color of the keys                |
| - [title](#display_color_oneOf_i1_title )         | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set the color of the title               |
| - [output](#display_color_oneOf_i1_output )       | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set the color of the module output       |
| - [separator](#display_color_oneOf_i1_separator ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set the color of the key-value separator |

##### <a name="display_color_oneOf_i1_keys"></a>4.7.2.1. Property `JSON config > display > color > oneOf > item 1 > keys`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set the color of the keys

##### <a name="display_color_oneOf_i1_title"></a>4.7.2.2. Property `JSON config > display > color > oneOf > item 1 > title`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set the color of the title

##### <a name="display_color_oneOf_i1_output"></a>4.7.2.3. Property `JSON config > display > color > oneOf > item 1 > output`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set the color of the module output

##### <a name="display_color_oneOf_i1_separator"></a>4.7.2.4. Property `JSON config > display > color > oneOf > item 1 > separator`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set the color of the key-value separator

### <a name="display_brightColor"></a>4.8. Property `JSON config > display > brightColor`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Set if the keys, title and ASCII logo should be printed in bright color

### <a name="display_keyWidth"></a>4.9. Property `JSON config > display > keyWidth`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `0`       |

**Description:** Align the width of keys to number of characters, 0 to disable

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

### <a name="display_binaryPrefix"></a>4.10. Property `JSON config > display > binaryPrefix`

|              |             |
| ------------ | ----------- |
| **Type**     | `combining` |
| **Required** | No          |

**Description:** Set the binary prefix to used when printing bytes

| One of(Option)                           |
| ---------------------------------------- |
| [item 0](#display_binaryPrefix_oneOf_i0) |
| [item 1](#display_binaryPrefix_oneOf_i1) |
| [item 2](#display_binaryPrefix_oneOf_i2) |

#### <a name="display_binaryPrefix_oneOf_i0"></a>4.10.1. Property `JSON config > display > binaryPrefix > oneOf > item 0`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** 1024 Bytes = 1 KiB, 1024 KiB = 1 MiB, ... (standard)

Specific value: `"iec"`

#### <a name="display_binaryPrefix_oneOf_i1"></a>4.10.2. Property `JSON config > display > binaryPrefix > oneOf > item 1`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** 1000 Bytes = 1 KB, 1000 KB = 1 MB, ...

Specific value: `"si"`

#### <a name="display_binaryPrefix_oneOf_i2"></a>4.10.3. Property `JSON config > display > binaryPrefix > oneOf > item 2`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** 1024 Bytes = 1 kB, 1024 K = 1 MB, ...

Specific value: `"jedec"`

### <a name="display_size"></a>4.11. Property `JSON config > display > size`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set how a size value should be displayed

| Property                                | Pattern | Type             | Deprecated | Definition | Title/Description                                                              |
| --------------------------------------- | ------- | ---------------- | ---------- | ---------- | ------------------------------------------------------------------------------ |
| - [maxPrefix](#display_size_maxPrefix ) | No      | enum (of string) | No         | -          | Set the largest binary prefix to use when formatting sizes                     |
| - [ndigits](#display_size_ndigits )     | No      | integer          | No         | -          | Set the number of digits to keep after the decimal point when formatting sizes |

#### <a name="display_size_maxPrefix"></a>4.11.1. Property `JSON config > display > size > maxPrefix`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"YB"`             |

**Description:** Set the largest binary prefix to use when formatting sizes

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

#### <a name="display_size_ndigits"></a>4.11.2. Property `JSON config > display > size > ndigits`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `2`       |

**Description:** Set the number of digits to keep after the decimal point when formatting sizes

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

### <a name="display_temp"></a>4.12. Property `JSON config > display > temp`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set how temperature values should be displayed

| Property                            | Pattern | Type             | Deprecated | Definition | Title/Description                                                                           |
| ----------------------------------- | ------- | ---------------- | ---------- | ---------- | ------------------------------------------------------------------------------------------- |
| - [unit](#display_temp_unit )       | No      | enum (of string) | No         | -          | Set the unit of the temperature                                                             |
| - [ndigits](#display_temp_ndigits ) | No      | integer          | No         | -          | Set the number of digits to keep after the decimal point when formatting temperature values |
| - [color](#display_temp_color )     | No      | object           | No         | -          | Set color used in different states of temperature values                                    |

#### <a name="display_temp_unit"></a>4.12.1. Property `JSON config > display > temp > unit`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"C"`              |

**Description:** Set the unit of the temperature

Must be one of:
* "CELSIUS"
* "C"
* "FAHRENHEIT"
* "F"
* "KELVIN"
* "K"

#### <a name="display_temp_ndigits"></a>4.12.2. Property `JSON config > display > temp > ndigits`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `1`       |

**Description:** Set the number of digits to keep after the decimal point when formatting temperature values

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

#### <a name="display_temp_color"></a>4.12.3. Property `JSON config > display > temp > color`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set color used in different states of temperature values

| Property                                | Pattern | Type             | Deprecated | Definition                           | Title/Description          |
| --------------------------------------- | ------- | ---------------- | ---------- | ------------------------------------ | -------------------------- |
| - [green](#display_temp_color_green )   | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in green state  |
| - [yellow](#display_temp_color_yellow ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in yellow state |
| - [red](#display_temp_color_red )       | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in red state    |

##### <a name="display_temp_color_green"></a>4.12.3.1. Property `JSON config > display > temp > color > green`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in green state

##### <a name="display_temp_color_yellow"></a>4.12.3.2. Property `JSON config > display > temp > color > yellow`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in yellow state

##### <a name="display_temp_color_red"></a>4.12.3.3. Property `JSON config > display > temp > color > red`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in red state

### <a name="display_bar"></a>4.13. Property `JSON config > display > bar`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set the bar configuration

| Property                                   | Pattern | Type    | Deprecated | Definition | Title/Description                                 |
| ------------------------------------------ | ------- | ------- | ---------- | ---------- | ------------------------------------------------- |
| - [charElapsed](#display_bar_charElapsed ) | No      | string  | No         | -          | Set the character to use in elapsed part          |
| - [charTotal](#display_bar_charTotal )     | No      | string  | No         | -          | Set the character to use in total part            |
| - [borderLeft](#display_bar_borderLeft )   | No      | string  | No         | -          | Set the string to use at left border              |
| - [borderRight](#display_bar_borderRight ) | No      | string  | No         | -          | Set the string to use at right border             |
| - [width](#display_bar_width )             | No      | integer | No         | -          | Set the width of the bar, in number of characters |

#### <a name="display_bar_charElapsed"></a>4.13.1. Property `JSON config > display > bar > charElapsed`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `"■"`    |

**Description:** Set the character to use in elapsed part

#### <a name="display_bar_charTotal"></a>4.13.2. Property `JSON config > display > bar > charTotal`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `"-"`    |

**Description:** Set the character to use in total part

#### <a name="display_bar_borderLeft"></a>4.13.3. Property `JSON config > display > bar > borderLeft`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `"[ "`   |

**Description:** Set the string to use at left border

#### <a name="display_bar_borderRight"></a>4.13.4. Property `JSON config > display > bar > borderRight`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `" ]"`   |

**Description:** Set the string to use at right border

#### <a name="display_bar_width"></a>4.13.5. Property `JSON config > display > bar > width`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `10`      |

**Description:** Set the width of the bar, in number of characters

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

### <a name="display_percent"></a>4.14. Property `JSON config > display > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set how a percentage value should be displayed

| Property                               | Pattern | Type   | Deprecated | Definition | Title/Description                                                                                                                                       |
| -------------------------------------- | ------- | ------ | ---------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - [type](#display_percent_type )       | No      | number | No         | -          | Set the percentage output type. 1 for percentage number, 2 for multi-color bar, 3 for both, 6 for bar only, 9 for colored number, 10 for monochrome bar |
| - [ndigits](#display_percent_ndigits ) | No      | number | No         | -          | Set the number of digits to keep after the decimal point when formatting percentage numbers                                                             |
| - [color](#display_percent_color )     | No      | object | No         | -          | Set color used in different states of percentage bars and numbers                                                                                       |

#### <a name="display_percent_type"></a>4.14.1. Property `JSON config > display > percent > type`

|              |          |
| ------------ | -------- |
| **Type**     | `number` |
| **Required** | No       |
| **Default**  | `9`      |

**Description:** Set the percentage output type. 1 for percentage number, 2 for multi-color bar, 3 for both, 6 for bar only, 9 for colored number, 10 for monochrome bar

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 255 |

#### <a name="display_percent_ndigits"></a>4.14.2. Property `JSON config > display > percent > ndigits`

|              |          |
| ------------ | -------- |
| **Type**     | `number` |
| **Required** | No       |
| **Default**  | `0`      |

**Description:** Set the number of digits to keep after the decimal point when formatting percentage numbers

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

#### <a name="display_percent_color"></a>4.14.3. Property `JSON config > display > percent > color`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set color used in different states of percentage bars and numbers

| Property                                   | Pattern | Type             | Deprecated | Definition                           | Title/Description          |
| ------------------------------------------ | ------- | ---------------- | ---------- | ------------------------------------ | -------------------------- |
| - [green](#display_percent_color_green )   | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in green state  |
| - [yellow](#display_percent_color_yellow ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in yellow state |
| - [red](#display_percent_color_red )       | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Color used in red state    |

##### <a name="display_percent_color_green"></a>4.14.3.1. Property `JSON config > display > percent > color > green`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in green state

##### <a name="display_percent_color_yellow"></a>4.14.3.2. Property `JSON config > display > percent > color > yellow`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in yellow state

##### <a name="display_percent_color_red"></a>4.14.3.3. Property `JSON config > display > percent > color > red`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Color used in red state

### <a name="display_noBuffer"></a>4.15. Property `JSON config > display > noBuffer`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Whether to disable the stdout application buffer

### <a name="display_tsVersion"></a>4.16. Property `JSON config > display > tsVersion`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Whether to detect and display the version of terminal and shell. Mainly for benchmarking

## <a name="library"></a>5. Property `JSON config > library`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set the path of a library to load

| Property                               | Pattern | Type   | Deprecated | Definition | Title/Description                                                                                                                                                                              |
| -------------------------------------- | ------- | ------ | ---------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - [pci](#library_pci )                 | No      | string | No         | -          | GPU output (Linux and FreeBSD)                                                                                                                                                                 |
| - [vulkan](#library_vulkan )           | No      | string | No         | -          | Vulkan module & fallback for GPU output                                                                                                                                                        |
| - [freetype](#library_freetype )       | No      | string | No         | -          | Used for Termux font detection (Android)                                                                                                                                                       |
| - [wayland](#library_wayland )         | No      | string | No         | -          | Better display performance and output in wayland sessions.<br />Supports different refresh rates per monitor.<br />(Linux, FreeBSD)                                                            |
| - [xcbRandr](#library_xcbRandr )       | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [xcb](#library_xcb )                 | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [xrandr](#library_xrandr )           | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [x11](#library_x11 )                 | No      | string | No         | -          | X11 sessions for better display detection and faster WM detection.<br />The *randr ones provide multi monitor support The libxcb* ones usually have better performance.<br /> (Linux, FreeBSD) |
| - [drm](#library_drm )                 | No      | string | No         | -          | Used for fast resolution and refresh rate detection (Linux)                                                                                                                                    |
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

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** GPU output (Linux and FreeBSD)

### <a name="library_vulkan"></a>5.2. Property `JSON config > library > vulkan`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Vulkan module & fallback for GPU output

### <a name="library_freetype"></a>5.3. Property `JSON config > library > freetype`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Used for Termux font detection (Android)

### <a name="library_wayland"></a>5.4. Property `JSON config > library > wayland`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Better display performance and output in wayland sessions.
Supports different refresh rates per monitor.
(Linux, FreeBSD)

### <a name="library_xcbRandr"></a>5.5. Property `JSON config > library > xcbRandr`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

### <a name="library_xcb"></a>5.6. Property `JSON config > library > xcb`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

### <a name="library_xrandr"></a>5.7. Property `JSON config > library > xrandr`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

### <a name="library_x11"></a>5.8. Property `JSON config > library > x11`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** X11 sessions for better display detection and faster WM detection.
The *randr ones provide multi monitor support The libxcb* ones usually have better performance.
 (Linux, FreeBSD)

### <a name="library_drm"></a>5.9. Property `JSON config > library > drm`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Used for fast resolution and refresh rate detection (Linux)

### <a name="library_gio"></a>5.10. Property `JSON config > library > gio`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed for values that are only stored GSettings (Linux, FreeBSD)

### <a name="library_dconf"></a>5.11. Property `JSON config > library > dconf`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed for values that are only stored in DConf + Fallback for GSettings (Linux, FreeBSD)

### <a name="library_dbus"></a>5.12. Property `JSON config > library > dbus`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Bluetooth, Player & Media detection (Linux, FreeBSD)

### <a name="library_xfconf"></a>5.13. Property `JSON config > library > xfconf`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed for XFWM theme and XFCE Terminal font (Linux, FreeBSD)

### <a name="library_sqlite3"></a>5.14. Property `JSON config > library > sqlite3`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed for pkg & rpm package count (Linux, FreeBSD)

### <a name="library_rpm"></a>5.15. Property `JSON config > library > rpm`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Slower fallback for rpm package count. Needed on openSUSE & old CentOS / Redhat. (Linux)

### <a name="library_imagemagick"></a>5.16. Property `JSON config > library > imagemagick`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Images in terminal using sixel or kitty graphics protocol (Linux, FreeBSD, macOS)

### <a name="library_z"></a>5.17. Property `JSON config > library > z`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Libz. Faster image output when using kitty graphics protocol (Linux, FreeBSD, macOS)

### <a name="library_chafa"></a>5.18. Property `JSON config > library > chafa`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Image output as ascii art (Linux, FreeBSD, macOS)

### <a name="library_egl"></a>5.19. Property `JSON config > library > egl`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

### <a name="library_glx"></a>5.20. Property `JSON config > library > glx`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

### <a name="library_osmesa"></a>5.21. Property `JSON config > library > osmesa`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Needed by the OpenGL module for gl context creation (Linux, FreeBSD)

### <a name="library_opencl"></a>5.22. Property `JSON config > library > opencl`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** OpenCL module (Linux, FreeBSD, Windows)

### <a name="library_pulse"></a>5.23. Property `JSON config > library > pulse`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Pulseaudio. Used for Sound detection (Linux, FreeBSD)

### <a name="library_nm"></a>5.24. Property `JSON config > library > nm`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** NetworkManager. Used for Wifi detection (Linux)

### <a name="library_ddcutil"></a>5.25. Property `JSON config > library > ddcutil`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Used for brightness detection of external displays (Linux)

## <a name="modules"></a>6. Property `JSON config > modules`

|              |         |
| ------------ | ------- |
| **Type**     | `array` |
| **Required** | No      |

**Description:** Fastfetch modules to run

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

| Any of(Option)                    |
| --------------------------------- |
| [item 0](#modules_items_anyOf_i0) |
| [item 1](#modules_items_anyOf_i1) |

#### <a name="modules_items_anyOf_i0"></a>6.1.1. Property `JSON config > modules > modules items > anyOf > item 0`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

**Description:** Run module with default configurations

Must be one of:
* "battery"
* "bios"
* "bluetooth"
* "board"
* "bootmgr"
* "break"
* "brightness"
* "camera"
* "chassis"
* "cpu"
* "cpucache"
* "cpuusage"
* "command"
* "colors"
* "cursor"
* "datetime"
* "display"
* "disk"
* "diskio"
* "de"
* "dns"
* "editor"
* "font"
* "gamepad"
* "gpu"
* "host"
* "icons"
* "initsystem"
* "kernel"
* "lm"
* "loadavg"
* "locale"
* "localip"
* "media"
* "memory"
* "monitor"
* "netio"
* "opencl"
* "opengl"
* "os"
* "packages"
* "physicaldisk"
* "physicalmemory"
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
* "terminaltheme"
* "title"
* "theme"
* "uptime"
* "users"
* "version"
* "vulkan"
* "wallpaper"
* "weather"
* "wm"
* "wifi"
* "wmtheme"

#### <a name="modules_items_anyOf_i1"></a>6.1.2. Property `JSON config > modules > modules items > anyOf > item 1`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

**Description:** Run module with custom configurations

| Property                                | Pattern | Type   | Deprecated | Definition | Title/Description |
| --------------------------------------- | ------- | ------ | ---------- | ---------- | ----------------- |
| + [type](#modules_items_anyOf_i1_type ) | No      | string | No         | -          | -                 |

| One of(Option)                                     |
| -------------------------------------------------- |
| [Break](#modules_items_anyOf_i1_oneOf_i0)          |
| [module](#modules_items_anyOf_i1_oneOf_i1)         |
| [Battery](#modules_items_anyOf_i1_oneOf_i2)        |
| [Bluetooth](#modules_items_anyOf_i1_oneOf_i3)      |
| [Brightness](#modules_items_anyOf_i1_oneOf_i4)     |
| [Chassis](#modules_items_anyOf_i1_oneOf_i5)        |
| [CPU](#modules_items_anyOf_i1_oneOf_i6)            |
| [CPU Usage](#modules_items_anyOf_i1_oneOf_i7)      |
| [Colors](#modules_items_anyOf_i1_oneOf_i8)         |
| [Command](#modules_items_anyOf_i1_oneOf_i9)        |
| [Custom](#modules_items_anyOf_i1_oneOf_i10)        |
| [Display](#modules_items_anyOf_i1_oneOf_i11)       |
| [Disk](#modules_items_anyOf_i1_oneOf_i12)          |
| [DiskIO](#modules_items_anyOf_i1_oneOf_i13)        |
| [DE](#modules_items_anyOf_i1_oneOf_i14)            |
| [DNS](#modules_items_anyOf_i1_oneOf_i15)           |
| [Gamepad](#modules_items_anyOf_i1_oneOf_i16)       |
| [GPU](#modules_items_anyOf_i1_oneOf_i17)           |
| [Local IP](#modules_items_anyOf_i1_oneOf_i18)      |
| [Memory](#modules_items_anyOf_i1_oneOf_i19)        |
| [Loadavg](#modules_items_anyOf_i1_oneOf_i20)       |
| [NetIO](#modules_items_anyOf_i1_oneOf_i21)         |
| [OpenGL](#modules_items_anyOf_i1_oneOf_i22)        |
| [Packages](#modules_items_anyOf_i1_oneOf_i23)      |
| [Physical Disk](#modules_items_anyOf_i1_oneOf_i24) |
| [Public IP](#modules_items_anyOf_i1_oneOf_i25)     |
| [Separator](#modules_items_anyOf_i1_oneOf_i26)     |
| [Sound](#modules_items_anyOf_i1_oneOf_i27)         |
| [Swap](#modules_items_anyOf_i1_oneOf_i28)          |
| [Title](#modules_items_anyOf_i1_oneOf_i29)         |
| [Users](#modules_items_anyOf_i1_oneOf_i30)         |
| [Weather](#modules_items_anyOf_i1_oneOf_i31)       |
| [WM](#modules_items_anyOf_i1_oneOf_i32)            |

##### <a name="modules_items_anyOf_i1_oneOf_i0"></a>6.1.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Break`

**Title:** Break

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                         | Pattern | Type  | Deprecated | Definition | Title/Description  |
| ------------------------------------------------ | ------- | ----- | ---------- | ---------- | ------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i0_type ) | No      | const | No         | -          | Print a empty line |

###### <a name="modules_items_anyOf_i1_oneOf_i0_type"></a>6.1.2.1.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Break > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print a empty line

Specific value: `"break"`

##### <a name="modules_items_anyOf_i1_oneOf_i1"></a>6.1.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module`

**Title:** module

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** No additional properties

| Property                                                       | Pattern | Type             | Deprecated | Definition             | Title/Description                                                        |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | ---------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i1_type )               | No      | Combination      | No         | -                      | -                                                                        |
| - [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | No      | string           | No         | In #/$defs/key         | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | No      | enum (of string) | No         | In #/$defs/keyColor    | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | No      | enum (of string) | No         | In #/$defs/outputColor | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | No      | integer          | No         | In #/$defs/keyWidth    | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i1_format )           | No      | string           | No         | In #/$defs/format      | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i1_type"></a>6.1.2.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |

| Any of(Option)                                             |
| ---------------------------------------------------------- |
| [item 0](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i0)   |
| [item 1](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i1)   |
| [item 2](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i2)   |
| [item 3](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i3)   |
| [item 4](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i4)   |
| [item 5](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i5)   |
| [item 6](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i6)   |
| [item 7](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i7)   |
| [item 8](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i8)   |
| [item 9](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i9)   |
| [item 10](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i10) |
| [item 11](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i11) |
| [item 12](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i12) |
| [item 13](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i13) |
| [item 14](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i14) |
| [item 15](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i15) |
| [item 16](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i16) |
| [item 17](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i17) |
| [item 18](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i18) |
| [item 19](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i19) |
| [item 20](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i20) |
| [item 21](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i21) |
| [item 22](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i22) |
| [item 23](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i23) |
| [item 24](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i24) |
| [item 25](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i25) |
| [item 26](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i26) |
| [item 27](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i27) |
| [item 28](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i28) |
| [item 29](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i29) |
| [item 30](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i30) |
| [item 31](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i31) |
| [item 32](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i32) |
| [item 33](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i33) |
| [item 34](#modules_items_anyOf_i1_oneOf_i1_type_anyOf_i34) |

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i0"></a>6.1.2.2.1.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 0`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print information of 1st-stage bootloader (name, version, release date, etc)

Specific value: `"bios"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i1"></a>6.1.2.2.1.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 1`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print motherboard name and other info

Specific value: `"board"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i2"></a>6.1.2.2.1.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 2`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print information of 2nd-stage bootloader (name, firmware, etc)

Specific value: `"bootmgr"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i3"></a>6.1.2.2.1.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 3`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print available cameras

Specific value: `"camera"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i4"></a>6.1.2.2.1.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 4`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print CPU cache sizes

Specific value: `"cpucache"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i5"></a>6.1.2.2.1.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 5`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print cursor style name

Specific value: `"cursor"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i6"></a>6.1.2.2.1.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 6`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current date and time

Specific value: `"datetime"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i7"></a>6.1.2.2.1.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 7`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print information of the default editor ($VISUAL or $EDITOR)

Specific value: `"editor"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i8"></a>6.1.2.2.1.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 8`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system font name

Specific value: `"font"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i9"></a>6.1.2.2.1.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 9`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print product name of your computer

Specific value: `"host"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i10"></a>6.1.2.2.1.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 10`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print icon style name

Specific value: `"icons"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i11"></a>6.1.2.2.1.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 11`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print init system (pid 1) name and version

Specific value: `"initsystem"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i12"></a>6.1.2.2.1.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 12`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system kernel version

Specific value: `"kernel"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i13"></a>6.1.2.2.1.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 13`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print login manager (desktop manager) name and version

Specific value: `"lm"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i14"></a>6.1.2.2.1.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 14`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system locale name

Specific value: `"locale"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i15"></a>6.1.2.2.1.16. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 15`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print playing song name

Specific value: `"media"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i16"></a>6.1.2.2.1.17. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 16`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print connected physical monitor information

Specific value: `"monitor"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i17"></a>6.1.2.2.1.18. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 17`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print highest OpenCL version supported by the GPU

Specific value: `"opencl"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i18"></a>6.1.2.2.1.19. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 18`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print operating system name and version

Specific value: `"os"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i19"></a>6.1.2.2.1.20. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 19`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system physical memory devices

Specific value: `"physicalmemory"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i20"></a>6.1.2.2.1.21. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 20`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print music player name

Specific value: `"player"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i21"></a>6.1.2.2.1.22. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 21`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print power adapter name and charging watts

Specific value: `"poweradapter"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i22"></a>6.1.2.2.1.23. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 22`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Count running processes

Specific value: `"processes"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i23"></a>6.1.2.2.1.24. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 23`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current shell name and version

Specific value: `"shell"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i24"></a>6.1.2.2.1.25. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 24`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current terminal name and version

Specific value: `"terminal"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i25"></a>6.1.2.2.1.26. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 25`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print font name and size used by current terminal

Specific value: `"terminalfont"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i26"></a>6.1.2.2.1.27. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 26`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current terminal size

Specific value: `"terminalsize"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i27"></a>6.1.2.2.1.28. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 27`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current terminal theme (foreground and background colors)

Specific value: `"terminaltheme"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i28"></a>6.1.2.2.1.29. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 28`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current theme of desktop environment

Specific value: `"theme"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i29"></a>6.1.2.2.1.30. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 29`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print how long system has been running

Specific value: `"uptime"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i30"></a>6.1.2.2.1.31. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 30`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print Fastfetch version

Specific value: `"version"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i31"></a>6.1.2.2.1.32. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 31`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print highest Vulkan version supported by the GPU

Specific value: `"vulkan"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i32"></a>6.1.2.2.1.33. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 32`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print image file path of current wallpaper

Specific value: `"wallpaper"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i33"></a>6.1.2.2.1.34. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 33`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print connected Wi-Fi info (SSID, connection and security protocol)

Specific value: `"wifi"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_type_anyOf_i34"></a>6.1.2.2.1.35. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > type > anyOf > item 34`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current theme of window manager

Specific value: `"wmtheme"`

###### <a name="modules_items_anyOf_i1_oneOf_i1_key"></a>6.1.2.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > key`

|                |             |
| -------------- | ----------- |
| **Type**       | `string`    |
| **Required**   | No          |
| **Defined in** | #/$defs/key |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i1_keyColor"></a>6.1.2.2.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > keyColor`

|                |                    |
| -------------- | ------------------ |
| **Type**       | `enum (of string)` |
| **Required**   | No                 |
| **Defined in** | #/$defs/keyColor   |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i1_outputColor"></a>6.1.2.2.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > outputColor`

|                |                     |
| -------------- | ------------------- |
| **Type**       | `enum (of string)`  |
| **Required**   | No                  |
| **Defined in** | #/$defs/outputColor |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i1_keyWidth"></a>6.1.2.2.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > keyWidth`

|                |                  |
| -------------- | ---------------- |
| **Type**       | `integer`        |
| **Required**   | No               |
| **Default**    | `0`              |
| **Defined in** | #/$defs/keyWidth |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

###### <a name="modules_items_anyOf_i1_oneOf_i1_format"></a>6.1.2.2.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > module > format`

|                |                |
| -------------- | -------------- |
| **Type**       | `string`       |
| **Required**   | No             |
| **Defined in** | #/$defs/format |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i2"></a>6.1.2.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery`

**Title:** Battery

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                                              |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i2_type )               | No      | const            | No         | -                                                                    | Print battery capacity, status, etc                                                                                            |
| - [useSetupApi](#modules_items_anyOf_i1_oneOf_i2_useSetupApi ) | No      | boolean          | No         | -                                                                    | Set if \`SetupAPI\` should be used on Windows to detect battery info, which supports multi batteries, but slower. Windows only |
| - [temp](#modules_items_anyOf_i1_oneOf_i2_temp )               | No      | object           | No         | In #/$defs/temperature                                               | Detect and display temperature if supported                                                                                    |
| - [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | No      | object           | No         | In #/$defs/percent                                                   | Threshold of percentage colors                                                                                                 |
| - [key](#modules_items_anyOf_i1_oneOf_i2_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                                              |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i2_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                                              |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i2_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                                                         |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i2_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                                                     |
| - [format](#modules_items_anyOf_i1_oneOf_i2_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                                                       |

###### <a name="modules_items_anyOf_i1_oneOf_i2_type"></a>6.1.2.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print battery capacity, status, etc

Specific value: `"battery"`

###### <a name="modules_items_anyOf_i1_oneOf_i2_useSetupApi"></a>6.1.2.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > useSetupApi`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if `SetupAPI` should be used on Windows to detect battery info, which supports multi batteries, but slower. Windows only

###### <a name="modules_items_anyOf_i1_oneOf_i2_temp"></a>6.1.2.3.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |
| **Defined in**            | #/$defs/temperature                                                       |

**Description:** Detect and display temperature if supported

| One of(Option)                                           |
| -------------------------------------------------------- |
| [item 0](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i0) |
| [item 1](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1) |

###### <a name="modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i0"></a>6.1.2.3.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 0`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

###### <a name="modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1"></a>6.1.2.3.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                           | Pattern | Type    | Deprecated | Definition | Title/Description                                                                                                                       |
| ------------------------------------------------------------------ | ------- | ------- | ---------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| - [green](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_green )   | No      | integer | No         | -          | Value (in celsius) less then green will be shown in green                                                                               |
| - [yellow](#modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_yellow ) | No      | integer | No         | -          | Value (in celsius) greater than green and less then yellow will be shown in yellow.<br />Value greater than yellow will be shown in red |

###### <a name="modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_green"></a>6.1.2.3.3.2.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1 > green`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Value (in celsius) less then green will be shown in green

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 100 |

###### <a name="modules_items_anyOf_i1_oneOf_i2_temp_oneOf_i1_yellow"></a>6.1.2.3.3.2.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > temp > oneOf > item 1 > yellow`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Value (in celsius) greater than green and less then yellow will be shown in yellow.
Value greater than yellow will be shown in red

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 100 |

###### <a name="modules_items_anyOf_i1_oneOf_i2_percent"></a>6.1.2.3.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Defined in**            | #/$defs/percent                                         |

**Description:** Threshold of percentage colors

| Property                                                     | Pattern | Type    | Deprecated | Definition | Title/Description                                                                                                          |
| ------------------------------------------------------------ | ------- | ------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------------------------- |
| - [green](#modules_items_anyOf_i1_oneOf_i2_percent_green )   | No      | integer | No         | -          | Value less then green will be shown in green                                                                               |
| - [yellow](#modules_items_anyOf_i1_oneOf_i2_percent_yellow ) | No      | integer | No         | -          | Value greater than green and less then yellow will be shown in yellow.<br />Value greater than yellow will be shown in red |

###### <a name="modules_items_anyOf_i1_oneOf_i2_percent_green"></a>6.1.2.3.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent > green`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Value less then green will be shown in green

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 100 |

###### <a name="modules_items_anyOf_i1_oneOf_i2_percent_yellow"></a>6.1.2.3.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > percent > yellow`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

**Description:** Value greater than green and less then yellow will be shown in yellow.
Value greater than yellow will be shown in red

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 100 |

###### <a name="modules_items_anyOf_i1_oneOf_i2_key"></a>6.1.2.3.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i2_keyColor"></a>6.1.2.3.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i2_outputColor"></a>6.1.2.3.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i2_keyWidth"></a>6.1.2.3.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i2_format"></a>6.1.2.3.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Battery > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i3"></a>6.1.2.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth`

**Title:** Bluetooth

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                 | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| ------------------------------------------------------------------------ | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i3_type )                         | No      | const            | No         | -                                                                    | List bluetooth devices                                                   |
| - [showDisconnected](#modules_items_anyOf_i1_oneOf_i3_showDisconnected ) | No      | boolean          | No         | -                                                                    | Set if disconnected bluetooth devices should be printed                  |
| - [percent](#modules_items_anyOf_i1_oneOf_i3_percent )                   | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i3_key )                           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i3_keyColor )                 | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i3_outputColor )           | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i3_keyWidth )                 | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i3_format )                     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i3_type"></a>6.1.2.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** List bluetooth devices

Specific value: `"bluetooth"`

###### <a name="modules_items_anyOf_i1_oneOf_i3_showDisconnected"></a>6.1.2.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > showDisconnected`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if disconnected bluetooth devices should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i3_percent"></a>6.1.2.4.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i3_key"></a>6.1.2.4.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i3_keyColor"></a>6.1.2.4.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i3_outputColor"></a>6.1.2.4.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i3_keyWidth"></a>6.1.2.4.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i3_format"></a>6.1.2.4.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Bluetooth > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i4"></a>6.1.2.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness`

**Title:** Brightness

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                                                                    |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i4_type )               | No      | const            | No         | -                                                                    | Print current brightness level of your monitors                                                                                                      |
| - [percent](#modules_items_anyOf_i1_oneOf_i4_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                                                                                                       |
| - [ddcciSleep](#modules_items_anyOf_i1_oneOf_i4_ddcciSleep )   | No      | integer          | No         | -                                                                    | Set the sleep times (in ms) when sending DDC/CI requests.<br />See <https://www.ddcutil.com/performance_options/#option-sleep-multiplier> for detail |
| - [key](#modules_items_anyOf_i1_oneOf_i4_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                                                                    |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i4_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                                                                    |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i4_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                                                                               |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i4_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                                                                           |
| - [format](#modules_items_anyOf_i1_oneOf_i4_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                                                                             |

###### <a name="modules_items_anyOf_i1_oneOf_i4_type"></a>6.1.2.5.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print current brightness level of your monitors

Specific value: `"brightness"`

###### <a name="modules_items_anyOf_i1_oneOf_i4_percent"></a>6.1.2.5.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i4_ddcciSleep"></a>6.1.2.5.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > ddcciSleep`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `10`      |

**Description:** Set the sleep times (in ms) when sending DDC/CI requests.
See <https://www.ddcutil.com/performance_options/#option-sleep-multiplier> for detail

| Restrictions |          |
| ------------ | -------- |
| **Minimum**  | &ge; 0   |
| **Maximum**  | &le; 400 |

###### <a name="modules_items_anyOf_i1_oneOf_i4_key"></a>6.1.2.5.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i4_keyColor"></a>6.1.2.5.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i4_outputColor"></a>6.1.2.5.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i4_keyWidth"></a>6.1.2.5.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i4_format"></a>6.1.2.5.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Brightness > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i5"></a>6.1.2.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis`

**Title:** Chassis

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i5_type )               | No      | const            | No         | -                                                                    | Print chassis type (desktop, laptop, etc)                                |
| - [key](#modules_items_anyOf_i1_oneOf_i5_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i5_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i5_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i5_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i5_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i5_type"></a>6.1.2.6.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print chassis type (desktop, laptop, etc)

Specific value: `"chassis"`

###### <a name="modules_items_anyOf_i1_oneOf_i5_key"></a>6.1.2.6.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i5_keyColor"></a>6.1.2.6.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i5_outputColor"></a>6.1.2.6.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i5_keyWidth"></a>6.1.2.6.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i5_format"></a>6.1.2.6.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Chassis > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i6"></a>6.1.2.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU`

**Title:** CPU

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                               | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                           |
| ---------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i6_type )                       | No      | const            | No         | -                                                                    | Print CPU name, frequency, etc                                                              |
| - [temp](#modules_items_anyOf_i1_oneOf_i6_temp )                       | No      | object           | No         | Same as [temp](#modules_items_anyOf_i1_oneOf_i2_temp )               | Detect and display temperature if supported                                                 |
| - [freqNdigits](#modules_items_anyOf_i1_oneOf_i6_freqNdigits )         | No      | integer          | No         | -                                                                    | Set the number of digits to keep after the decimal point when printing CPU frequency        |
| - [showPeCoreCount](#modules_items_anyOf_i1_oneOf_i6_showPeCoreCount ) | No      | boolean          | No         | -                                                                    | Detect and display CPU frequency of different core types (eg. Pcore and Ecore) if supported |
| - [key](#modules_items_anyOf_i1_oneOf_i6_key )                         | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                           |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i6_keyColor )               | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                           |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i6_outputColor )         | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                      |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i6_keyWidth )               | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                  |
| - [format](#modules_items_anyOf_i1_oneOf_i6_format )                   | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                    |

###### <a name="modules_items_anyOf_i1_oneOf_i6_type"></a>6.1.2.7.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print CPU name, frequency, etc

Specific value: `"cpu"`

###### <a name="modules_items_anyOf_i1_oneOf_i6_temp"></a>6.1.2.7.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > temp`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |
| **Same definition as**    | [temp](#modules_items_anyOf_i1_oneOf_i2_temp)                             |

**Description:** Detect and display temperature if supported

###### <a name="modules_items_anyOf_i1_oneOf_i6_freqNdigits"></a>6.1.2.7.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > freqNdigits`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `2`       |

**Description:** Set the number of digits to keep after the decimal point when printing CPU frequency

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

###### <a name="modules_items_anyOf_i1_oneOf_i6_showPeCoreCount"></a>6.1.2.7.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > showPeCoreCount`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Detect and display CPU frequency of different core types (eg. Pcore and Ecore) if supported

###### <a name="modules_items_anyOf_i1_oneOf_i6_key"></a>6.1.2.7.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i6_keyColor"></a>6.1.2.7.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i6_outputColor"></a>6.1.2.7.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i6_keyWidth"></a>6.1.2.7.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i6_format"></a>6.1.2.7.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i7"></a>6.1.2.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage`

**Title:** CPU Usage

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                 | Pattern | Type    | Deprecated | Definition                                                   | Title/Description                                                    |
| -------------------------------------------------------- | ------- | ------- | ---------- | ------------------------------------------------------------ | -------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i7_type )         | No      | const   | No         | -                                                            | Print CPU usage. Costs some time to collect data                     |
| - [percent](#modules_items_anyOf_i1_oneOf_i7_percent )   | No      | object  | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent ) | Threshold of percentage colors                                       |
| - [separate](#modules_items_anyOf_i1_oneOf_i7_separate ) | No      | boolean | No         | -                                                            | Display CPU usage per CPU logical core, instead of an average result |

###### <a name="modules_items_anyOf_i1_oneOf_i7_type"></a>6.1.2.8.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print CPU usage. Costs some time to collect data

Specific value: `"cpuusage"`

###### <a name="modules_items_anyOf_i1_oneOf_i7_percent"></a>6.1.2.8.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i7_separate"></a>6.1.2.8.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > CPU Usage > separate`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Display CPU usage per CPU logical core, instead of an average result

##### <a name="modules_items_anyOf_i1_oneOf_i8"></a>6.1.2.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors`

**Title:** Colors

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition                                           | Title/Description                                         |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | ---------------------------------------------------- | --------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i8_type )               | No      | const            | No         | -                                                    | Print some colored blocks                                 |
| - [symbol](#modules_items_anyOf_i1_oneOf_i8_symbol )           | No      | enum (of string) | No         | -                                                    | Set the symbol to use                                     |
| - [paddingLeft](#modules_items_anyOf_i1_oneOf_i8_paddingLeft ) | No      | integer          | No         | -                                                    | Set the number of white spaces to print before the symbol |
| - [block](#modules_items_anyOf_i1_oneOf_i8_block )             | No      | object           | No         | -                                                    | Set behavior of block printing                            |
| - [key](#modules_items_anyOf_i1_oneOf_i8_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key ) | Key of the module                                         |

###### <a name="modules_items_anyOf_i1_oneOf_i8_type"></a>6.1.2.9.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print some colored blocks

Specific value: `"colors"`

###### <a name="modules_items_anyOf_i1_oneOf_i8_symbol"></a>6.1.2.9.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > symbol`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"block"`          |

**Description:** Set the symbol to use

Must be one of:
* "block"
* "circle"
* "diamond"
* "triangle"
* "square"
* "star"

###### <a name="modules_items_anyOf_i1_oneOf_i8_paddingLeft"></a>6.1.2.9.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > paddingLeft`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `0`       |

**Description:** Set the number of white spaces to print before the symbol

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

###### <a name="modules_items_anyOf_i1_oneOf_i8_block"></a>6.1.2.9.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set behavior of block printing

| Property                                                 | Pattern | Type             | Deprecated | Definition | Title/Description                              |
| -------------------------------------------------------- | ------- | ---------------- | ---------- | ---------- | ---------------------------------------------- |
| - [width](#modules_items_anyOf_i1_oneOf_i8_block_width ) | No      | integer          | No         | -          | Set the block width in spaces                  |
| - [range](#modules_items_anyOf_i1_oneOf_i8_block_range ) | No      | array of integer | No         | -          | Set the range of colors in the blocks to print |

###### <a name="modules_items_anyOf_i1_oneOf_i8_block_width"></a>6.1.2.9.4.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > width`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `3`       |

**Description:** Set the block width in spaces

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 1 |

###### <a name="modules_items_anyOf_i1_oneOf_i8_block_range"></a>6.1.2.9.4.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > range`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `array of integer` |
| **Required** | No                 |

**Description:** Set the range of colors in the blocks to print

|                      | Array restrictions |
| -------------------- | ------------------ |
| **Min items**        | 2                  |
| **Max items**        | 2                  |
| **Items unicity**    | False              |
| **Additional items** | False              |
| **Tuple validation** | See below          |

| Each item of this array must be                                   | Description |
| ----------------------------------------------------------------- | ----------- |
| [range items](#modules_items_anyOf_i1_oneOf_i8_block_range_items) | -           |

###### <a name="autogenerated_heading_3"></a>6.1.2.9.4.2.1. JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > block > range > range items

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |

| Restrictions |         |
| ------------ | ------- |
| **Minimum**  | &ge; 0  |
| **Maximum**  | &le; 15 |

###### <a name="modules_items_anyOf_i1_oneOf_i8_key"></a>6.1.2.9.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Colors > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

##### <a name="modules_items_anyOf_i1_oneOf_i9"></a>6.1.2.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command`

**Title:** Command

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                       | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                 |
| -------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i9_type )               | No      | const            | No         | -                                                                    | Running custom shell scripts                                                                      |
| - [shell](#modules_items_anyOf_i1_oneOf_i9_shell )             | No      | string           | No         | -                                                                    | Set the shell program to execute the command text<br />Default: cmd for Windows, /bin/sh for *nix |
| - [text](#modules_items_anyOf_i1_oneOf_i9_text )               | No      | string           | No         | -                                                                    | Set the command text to be executed                                                               |
| - [key](#modules_items_anyOf_i1_oneOf_i9_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                 |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i9_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                 |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i9_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                            |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i9_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                        |
| - [format](#modules_items_anyOf_i1_oneOf_i9_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                          |

###### <a name="modules_items_anyOf_i1_oneOf_i9_type"></a>6.1.2.10.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Running custom shell scripts

Specific value: `"command"`

###### <a name="modules_items_anyOf_i1_oneOf_i9_shell"></a>6.1.2.10.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > shell`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Set the shell program to execute the command text
Default: cmd for Windows, /bin/sh for *nix

###### <a name="modules_items_anyOf_i1_oneOf_i9_text"></a>6.1.2.10.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > text`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Set the command text to be executed

###### <a name="modules_items_anyOf_i1_oneOf_i9_key"></a>6.1.2.10.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i9_keyColor"></a>6.1.2.10.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i9_outputColor"></a>6.1.2.10.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i9_keyWidth"></a>6.1.2.10.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i9_format"></a>6.1.2.10.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Command > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i10"></a>6.1.2.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom`

**Title:** Custom

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                      |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i10_type )               | No      | const            | No         | -                                                                    | Print a custom string, with or without key                             |
| - [key](#modules_items_anyOf_i1_oneOf_i10_key )                 | No      | string           | No         | -                                                                    | Leave empty not to print the key                                       |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i10_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`      |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i10_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\` |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i10_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`             |
| - [format](#modules_items_anyOf_i1_oneOf_i10_format )           | No      | string           | No         | -                                                                    | Text to print                                                          |

###### <a name="modules_items_anyOf_i1_oneOf_i10_type"></a>6.1.2.11.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print a custom string, with or without key

Specific value: `"custom"`

###### <a name="modules_items_anyOf_i1_oneOf_i10_key"></a>6.1.2.11.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > key`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Leave empty not to print the key

###### <a name="modules_items_anyOf_i1_oneOf_i10_keyColor"></a>6.1.2.11.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i10_outputColor"></a>6.1.2.11.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i10_keyWidth"></a>6.1.2.11.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i10_format"></a>6.1.2.11.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Custom > format`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Text to print

##### <a name="modules_items_anyOf_i1_oneOf_i11"></a>6.1.2.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display`

**Title:** Display

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                      | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                              |
| ----------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i11_type )                             | No      | const            | No         | -                                                                    | Print resolutions, refresh rates, etc                                          |
| - [compactType](#modules_items_anyOf_i1_oneOf_i11_compactType )               | No      | enum (of string) | No         | -                                                                    | Set if all displays should be printed in one line                              |
| - [preciseRefreshRate](#modules_items_anyOf_i1_oneOf_i11_preciseRefreshRate ) | No      | boolean          | No         | -                                                                    | Set if decimal refresh rates should not be rounded into integers when printing |
| - [order](#modules_items_anyOf_i1_oneOf_i11_order )                           | No      | enum (of string) | No         | -                                                                    | Set the order should be used when printing                                     |
| - [key](#modules_items_anyOf_i1_oneOf_i11_key )                               | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                              |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i11_keyColor )                     | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`              |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i11_outputColor )               | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`         |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i11_keyWidth )                     | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                     |
| - [format](#modules_items_anyOf_i1_oneOf_i11_format )                         | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail       |

###### <a name="modules_items_anyOf_i1_oneOf_i11_type"></a>6.1.2.12.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print resolutions, refresh rates, etc

Specific value: `"display"`

###### <a name="modules_items_anyOf_i1_oneOf_i11_compactType"></a>6.1.2.12.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > compactType`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"none"`           |

**Description:** Set if all displays should be printed in one line

Must be one of:
* "none"
* "original"
* "scaled"
* "original-with-refresh-rate"
* "scaled-with-refresh-rate"

###### <a name="modules_items_anyOf_i1_oneOf_i11_preciseRefreshRate"></a>6.1.2.12.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > preciseRefreshRate`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if decimal refresh rates should not be rounded into integers when printing

###### <a name="modules_items_anyOf_i1_oneOf_i11_order"></a>6.1.2.12.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > order`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"none"`           |

**Description:** Set the order should be used when printing

Must be one of:
* "none"
* "asc"
* "desc"

###### <a name="modules_items_anyOf_i1_oneOf_i11_key"></a>6.1.2.12.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i11_keyColor"></a>6.1.2.12.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i11_outputColor"></a>6.1.2.12.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i11_keyWidth"></a>6.1.2.12.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i11_format"></a>6.1.2.12.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Display > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i12"></a>6.1.2.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk`

**Title:** Disk

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                              | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                                                                                                    |
| --------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i12_type )                     | No      | const            | No         | -                                                                    | Print partitions, space usage, disk type, etc                                                                                                                                        |
| - [folders](#modules_items_anyOf_i1_oneOf_i12_folders )               | No      | string           | No         | -                                                                    | A colon (semicolon on Windows) separated list of folder paths for the disk output<br />Default: auto detection using mount-points<br />This option overrides other \`show*\` options |
| - [showExternal](#modules_items_anyOf_i1_oneOf_i12_showExternal )     | No      | boolean          | No         | -                                                                    | Set if external volume should be printed                                                                                                                                             |
| - [showHidden](#modules_items_anyOf_i1_oneOf_i12_showHidden )         | No      | boolean          | No         | -                                                                    | Set if hidden volumes should be printed                                                                                                                                              |
| - [showSubvolumes](#modules_items_anyOf_i1_oneOf_i12_showSubvolumes ) | No      | boolean          | No         | -                                                                    | Set if subvolumes should be printed                                                                                                                                                  |
| - [showReadOnly](#modules_items_anyOf_i1_oneOf_i12_showReadOnly )     | No      | boolean          | No         | -                                                                    | Set if read only volumes should be printed                                                                                                                                           |
| - [showUnknown](#modules_items_anyOf_i1_oneOf_i12_showUnknown )       | No      | boolean          | No         | -                                                                    | Set if unknown (unable to detect sizes) volumes should be printed                                                                                                                    |
| - [useAvailable](#modules_items_anyOf_i1_oneOf_i12_useAvailable )     | No      | boolean          | No         | -                                                                    | Use f_bavail (lpFreeBytesAvailableToCaller for Windows) instead of f_bfree to calculate used bytes                                                                                   |
| - [percent](#modules_items_anyOf_i1_oneOf_i12_percent )               | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                                                                                                                                       |
| - [key](#modules_items_anyOf_i1_oneOf_i12_key )                       | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                                                                                                    |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i12_keyColor )             | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                                                                                                    |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i12_outputColor )       | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                                                                                                               |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i12_keyWidth )             | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                                                                                                           |
| - [format](#modules_items_anyOf_i1_oneOf_i12_format )                 | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                                                                                                             |

###### <a name="modules_items_anyOf_i1_oneOf_i12_type"></a>6.1.2.13.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print partitions, space usage, disk type, etc

Specific value: `"disk"`

###### <a name="modules_items_anyOf_i1_oneOf_i12_folders"></a>6.1.2.13.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > folders`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** A colon (semicolon on Windows) separated list of folder paths for the disk output
Default: auto detection using mount-points
This option overrides other `show*` options

###### <a name="modules_items_anyOf_i1_oneOf_i12_showExternal"></a>6.1.2.13.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showExternal`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Set if external volume should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i12_showHidden"></a>6.1.2.13.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showHidden`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if hidden volumes should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i12_showSubvolumes"></a>6.1.2.13.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showSubvolumes`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if subvolumes should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i12_showReadOnly"></a>6.1.2.13.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showReadOnly`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if read only volumes should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i12_showUnknown"></a>6.1.2.13.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > showUnknown`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if unknown (unable to detect sizes) volumes should be printed

###### <a name="modules_items_anyOf_i1_oneOf_i12_useAvailable"></a>6.1.2.13.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > useAvailable`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Use f_bavail (lpFreeBytesAvailableToCaller for Windows) instead of f_bfree to calculate used bytes

###### <a name="modules_items_anyOf_i1_oneOf_i12_percent"></a>6.1.2.13.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i12_key"></a>6.1.2.13.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i12_keyColor"></a>6.1.2.13.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i12_outputColor"></a>6.1.2.13.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i12_keyWidth"></a>6.1.2.13.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i12_format"></a>6.1.2.13.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Disk > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i13"></a>6.1.2.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO`

**Title:** DiskIO

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i13_type )               | No      | const            | No         | -                                                                    | Print physical disk I/O throughput                                       |
| - [namePrefix](#modules_items_anyOf_i1_oneOf_i13_namePrefix )   | No      | string           | No         | -                                                                    | Show disks with given name prefix only                                   |
| - [detectTotal](#modules_items_anyOf_i1_oneOf_i13_detectTotal ) | No      | boolean          | No         | -                                                                    | Detect total bytes instead of current rate                               |
| - [key](#modules_items_anyOf_i1_oneOf_i13_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i13_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i13_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i13_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i13_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i13_type"></a>6.1.2.14.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print physical disk I/O throughput

Specific value: `"diskio"`

###### <a name="modules_items_anyOf_i1_oneOf_i13_namePrefix"></a>6.1.2.14.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > namePrefix`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Show disks with given name prefix only

###### <a name="modules_items_anyOf_i1_oneOf_i13_detectTotal"></a>6.1.2.14.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > detectTotal`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Detect total bytes instead of current rate

###### <a name="modules_items_anyOf_i1_oneOf_i13_key"></a>6.1.2.14.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i13_keyColor"></a>6.1.2.14.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i13_outputColor"></a>6.1.2.14.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i13_keyWidth"></a>6.1.2.14.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i13_format"></a>6.1.2.14.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DiskIO > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i14"></a>6.1.2.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE`

**Title:** DE

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                          | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                     |
| --------------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i14_type )                                 | No      | const            | No         | -                                                                    | Print desktop environment name                                                                        |
| - [slowVersionDetection](#modules_items_anyOf_i1_oneOf_i14_slowVersionDetection ) | No      | boolean          | No         | -                                                                    | Set if DE version should be detected with slow operations.<br />Should be unnecessary for most cases. |
| - [key](#modules_items_anyOf_i1_oneOf_i14_key )                                   | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                     |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i14_keyColor )                         | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                     |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i14_outputColor )                   | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                                |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i14_keyWidth )                         | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                            |
| - [format](#modules_items_anyOf_i1_oneOf_i14_format )                             | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                              |

###### <a name="modules_items_anyOf_i1_oneOf_i14_type"></a>6.1.2.15.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print desktop environment name

Specific value: `"de"`

###### <a name="modules_items_anyOf_i1_oneOf_i14_slowVersionDetection"></a>6.1.2.15.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > slowVersionDetection`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `"false"` |

**Description:** Set if DE version should be detected with slow operations.
Should be unnecessary for most cases.

###### <a name="modules_items_anyOf_i1_oneOf_i14_key"></a>6.1.2.15.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i14_keyColor"></a>6.1.2.15.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i14_outputColor"></a>6.1.2.15.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i14_keyWidth"></a>6.1.2.15.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i14_format"></a>6.1.2.15.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DE > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i15"></a>6.1.2.16. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS`

**Title:** DNS

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i15_type )               | No      | const            | No         | -                                                                    | Print DNS servers                                                        |
| - [showType](#modules_items_anyOf_i1_oneOf_i15_showType )       | No      | enum (of string) | No         | -                                                                    | Specify the type of DNS servers should be detected                       |
| - [key](#modules_items_anyOf_i1_oneOf_i15_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i15_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i15_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i15_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i15_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i15_type"></a>6.1.2.16.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print DNS servers

Specific value: `"dns"`

###### <a name="modules_items_anyOf_i1_oneOf_i15_showType"></a>6.1.2.16.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > showType`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"both"`           |

**Description:** Specify the type of DNS servers should be detected

Must be one of:
* "ipv4"
* "ipv6"
* "both"

###### <a name="modules_items_anyOf_i1_oneOf_i15_key"></a>6.1.2.16.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i15_keyColor"></a>6.1.2.16.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i15_outputColor"></a>6.1.2.16.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i15_keyWidth"></a>6.1.2.16.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i15_format"></a>6.1.2.16.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > DNS > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i16"></a>6.1.2.17. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad`

**Title:** Gamepad

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i16_type )               | No      | const            | No         | -                                                                    | List connected gamepads                                                  |
| - [percent](#modules_items_anyOf_i1_oneOf_i16_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i16_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i16_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i16_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i16_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i16_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i16_type"></a>6.1.2.17.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** List connected gamepads

Specific value: `"gamepad"`

###### <a name="modules_items_anyOf_i1_oneOf_i16_percent"></a>6.1.2.17.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i16_key"></a>6.1.2.17.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i16_keyColor"></a>6.1.2.17.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i16_outputColor"></a>6.1.2.17.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i16_keyWidth"></a>6.1.2.17.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i16_format"></a>6.1.2.17.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Gamepad > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i17"></a>6.1.2.18. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU`

**Title:** GPU

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                                  |
| ----------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i17_type )                       | No      | const            | No         | -                                                                    | Print GPU names, graphic memory size, type, etc                                                    |
| - [temp](#modules_items_anyOf_i1_oneOf_i17_temp )                       | No      | object           | No         | Same as [temp](#modules_items_anyOf_i1_oneOf_i2_temp )               | Detect and display temperature if supported                                                        |
| - [driverSpecific](#modules_items_anyOf_i1_oneOf_i17_driverSpecific )   | No      | boolean          | No         | -                                                                    | Use driver specific method to detect more detailed GPU information (memory usage, core count, etc) |
| - [detectionMethod](#modules_items_anyOf_i1_oneOf_i17_detectionMethod ) | No      | enum (of string) | No         | -                                                                    | Force using a specified method to detect GPUs                                                      |
| - [hideType](#modules_items_anyOf_i1_oneOf_i17_hideType )               | No      | enum (of string) | No         | -                                                                    | Specify the type of GPUs should not be printed                                                     |
| - [key](#modules_items_anyOf_i1_oneOf_i17_key )                         | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                                  |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i17_keyColor )               | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                                  |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i17_outputColor )         | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`                             |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i17_keyWidth )               | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                                         |
| - [format](#modules_items_anyOf_i1_oneOf_i17_format )                   | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail                           |

###### <a name="modules_items_anyOf_i1_oneOf_i17_type"></a>6.1.2.18.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print GPU names, graphic memory size, type, etc

Specific value: `"gpu"`

###### <a name="modules_items_anyOf_i1_oneOf_i17_temp"></a>6.1.2.18.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > temp`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |
| **Same definition as**    | [temp](#modules_items_anyOf_i1_oneOf_i2_temp)                             |

**Description:** Detect and display temperature if supported

###### <a name="modules_items_anyOf_i1_oneOf_i17_driverSpecific"></a>6.1.2.18.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > driverSpecific`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Use driver specific method to detect more detailed GPU information (memory usage, core count, etc)

###### <a name="modules_items_anyOf_i1_oneOf_i17_detectionMethod"></a>6.1.2.18.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > detectionMethod`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"auto"`           |

**Description:** Force using a specified method to detect GPUs

Must be one of:
* "auto"
* "pci"
* "vulkan"
* "opencl"
* "opengl"

###### <a name="modules_items_anyOf_i1_oneOf_i17_hideType"></a>6.1.2.18.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > hideType`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"none"`           |

**Description:** Specify the type of GPUs should not be printed

Must be one of:
* "integrated"
* "discrete"
* "none"

###### <a name="modules_items_anyOf_i1_oneOf_i17_key"></a>6.1.2.18.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i17_keyColor"></a>6.1.2.18.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i17_outputColor"></a>6.1.2.18.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i17_keyWidth"></a>6.1.2.18.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i17_format"></a>6.1.2.18.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > GPU > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i18"></a>6.1.2.19. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP`

**Title:** Local IP

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                  | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                    |
| ------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i18_type )                         | No      | const            | No         | -                                                                    | List local IP addresses (v4 or v6), MAC addresses, etc                               |
| - [showIpv4](#modules_items_anyOf_i1_oneOf_i18_showIpv4 )                 | No      | boolean          | No         | -                                                                    | Show IPv4 addresses                                                                  |
| - [showIpv6](#modules_items_anyOf_i1_oneOf_i18_showIpv6 )                 | No      | boolean          | No         | -                                                                    | Show IPv6 addresses                                                                  |
| - [showMac](#modules_items_anyOf_i1_oneOf_i18_showMac )                   | No      | boolean          | No         | -                                                                    | Show MAC addresses                                                                   |
| - [showLoop](#modules_items_anyOf_i1_oneOf_i18_showLoop )                 | No      | boolean          | No         | -                                                                    | Show loop back addresses (127.0.0.1)                                                 |
| - [showPrefixLen](#modules_items_anyOf_i1_oneOf_i18_showPrefixLen )       | No      | boolean          | No         | -                                                                    | Show network prefix length (/N)                                                      |
| - [showAllIps](#modules_items_anyOf_i1_oneOf_i18_showAllIps )             | No      | boolean          | No         | -                                                                    | Show all IPs bound to the same interface.<br />By default only the first IP is shown |
| - [compact](#modules_items_anyOf_i1_oneOf_i18_compact )                   | No      | boolean          | No         | -                                                                    | Show all IPs in one line                                                             |
| - [namePrefix](#modules_items_anyOf_i1_oneOf_i18_namePrefix )             | No      | string           | No         | -                                                                    | Show IPs with given name prefix only                                                 |
| - [defaultRouteOnly](#modules_items_anyOf_i1_oneOf_i18_defaultRouteOnly ) | No      | boolean          | No         | -                                                                    | Show ips that are used for default routing only                                      |
| - [key](#modules_items_anyOf_i1_oneOf_i18_key )                           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                    |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i18_keyColor )                 | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                    |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i18_outputColor )           | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`               |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i18_keyWidth )                 | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                           |
| - [format](#modules_items_anyOf_i1_oneOf_i18_format )                     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail             |

###### <a name="modules_items_anyOf_i1_oneOf_i18_type"></a>6.1.2.19.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** List local IP addresses (v4 or v6), MAC addresses, etc

Specific value: `"localip"`

###### <a name="modules_items_anyOf_i1_oneOf_i18_showIpv4"></a>6.1.2.19.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showIpv4`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Show IPv4 addresses

###### <a name="modules_items_anyOf_i1_oneOf_i18_showIpv6"></a>6.1.2.19.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showIpv6`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show IPv6 addresses

###### <a name="modules_items_anyOf_i1_oneOf_i18_showMac"></a>6.1.2.19.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showMac`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show MAC addresses

###### <a name="modules_items_anyOf_i1_oneOf_i18_showLoop"></a>6.1.2.19.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showLoop`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show loop back addresses (127.0.0.1)

###### <a name="modules_items_anyOf_i1_oneOf_i18_showPrefixLen"></a>6.1.2.19.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showPrefixLen`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Show network prefix length (/N)

###### <a name="modules_items_anyOf_i1_oneOf_i18_showAllIps"></a>6.1.2.19.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > showAllIps`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show all IPs bound to the same interface.
By default only the first IP is shown

###### <a name="modules_items_anyOf_i1_oneOf_i18_compact"></a>6.1.2.19.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > compact`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show all IPs in one line

###### <a name="modules_items_anyOf_i1_oneOf_i18_namePrefix"></a>6.1.2.19.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > namePrefix`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Show IPs with given name prefix only

###### <a name="modules_items_anyOf_i1_oneOf_i18_defaultRouteOnly"></a>6.1.2.19.10. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > defaultRouteOnly`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Show ips that are used for default routing only

###### <a name="modules_items_anyOf_i1_oneOf_i18_key"></a>6.1.2.19.11. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i18_keyColor"></a>6.1.2.19.12. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i18_outputColor"></a>6.1.2.19.13. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i18_keyWidth"></a>6.1.2.19.14. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i18_format"></a>6.1.2.19.15. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Local IP > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i19"></a>6.1.2.20. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory`

**Title:** Memory

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i19_type )               | No      | const            | No         | -                                                                    | Print system memory usage info                                           |
| - [percent](#modules_items_anyOf_i1_oneOf_i19_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i19_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i19_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i19_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i19_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i19_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i19_type"></a>6.1.2.20.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system memory usage info

Specific value: `"memory"`

###### <a name="modules_items_anyOf_i1_oneOf_i19_percent"></a>6.1.2.20.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i19_key"></a>6.1.2.20.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i19_keyColor"></a>6.1.2.20.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i19_outputColor"></a>6.1.2.20.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i19_keyWidth"></a>6.1.2.20.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i19_format"></a>6.1.2.20.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Memory > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i20"></a>6.1.2.21. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg`

**Title:** Loadavg

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i20_type )               | No      | const            | No         | -                                                                    | Print system load averages                                               |
| - [ndigits](#modules_items_anyOf_i1_oneOf_i20_ndigits )         | No      | integer          | No         | -                                                                    | Set the number of digits to keep after the decimal point                 |
| - [compact](#modules_items_anyOf_i1_oneOf_i20_compact )         | No      | boolean          | No         | -                                                                    | Show values in one line                                                  |
| - [percent](#modules_items_anyOf_i1_oneOf_i20_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i20_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i20_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i20_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i20_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i20_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i20_type"></a>6.1.2.21.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print system load averages

Specific value: `"loadavg"`

###### <a name="modules_items_anyOf_i1_oneOf_i20_ndigits"></a>6.1.2.21.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > ndigits`

|              |           |
| ------------ | --------- |
| **Type**     | `integer` |
| **Required** | No        |
| **Default**  | `2`       |

**Description:** Set the number of digits to keep after the decimal point

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |
| **Maximum**  | &le; 9 |

###### <a name="modules_items_anyOf_i1_oneOf_i20_compact"></a>6.1.2.21.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > compact`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Show values in one line

###### <a name="modules_items_anyOf_i1_oneOf_i20_percent"></a>6.1.2.21.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i20_key"></a>6.1.2.21.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i20_keyColor"></a>6.1.2.21.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i20_outputColor"></a>6.1.2.21.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i20_keyWidth"></a>6.1.2.21.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i20_format"></a>6.1.2.21.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Loadavg > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i21"></a>6.1.2.22. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO`

**Title:** NetIO

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                                  | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| ------------------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i21_type )                         | No      | const            | No         | -                                                                    | Print network I/O throughput                                             |
| - [namePrefix](#modules_items_anyOf_i1_oneOf_i21_namePrefix )             | No      | string           | No         | -                                                                    | Show IPs with given name prefix only                                     |
| - [defaultRouteOnly](#modules_items_anyOf_i1_oneOf_i21_defaultRouteOnly ) | No      | boolean          | No         | -                                                                    | Show ips that are used for default routing only                          |
| - [detectTotal](#modules_items_anyOf_i1_oneOf_i21_detectTotal )           | No      | boolean          | No         | -                                                                    | Detect total bytes instead of current rate                               |
| - [key](#modules_items_anyOf_i1_oneOf_i21_key )                           | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i21_keyColor )                 | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i21_outputColor )           | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i21_keyWidth )                 | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i21_format )                     | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i21_type"></a>6.1.2.22.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print network I/O throughput

Specific value: `"netio"`

###### <a name="modules_items_anyOf_i1_oneOf_i21_namePrefix"></a>6.1.2.22.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > namePrefix`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Show IPs with given name prefix only

###### <a name="modules_items_anyOf_i1_oneOf_i21_defaultRouteOnly"></a>6.1.2.22.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > defaultRouteOnly`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `true`    |

**Description:** Show ips that are used for default routing only

###### <a name="modules_items_anyOf_i1_oneOf_i21_detectTotal"></a>6.1.2.22.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > detectTotal`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Detect total bytes instead of current rate

###### <a name="modules_items_anyOf_i1_oneOf_i21_key"></a>6.1.2.22.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i21_keyColor"></a>6.1.2.22.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i21_outputColor"></a>6.1.2.22.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i21_keyWidth"></a>6.1.2.22.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i21_format"></a>6.1.2.22.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > NetIO > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i22"></a>6.1.2.23. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL`

**Title:** OpenGL

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i22_type )               | No      | const            | No         | -                                                                    | Print highest OpenGL version supported by the GPU                        |
| - [library](#modules_items_anyOf_i1_oneOf_i22_library )         | No      | enum (of string) | No         | -                                                                    | Set the OpenGL context creation library to use. Linux only               |
| - [key](#modules_items_anyOf_i1_oneOf_i22_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i22_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i22_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i22_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i22_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i22_type"></a>6.1.2.23.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print highest OpenGL version supported by the GPU

Specific value: `"opengl"`

###### <a name="modules_items_anyOf_i1_oneOf_i22_library"></a>6.1.2.23.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > library`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"auto"`           |

**Description:** Set the OpenGL context creation library to use. Linux only

Must be one of:
* "auto"
* "egl"
* "glx"
* "osmesa"

###### <a name="modules_items_anyOf_i1_oneOf_i22_key"></a>6.1.2.23.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i22_keyColor"></a>6.1.2.23.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i22_outputColor"></a>6.1.2.23.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i22_keyWidth"></a>6.1.2.23.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i22_format"></a>6.1.2.23.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > OpenGL > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i23"></a>6.1.2.24. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages`

**Title:** Packages

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type                      | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ------------------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i23_type )               | No      | const                     | No         | -                                                                    | List installed package managers and count of installed packages          |
| - [disabled](#modules_items_anyOf_i1_oneOf_i23_disabled )       | No      | array of enum (of string) | No         | -                                                                    | List of package managers to be disabled when detecting                   |
| - [key](#modules_items_anyOf_i1_oneOf_i23_key )                 | No      | string                    | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i23_keyColor )       | No      | enum (of string)          | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i23_outputColor ) | No      | enum (of string)          | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i23_keyWidth )       | No      | integer                   | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i23_format )           | No      | string                    | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i23_type"></a>6.1.2.24.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** List installed package managers and count of installed packages

Specific value: `"packages"`

###### <a name="modules_items_anyOf_i1_oneOf_i23_disabled"></a>6.1.2.24.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > disabled`

|              |                             |
| ------------ | --------------------------- |
| **Type**     | `array of enum (of string)` |
| **Required** | No                          |
| **Default**  | `["winget"]`                |

**Description:** List of package managers to be disabled when detecting

|                      | Array restrictions |
| -------------------- | ------------------ |
| **Min items**        | N/A                |
| **Max items**        | N/A                |
| **Items unicity**    | False              |
| **Additional items** | False              |
| **Tuple validation** | See below          |

| Each item of this array must be                                    | Description |
| ------------------------------------------------------------------ | ----------- |
| [disabled items](#modules_items_anyOf_i1_oneOf_i23_disabled_items) | -           |

###### <a name="autogenerated_heading_4"></a>6.1.2.24.2.1. JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > disabled > disabled items

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |

Must be one of:
* "am"
* "apk"
* "brew"
* "choco"
* "dpkg"
* "emerge"
* "eopkg"
* "flatpak"
* "macports"
* "nix"
* "opkg"
* "pacman"
* "paludis"
* "pkg"
* "pkgtool"
* "rpm"
* "scoop"
* "snap"
* "winget"
* "xbps"

###### <a name="modules_items_anyOf_i1_oneOf_i23_key"></a>6.1.2.24.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i23_keyColor"></a>6.1.2.24.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i23_outputColor"></a>6.1.2.24.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i23_keyWidth"></a>6.1.2.24.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i23_format"></a>6.1.2.24.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Packages > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i24"></a>6.1.2.25. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk`

**Title:** Physical Disk

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i24_type )               | No      | const            | No         | -                                                                    | Print physical disk information                                          |
| - [namePrefix](#modules_items_anyOf_i1_oneOf_i24_namePrefix )   | No      | string           | No         | -                                                                    | Show disks with given name prefix only                                   |
| - [temp](#modules_items_anyOf_i1_oneOf_i24_temp )               | No      | object           | No         | Same as [temp](#modules_items_anyOf_i1_oneOf_i2_temp )               | Detect and display temperature if supported                              |
| - [key](#modules_items_anyOf_i1_oneOf_i24_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i24_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i24_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i24_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i24_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i24_type"></a>6.1.2.25.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print physical disk information

Specific value: `"physicaldisk"`

###### <a name="modules_items_anyOf_i1_oneOf_i24_namePrefix"></a>6.1.2.25.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > namePrefix`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** Show disks with given name prefix only

###### <a name="modules_items_anyOf_i1_oneOf_i24_temp"></a>6.1.2.25.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > temp`

|                           |                                                                           |
| ------------------------- | ------------------------------------------------------------------------- |
| **Type**                  | `combining`                                                               |
| **Required**              | No                                                                        |
| **Additional properties** | [[Any type: allowed]](# "Additional Properties of any type are allowed.") |
| **Same definition as**    | [temp](#modules_items_anyOf_i1_oneOf_i2_temp)                             |

**Description:** Detect and display temperature if supported

###### <a name="modules_items_anyOf_i1_oneOf_i24_key"></a>6.1.2.25.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i24_keyColor"></a>6.1.2.25.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i24_outputColor"></a>6.1.2.25.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i24_keyWidth"></a>6.1.2.25.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i24_format"></a>6.1.2.25.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Physical Disk > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i25"></a>6.1.2.26. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP`

**Title:** Public IP

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                                 |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i25_type )               | No      | const            | No         | -                                                                    | Print your public IP address, etc                                                 |
| - [url](#modules_items_anyOf_i1_oneOf_i25_url )                 | No      | string           | No         | -                                                                    | The URL of public IP detection server to be used. Only HTTP protocol is supported |
| - [timeout](#modules_items_anyOf_i1_oneOf_i25_timeout )         | No      | integer          | No         | -                                                                    | Time in milliseconds to wait for the public ip server to respond                  |
| - [ipv6](#modules_items_anyOf_i1_oneOf_i25_ipv6 )               | No      | boolean          | No         | -                                                                    | Whether to use IPv6 for public IP detection server                                |
| - [key](#modules_items_anyOf_i1_oneOf_i25_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                                 |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i25_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`                 |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i25_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`            |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i25_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`                        |
| - [format](#modules_items_anyOf_i1_oneOf_i25_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail          |

###### <a name="modules_items_anyOf_i1_oneOf_i25_type"></a>6.1.2.26.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print your public IP address, etc

Specific value: `"publicip"`

###### <a name="modules_items_anyOf_i1_oneOf_i25_url"></a>6.1.2.26.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > url`

|              |                         |
| ------------ | ----------------------- |
| **Type**     | `string`                |
| **Required** | No                      |
| **Format**   | `url`                   |
| **Default**  | `"http://ipinfo.io/ip"` |

**Description:** The URL of public IP detection server to be used. Only HTTP protocol is supported

###### <a name="modules_items_anyOf_i1_oneOf_i25_timeout"></a>6.1.2.26.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > timeout`

|              |                  |
| ------------ | ---------------- |
| **Type**     | `integer`        |
| **Required** | No               |
| **Default**  | `"disabled (0)"` |

**Description:** Time in milliseconds to wait for the public ip server to respond

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

###### <a name="modules_items_anyOf_i1_oneOf_i25_ipv6"></a>6.1.2.26.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > ipv6`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Whether to use IPv6 for public IP detection server

###### <a name="modules_items_anyOf_i1_oneOf_i25_key"></a>6.1.2.26.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i25_keyColor"></a>6.1.2.26.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i25_outputColor"></a>6.1.2.26.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i25_keyWidth"></a>6.1.2.26.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i25_format"></a>6.1.2.26.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Public IP > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i26"></a>6.1.2.27. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator`

**Title:** Separator

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                      |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| - [type](#modules_items_anyOf_i1_oneOf_i26_type )               | No      | const            | No         | -                                                                    | Print a separator line                                                 |
| - [string](#modules_items_anyOf_i1_oneOf_i26_string )           | No      | string           | No         | -                                                                    | Set the string to be printed                                           |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i26_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\` |

###### <a name="modules_items_anyOf_i1_oneOf_i26_type"></a>6.1.2.27.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print a separator line

Specific value: `"separator"`

###### <a name="modules_items_anyOf_i1_oneOf_i26_string"></a>6.1.2.27.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > string`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |
| **Default**  | `"-"`    |

**Description:** Set the string to be printed

###### <a name="modules_items_anyOf_i1_oneOf_i26_outputColor"></a>6.1.2.27.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Separator > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

##### <a name="modules_items_anyOf_i1_oneOf_i27"></a>6.1.2.28. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound`

**Title:** Sound

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i27_type )               | No      | const            | No         | -                                                                    | Print sound devices, volume, etc                                         |
| - [soundType](#modules_items_anyOf_i1_oneOf_i27_soundType )     | No      | enum (of string) | No         | -                                                                    | Set what type of sound devices should be printed                         |
| - [percent](#modules_items_anyOf_i1_oneOf_i27_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i27_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i27_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i27_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i27_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i27_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i27_type"></a>6.1.2.28.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print sound devices, volume, etc

Specific value: `"sound"`

###### <a name="modules_items_anyOf_i1_oneOf_i27_soundType"></a>6.1.2.28.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > soundType`

|              |                    |
| ------------ | ------------------ |
| **Type**     | `enum (of string)` |
| **Required** | No                 |
| **Default**  | `"main"`           |

**Description:** Set what type of sound devices should be printed

Must be one of:
* "main"
* "active"
* "all"

###### <a name="modules_items_anyOf_i1_oneOf_i27_percent"></a>6.1.2.28.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i27_key"></a>6.1.2.28.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i27_keyColor"></a>6.1.2.28.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i27_outputColor"></a>6.1.2.28.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i27_keyWidth"></a>6.1.2.28.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i27_format"></a>6.1.2.28.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Sound > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i28"></a>6.1.2.29. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap`

**Title:** Swap

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i28_type )               | No      | const            | No         | -                                                                    | Print swap (paging file) space usage                                     |
| - [percent](#modules_items_anyOf_i1_oneOf_i28_percent )         | No      | object           | No         | Same as [percent](#modules_items_anyOf_i1_oneOf_i2_percent )         | Threshold of percentage colors                                           |
| - [key](#modules_items_anyOf_i1_oneOf_i28_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i28_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i28_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i28_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i28_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i28_type"></a>6.1.2.29.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print swap (paging file) space usage

Specific value: `"swap"`

###### <a name="modules_items_anyOf_i1_oneOf_i28_percent"></a>6.1.2.29.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > percent`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |
| **Same definition as**    | [percent](#modules_items_anyOf_i1_oneOf_i2_percent)     |

**Description:** Threshold of percentage colors

###### <a name="modules_items_anyOf_i1_oneOf_i28_key"></a>6.1.2.29.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i28_keyColor"></a>6.1.2.29.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i28_outputColor"></a>6.1.2.29.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i28_keyWidth"></a>6.1.2.29.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i28_format"></a>6.1.2.29.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Swap > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i29"></a>6.1.2.30. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title`

**Title:** Title

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i29_type )               | No      | const            | No         | -                                                                    | Print title, which contains your user name, hostname                     |
| - [fqdn](#modules_items_anyOf_i1_oneOf_i29_fqdn )               | No      | boolean          | No         | -                                                                    | Set if the title should use fully qualified domain name                  |
| - [color](#modules_items_anyOf_i1_oneOf_i29_color )             | No      | object           | No         | -                                                                    | Set colors of the different part of title                                |
| - [key](#modules_items_anyOf_i1_oneOf_i29_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i29_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i29_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i29_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i29_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i29_type"></a>6.1.2.30.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print title, which contains your user name, hostname

Specific value: `"title"`

###### <a name="modules_items_anyOf_i1_oneOf_i29_fqdn"></a>6.1.2.30.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > fqdn`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if the title should use fully qualified domain name

###### <a name="modules_items_anyOf_i1_oneOf_i29_color"></a>6.1.2.30.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color`

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

**Description:** Set colors of the different part of title

| Property                                                | Pattern | Type             | Deprecated | Definition                           | Title/Description                       |
| ------------------------------------------------------- | ------- | ---------------- | ---------- | ------------------------------------ | --------------------------------------- |
| - [user](#modules_items_anyOf_i1_oneOf_i29_color_user ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set color of the user name (left part)  |
| - [at](#modules_items_anyOf_i1_oneOf_i29_color_at )     | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set color of the @ symbol (middle part) |
| - [host](#modules_items_anyOf_i1_oneOf_i29_color_host ) | No      | enum (of string) | No         | Same as [1](#logo_oneOf_i2_color_1 ) | Set color of the host name (right part) |

###### <a name="modules_items_anyOf_i1_oneOf_i29_color_user"></a>6.1.2.30.3.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > user`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set color of the user name (left part)

###### <a name="modules_items_anyOf_i1_oneOf_i29_color_at"></a>6.1.2.30.3.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > at`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set color of the @ symbol (middle part)

###### <a name="modules_items_anyOf_i1_oneOf_i29_color_host"></a>6.1.2.30.3.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > color > host`

|                        |                             |
| ---------------------- | --------------------------- |
| **Type**               | `enum (of string)`          |
| **Required**           | No                          |
| **Same definition as** | [1](#logo_oneOf_i2_color_1) |

**Description:** Set color of the host name (right part)

###### <a name="modules_items_anyOf_i1_oneOf_i29_key"></a>6.1.2.30.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i29_keyColor"></a>6.1.2.30.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i29_outputColor"></a>6.1.2.30.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i29_keyWidth"></a>6.1.2.30.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i29_format"></a>6.1.2.30.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Title > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i30"></a>6.1.2.31. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users`

**Title:** Users

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                        | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| --------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i30_type )               | No      | const            | No         | -                                                                    | Print users currently logged in                                          |
| - [compact](#modules_items_anyOf_i1_oneOf_i30_compact )         | No      | boolean          | No         | -                                                                    | Show all active users in one line                                        |
| - [myselfOnly](#modules_items_anyOf_i1_oneOf_i30_myselfOnly )   | No      | boolean          | No         | -                                                                    | Show only the current user                                               |
| - [key](#modules_items_anyOf_i1_oneOf_i30_key )                 | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i30_keyColor )       | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i30_outputColor ) | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i30_keyWidth )       | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i30_format )           | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i30_type"></a>6.1.2.31.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print users currently logged in

Specific value: `"users"`

###### <a name="modules_items_anyOf_i1_oneOf_i30_compact"></a>6.1.2.31.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > compact`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show all active users in one line

###### <a name="modules_items_anyOf_i1_oneOf_i30_myselfOnly"></a>6.1.2.31.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > myselfOnly`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Show only the current user

###### <a name="modules_items_anyOf_i1_oneOf_i30_key"></a>6.1.2.31.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i30_keyColor"></a>6.1.2.31.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i30_outputColor"></a>6.1.2.31.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i30_keyWidth"></a>6.1.2.31.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i30_format"></a>6.1.2.31.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Users > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i31"></a>6.1.2.32. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather`

**Title:** Weather

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                          | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| ----------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i31_type )                 | No      | const            | No         | -                                                                    | Print weather information                                                |
| - [location](#modules_items_anyOf_i1_oneOf_i31_location )         | No      | string           | No         | -                                                                    | The location to display                                                  |
| - [timeout](#modules_items_anyOf_i1_oneOf_i31_timeout )           | No      | integer          | No         | -                                                                    | Time in milliseconds to wait for the weather server to respond           |
| - [outputFormat](#modules_items_anyOf_i1_oneOf_i31_outputFormat ) | No      | string           | No         | -                                                                    | The output weather format to be used (must be URI encoded)               |
| - [key](#modules_items_anyOf_i1_oneOf_i31_key )                   | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i31_keyColor )         | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i31_outputColor )   | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i31_keyWidth )         | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i31_format )             | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i31_type"></a>6.1.2.32.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print weather information

Specific value: `"weather"`

###### <a name="modules_items_anyOf_i1_oneOf_i31_location"></a>6.1.2.32.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > location`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | No       |

**Description:** The location to display

###### <a name="modules_items_anyOf_i1_oneOf_i31_timeout"></a>6.1.2.32.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > timeout`

|              |                  |
| ------------ | ---------------- |
| **Type**     | `integer`        |
| **Required** | No               |
| **Default**  | `"disabled (0)"` |

**Description:** Time in milliseconds to wait for the weather server to respond

| Restrictions |        |
| ------------ | ------ |
| **Minimum**  | &ge; 0 |

###### <a name="modules_items_anyOf_i1_oneOf_i31_outputFormat"></a>6.1.2.32.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > outputFormat`

|              |                  |
| ------------ | ---------------- |
| **Type**     | `string`         |
| **Required** | No               |
| **Default**  | `"%t+-+%C+(%l)"` |

**Description:** The output weather format to be used (must be URI encoded)

###### <a name="modules_items_anyOf_i1_oneOf_i31_key"></a>6.1.2.32.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i31_keyColor"></a>6.1.2.32.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i31_outputColor"></a>6.1.2.32.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i31_keyWidth"></a>6.1.2.32.8. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i31_format"></a>6.1.2.32.9. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > Weather > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_oneOf_i32"></a>6.1.2.33. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM`

**Title:** WM

|                           |                                                         |
| ------------------------- | ------------------------------------------------------- |
| **Type**                  | `object`                                                |
| **Required**              | No                                                      |
| **Additional properties** | [[Not allowed]](# "Additional Properties not allowed.") |

| Property                                                          | Pattern | Type             | Deprecated | Definition                                                           | Title/Description                                                        |
| ----------------------------------------------------------------- | ------- | ---------------- | ---------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| - [type](#modules_items_anyOf_i1_oneOf_i32_type )                 | No      | const            | No         | -                                                                    | Print window manager name and version                                    |
| - [detectPlugin](#modules_items_anyOf_i1_oneOf_i32_detectPlugin ) | No      | boolean          | No         | -                                                                    | Set if window manager plugin should be detected on supported platforms   |
| - [key](#modules_items_anyOf_i1_oneOf_i32_key )                   | No      | string           | No         | Same as [key](#modules_items_anyOf_i1_oneOf_i1_key )                 | Key of the module                                                        |
| - [keyColor](#modules_items_anyOf_i1_oneOf_i32_keyColor )         | No      | enum (of string) | No         | Same as [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor )       | Color of the module key. Left empty to use \`display.color.keys\`        |
| - [outputColor](#modules_items_anyOf_i1_oneOf_i32_outputColor )   | No      | enum (of string) | No         | Same as [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor ) | Output color of the module. Left empty to use \`display.color.output\`   |
| - [keyWidth](#modules_items_anyOf_i1_oneOf_i32_keyWidth )         | No      | integer          | No         | Same as [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth )       | Width of the module key. Use 0 to use \`display.keyWidth\`               |
| - [format](#modules_items_anyOf_i1_oneOf_i32_format )             | No      | string           | No         | Same as [format](#modules_items_anyOf_i1_oneOf_i1_format )           | Output format of the module. See \`-h &lt;module&gt;-format\` for detail |

###### <a name="modules_items_anyOf_i1_oneOf_i32_type"></a>6.1.2.33.1. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > type`

|              |         |
| ------------ | ------- |
| **Type**     | `const` |
| **Required** | No      |

**Description:** Print window manager name and version

Specific value: `"wm"`

###### <a name="modules_items_anyOf_i1_oneOf_i32_detectPlugin"></a>6.1.2.33.2. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > detectPlugin`

|              |           |
| ------------ | --------- |
| **Type**     | `boolean` |
| **Required** | No        |
| **Default**  | `false`   |

**Description:** Set if window manager plugin should be detected on supported platforms

###### <a name="modules_items_anyOf_i1_oneOf_i32_key"></a>6.1.2.33.3. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > key`

|                        |                                             |
| ---------------------- | ------------------------------------------- |
| **Type**               | `string`                                    |
| **Required**           | No                                          |
| **Same definition as** | [key](#modules_items_anyOf_i1_oneOf_i1_key) |

**Description:** Key of the module

###### <a name="modules_items_anyOf_i1_oneOf_i32_keyColor"></a>6.1.2.33.4. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > keyColor`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `enum (of string)`                                    |
| **Required**           | No                                                    |
| **Same definition as** | [keyColor](#modules_items_anyOf_i1_oneOf_i1_keyColor) |

**Description:** Color of the module key. Left empty to use `display.color.keys`

###### <a name="modules_items_anyOf_i1_oneOf_i32_outputColor"></a>6.1.2.33.5. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > outputColor`

|                        |                                                             |
| ---------------------- | ----------------------------------------------------------- |
| **Type**               | `enum (of string)`                                          |
| **Required**           | No                                                          |
| **Same definition as** | [outputColor](#modules_items_anyOf_i1_oneOf_i1_outputColor) |

**Description:** Output color of the module. Left empty to use `display.color.output`

###### <a name="modules_items_anyOf_i1_oneOf_i32_keyWidth"></a>6.1.2.33.6. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > keyWidth`

|                        |                                                       |
| ---------------------- | ----------------------------------------------------- |
| **Type**               | `integer`                                             |
| **Required**           | No                                                    |
| **Default**            | `0`                                                   |
| **Same definition as** | [keyWidth](#modules_items_anyOf_i1_oneOf_i1_keyWidth) |

**Description:** Width of the module key. Use 0 to use `display.keyWidth`

###### <a name="modules_items_anyOf_i1_oneOf_i32_format"></a>6.1.2.33.7. Property `JSON config > modules > modules items > anyOf > item 1 > oneOf > WM > format`

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Type**               | `string`                                          |
| **Required**           | No                                                |
| **Same definition as** | [format](#modules_items_anyOf_i1_oneOf_i1_format) |

**Description:** Output format of the module. See `-h &lt;module&gt;-format` for detail

##### <a name="modules_items_anyOf_i1_type"></a>6.1.2.34. Property `JSON config > modules > modules items > anyOf > item 1 > type`

|              |          |
| ------------ | -------- |
| **Type**     | `string` |
| **Required** | Yes      |

----------------------------------------------------------------------------------------------------------------------------
Generated using [json-schema-for-humans](https://github.com/coveooss/json-schema-for-humans) on 2024-06-21 at 10:06:48 +0800
