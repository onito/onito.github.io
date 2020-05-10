---
layout: post
title: 라즈베리파이 GPIO를 이용한 비접촉 수위센서 코딩💦
comments: true
category: [raspberry-pi]
tag: [IOT, GPIO]
excerpt_separator: <!-- more -->
---
<img alt="클라우드" width="140" align="left" style="padding: 0px 20px 0px 10px;" src="/images/post_img/20180630-0logo.png" >

DFRobot 의 비접촉 수위센서(Non-contact Liquid Level Sensor) XKC-Y25-T12V SKU: SEN0204 는, 보이지 않는 탱크나 파이프 내부에 공기가 찼는지, 물이 찼는지를 감지하는 센서입니다. 바이너리 신호선택(1,0)을 돌려줍니다. 라즈베리파이의 GPIO (Genreral purpose Input Output) 포트를 이용하여 신호를 감지 하는 코딩을 해봅니다.
<!-- more -->


<br><br>
# 1.0 라즈베리파이 GPIO 수위센서
> 1. 비접속 수위센서 코딩
> 1.RPi.GPIO modul - Digital INPUT.
> 1. DFRobot Non-contact Liquid Level Sensor XKC-Y25-T12V SKU: SEN0204
> 1. port 20,16,12 : Arduino Water level sensor

|<img src="/images/post_img/20180630-03illu01.png" width="300">　|<img src="/images/post_img/20180630-03illu02.png" width="300">|
|:-------:|:-------:|
| **fig.01** - on pipes | **fig.02** - on valves |


<br><br>
## Introduction of XKC-Y25-T12V SKU
> The non-contact liquid level sensor utilizes advanced signal processing technology by using a powerful chip with high-speed operation capacity to achieve non-contact liquid level detection. No contact with liquid makes the module suitable for hazardous applications such as detecting toxic substances, strong acid, strong alkali and all kinds of liquid in an airtight container under high pressure. There are no special requirements for the liquid or container and the sensor is easy to use and easy to install.
The liquid level sensor is equipped with an interface adapter that makes it compatible with DFRobot “Gravity” interface. There are 4 levels of sensitivity which are set by pressing the SET button.

* 4Pins into 3 Pins : ( 4 Pins are converted into 3 with small control board)
* VCC (5~24v)
* GND
* Signal (Digital get)



<br><br>
## Pin Description
Liquid Level Sensor-XKC-Y25-T12V Pin defination

|<img src="/images/post_img/20180630-02pi-02.png" width="550">|
|:-------:|
| **fig.03** - adaptor & sensor in detail |
||

|:---:|:------:|:----:|:----------|
|Num. |　Name    　|　     |　Description |
|1    |　(Brown) 　|　VCC　|　In VCC (range: +5V~+24V) |
|2    |　(Yellow)　|　OUT　|　Liquid level sensor signal output |
|3    |　(Blue)	　|　GND　|　GND |
|4    |　(Black) 　|　ADJ　|　Sensor sensitivity adjustingswitch (Adjust the sensor |
|     |　        　|　     |　sensitivity, 4 modes in all. Click the SET button  |
|     |　        　|　     |　on the adapter to set the sensor sensitivity.) |



<br><br>
## Non-contact Liquid Level Sensor Adapter
Liquid Level Sensor-XKC-Y25-T12V Pin defination


|<img src="/images/post_img/20180630-01circuit.png" width="">|
|:-------:|
| **fig.04** - adaptor circuit |
||

|　Num.	    |Name　|　Description |
|:--------:|:---:|:----|
|　Left_1	|VCC|　InVCC (range: +5V~+24V) |
|　Left_2	|OUT|　Liquid level sensor signal output |
|　Left_3	|GND|　GND |
|　Left_4	|ADJ|　Sensor sensitivity adjusting switch (Adjust the sensor  |
|　        |   |　sensitivity, 4 modes in all. Click the SET button on  |
|　        |   |　the adapterto set the sensor sensitivity.) |
|　Right_1 |OUT|　Signal |
|　Right_2 |VCC|　InVCC |
|　Right_3 |GND|　GND |


<br><br>

|<img src="/images/post_img/20180630-02pi-01.png" width="550">　|
|:-------:|
| **fig.05** - PI, adaptor & sensor |
||


<br><br>
## 2.0 파이썬 코드삽입 태그

```python
#!/usr/bin/python3
import RPi.GPIO as GPIO
import time
'''  RPi.GPIO modul - Digital INPUT.
 DFRobot Non-contact Liquid Level Sensor XKC-Y25-T12V SKU: SEN0204
 # 20,16,12 : Arduino Water level sensor
'''

SENS = []
SENS.extend((14, 15, 18))       # Arduino Sensors
SENS.extend((23, 24))           # Blank
SENS.extend((25, 8, 7, 1))      # XKC-Y25-T12V SKU: SEN0204

PORTS = len(SENS)

def setup():
    # BroadCom Chip Pin# Set
    GPIO.setmode(GPIO.BCM)
    for n in range(PORTS):
        GPIO.setup(SENS[n], GPIO.IN)

def loop():
    COUNT = 1
    while True:
        print('-------------- count: %s' % COUNT)

        for n in range(PORTS):
            num_bcm = str()
            read_bcm = GPIO.input(SENS[n])

            if SENS[n] >= 10:
                num_bcm = str(SENS[n])
            else:
                num_bcm = '0' + str(SENS[n])
            print('GPIO# %s = %s' % (num_bcm, read_bcm))

        print()
        time.sleep(1)
        COUNT += 1


def main():
    setup()

    try:
        loop()
    except KeyboardInterrupt:
        GPIO.cleanup()


if __name__ == '__main__':
    main()
```


<!-- this & that -->
