# ESP32-Flash-Crash
Code to demonstrate the Watch-Dog-Timer induces crashing of an ESP32 when downloading large files > 150k via WiFi

This is discussed in detail here: https://github.com/me-no-dev/ESPAsyncWebServer/issues/984

**In short:** 

The download of files > 150k Bytes via WiFi results in a crash induced by Watch-Dog-Timer (WDT) on task `async_tcp (CPU 1)`. This happens on all types of ESP32 of the "classic" type (new versions S2, S3, C3 not yet tested). 

When tested with ESP8266 (the present code is for ESP32-only but can be easily adapted to ESP8266) a WDT crash was NEVER seen!

On an ESP32 files smaller than 100k never result in a WDT crash. The transition from "never crashing" to "crashing" is between 100k and 150k files sizes. Worst effect was observed at a file size of 300k. Strangely, it actually got a little bit better at even large files.

While all types of ESP32 tested have shown this behaviour, its intensity seems to depend on the type of flash being used!

**How to run this code:**

Download zip-file for your PlatformIO or Arduino (test on Ar2.0Beta7) IDE, unzip, and open. The code is identical for both IDE, only the file naming is different. 

In file `FlashCrash.h` enter you proper WiFi credentials (line 14ff). Upload and run.

The default is running on a FFat file system. It can easily be changed to LittleFS or SPIFFS (file `FlashCrash.h` line 26-27). When you change, do not forget to adapt your partition settings in file `partitions.csv`!

When running enter the ESP32's IP in a web browser, and you will see the root page like in the left side of the picture. Click on the link `Download Data by JS`, and another page opens showing the downloaded content, like in the right side of the picture. This continues automatically, and every 100 downloads another line will be added to the root page (refersh to see).

![Auswahl_022](https://user-images.githubusercontent.com/9335726/126503324-2452d67c-21b1-4fc1-8a1d-9883ccb4bfc4.png)

**How this works**

The link `Download Data by JS` will upload a small page with JavaScript content. The JS does the download, sends a message to the ESP32 when complete, and triggers the next download after a pause of 50ms. This continues indefinitely.

*Note:* when the ESP32 crashes, then the webbrowser goes into a timeout. JS recognizes this timeout. Unfortunately, while this takes only ~10 sec on Firefox, it takes a long ~45 sec on Chromium!

**Results**

In the left side of the picture you see that the first 800 downloads occured without a single crash (Boots = 0). But after that, between 1 and 5 crashes (=Boots) occurred per 100 downloads. 


