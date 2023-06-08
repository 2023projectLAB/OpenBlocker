# OpenBlocker

### 차량 감지
---
**담당**</br>
최승우  

**소개**</br>
초음파 센서를 사용해서 센서앞에 있는 물체와의 거리를 통해 차량인지 구분하고
차단기를 올린다.

**진행상황**</br>
차단기로부터 물체까지의 거리측정 가능.


**코드**</br>
import paho.mqtt.client as mqtt
import json
import random
import time
from pymata4 import pymata4
port = "/dev/ttyACM0"


TRIGGER_PIN = 3
ECHO_PIN = 2
board = pymata4.Pymata4(port)
def on_connect(client, userdata, flags, rc):
    if rc == 0:
        print("connected OK")
    else:
        print("Bad connection Returned code=", rc)


def on_disconnect(client, userdata, flags, rc=0):
    print(str(rc))


def on_publish(client, userdata, mid):
    print("In on_pub callback mid= ", mid)

# 새로운 클라이언트 생성
client = mqtt.Client()
# 콜백 함수 설정 on_connect(브로커에 접속), on_disconnect(브로커에 접속중료), on_publish(메세지 발행)
client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish
# address : localhost, port: 1883 에 연결
client.connect('59.1.188.107', 3883)
client.loop_start()
# common topic 으로 메세지 발행
i=0
while(i<100):
    board.set_pin_mode_sonar(TRIGGER_PIN,ECHO_PIN)
    value = board.sonar_read(TRIGGER_PIN)[0]
    send_data = random.randrange(1,100)
    print(value)
    client.publish('Sonar',value,1)
    
    i+=1
    time.sleep(2)


client.loop_stop()
# 연결 종료
client.disconnect()
def measure(channel):
    board.set_pin_mode_sonar(TRIGGER_PIN,ECHO_PIN)



