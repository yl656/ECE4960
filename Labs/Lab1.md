---
title: The Artemis Board
description: <a href="https://cei-lab.github.io/ECE4960/Lab1.html" style="color:#FFCC00;">Lab 1</a>
layout: default
---
## 1 and 2. Environment

I updated my local installation of the Arduino IDE and installed the spcified version (1.1.2) of the Sparkfun Apollo3 board support pack. I then connected the Artemis Nano and my laptop, selected the correct port, lowered the baud rate as suggested, and uploaded an example script successfully. 

## 3. Blink

After uploading ```Example1_Blink``` to the board, the script toggles the on-board LED by writing 0 and 1 to the pin that is a defined constant ```LED_BUILTIN```  in a loop, and thus achieves the blinking effect. The result can be seen in the following video.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/TRdj1EKi1-c" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 4. Serial

Another example script I tested out was ```Example2_Serial```. The script sets up the Serial port with baud rate 9600, prints out a menu, asking the user how many cheeses would be activated, and prints out a response indicating how many cheeses are dispensed after the user enters a number or the program times out. In the following video, I successfully got the output ```Thank you. 9 cheeses dispensed.``` before I exited the program.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/BkgQW1qrjVc" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 5. Temperature

In this example script, ```Example4_analogRead``` tests several analog pins as well as the function ```getInternalTemp()```, which returns the internal die temperature of the Artemis Nano board. As we can see from the following video, the internal die temperature started out at approximately 32°C but slowly rises to about 34°C towards the end of the video. This happened because I was blowing on the processor directly and thus warming the chip.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Or_cgKoJYTQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 6. Pulse Density Modulated Microphone

The Artemis Nano also comes with a pulse density microphone that we can access. The script ```Example1_MicrophoneOutput``` reads the micropphone data into a buffer using the function ```myPDM.getData()```. It then performs FFT on the collected samples with the FFT library provided by Keil's CMSIS DSP Software Library. Finally, it finds the corresponding frequency of the FFT bucket with the maximum magnitude, which would be the loudest frequency. The following video shows that the board prints out the loudest frequencies over a period of time. At the beginning, I did not whistle or play any music. As a result, the loudest frequencies are mostly 0Hz and 57Hz. The 57Hz component is most likely due to the electronics in my room as the United States run on 60Hz AC. As soon as I started my music, however, the value started fluctuating with the music that I was playing.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/HmqB1F5eaOM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## 7. Sound-controlled LED on Battery

Finally, I changed the example script so that the LED turns on when the loudest frequency is greater than 250 and turns off otherwise. I did so by changing one line. Originally, the script prints out the loudest frequency to the console.

```
Serial.printf("Loudest frequency: %d\n", ui32LoudestFrequency);
```

Now, it writes 1 to to the LED pin if the loudest frequency is greater than 250 and 0 otherwise.

```
digitalWrite(LED_BUILTIN, ui32LoudestFrequency > 250);
```

The following video shows that. Whenever I whistle, the LED turns on, and it turns off whenever I stop.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/7MwxSqB2zM0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
