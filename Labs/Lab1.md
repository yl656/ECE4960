---
title: The Artemis Board
description: <a href="https://cei-lab.github.io/ECE4960/Lab1.html" style="color:#FFCC00;">Lab 1</a>
layout: default
---
## 1 and 2. Environment

I updated my local installation of the Arduino IDE and installed the spcified version (1.1.2) of the Sparkfun Apollo3 board support pack. I then connected the Artemis Nano and my laptop, selected the correct port, lowered the baud rate as suggested, and uploaded an example script successfully. 

## 3. Blink

After uploading ```Example1_Blink``` to the board, the script toggles the on-board LED by writing 0 and 1 to the pin defined constant ```LED_BUILTIN```  in a loop, and thus achieves the blinking effect. The result can be seen in the following video.

```
digitalWrite(LED_BUILTIN, LOW);
delay(1000);
digitalWrite(LED_BUILTIN, HIGH);
delay(1000);
```

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/TRdj1EKi1-c" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 4. Serial

Another example script I tested out was ```Example2_Serial```. The script sets up the Serial port with baud rate 9600 and prints out a menu, asking the user how many cheeses would be activated, and prints out a response indicating how many cheeses are dispensed after the user enters a number or the program times out. In the following video, I successfully saw the output ```Thank you. 9 cheeses dispensed.``` before I exited the program.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/BkgQW1qrjVc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 5. Analog Temperature
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Or_cgKoJYTQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 6. Pulse Density Modulated Microphone
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/HmqB1F5eaOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 7. Sound-controlled LED on Battery
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7MwxSqB2zM0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
