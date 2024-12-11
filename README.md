젯슨과 opencv를 활용한 라인 트레이서 (RPM 100,200)

라인을 검출한 후 라인의 무게중심의 좌표에 따라 구한 에러값을 통하여  rvel,lvel값을 조절하여 라인을 따라가게 된다 라인이 없어지는 경우 이전 값을 따라간다.(라인이 다시 검출 될때까지)

블록도

![image](https://github.com/user-attachments/assets/d130e3a1-f946-47bb-a245-2e7ba626dedd)


RPM 200(in, out)

https://www.youtube.com/watch?v=lV1nvZSyoXs

https://www.youtube.com/watch?v=jQ58OPhfsXM

RPM 100(in, out)

https://www.youtube.com/watch?v=IlPUGnL3SJY

https://www.youtube.com/watch?v=KZ65GFXh3Q8

vision.hpp 헤더파일

![image](https://github.com/user-attachments/assets/f989f6da-d514-46dd-98a1-feafb06682a8)

각 함수 설명 

void setFrame(Mat& frame)

![image](https://github.com/user-attachments/assets/8c476d62-a2d1-4c96-83d1-d00439ae9956)

매개변수로 받은 Mat 객체를 전처리한다.
참조로 받은 frame을 영상을 라인 검출하기 좋게 밑 1/4로 설정 후 흑백으로 변환, 평균 밝기 설정 후 이진화 한다.

int findMinIndex(Mat stats,Mat centroids, int lable, Point& po,int MINDISTANCE)

![image](https://github.com/user-attachments/assets/dcd3d076-4659-4c3d-9ee1-d962adc4bd87)

포인트 po를 기준으로 가장 가까운 객체의 인덱스를 리턴한다. 객체의 면적이 150이하인 경우 노이즈로 간주하여 검출하지 않는다.
찾은 객체의 거리가 MINDISTANCE보다 큰 경우 값을 리턴하지 않는다(다른 라인으로 간주). 객체를 검출한 경우 포인트 Po의 값을 재설정 해준다

void drawBoundingBox(Mat& frame,Mat stats,Mat centroids, int lable, int index, Point po)

![image](https://github.com/user-attachments/assets/de9d41e2-af3c-4637-bb35-7f2982fe9dac)

객체에 바운딩 박스를 그린다. 현재 따라가고 있는 객체는 빨강 다른 객체는 파랑 이외의 노이즈는 노랑으로 처리한다
따라가고 있는 객체가 검출되지 않는 경우 가장 최근의 포인트 po의 위치에 중심을 그린다.

double getErr(Mat frame, Point po,double gain)

![image](https://github.com/user-attachments/assets/b599a80c-4965-43a4-aa61-dfe72fe09a07)

에러값을 구한다 GAIN값을 조정하여 적절한 에러값을 구할 수 있다.

main.c 

![image](https://github.com/user-attachments/assets/ac02fb77-d0ee-4105-b6b2-f5fb2de07426)

필요한 헤더파일 인클루드 및 RPM 값과 GAIN 값을 디파인 해준다 RPM 값을 200으로 설정하는 경우 GAIN값을 0.25로 바꾸어 준다.


![image](https://github.com/user-attachments/assets/e4dd0443-e264-480f-8903-2ae294674b59)

Dxl, VideoCapture, VideoWrter, Tick Meter객체 생성 및 lvel,rvel (각 좌우 속도값) 등을 설정해준다  

전체 while문 

![image](https://github.com/user-attachments/assets/b63bd7f2-bcd3-4346-a304-63a1267adc75)

세부 설명

![image](https://github.com/user-attachments/assets/c90b99ff-130f-4430-9eea-676e1efdd4a9)

시간 측정을 위한 TickMeter 객체럴 리셋 및 스타트 해준다. 로봇은 처음에 정지 상태이기 때문에 사용자가 's' 를 입력하는 경우 mode 가 true로 바뀌면서 모터가 작동하고 'q'를 누르는 경우 종료한다.(엔터를 누르지 않아도 입력됨)

![image](https://github.com/user-attachments/assets/c770ae6b-94e9-4671-9f02-3a1415fa50bf)

source로부터 frame 객체를 받아와 처리하지 않은 원본을 pc로 전송해준 뒤 앞에 설명한 setFrame 함수를 통해 전처리 해준다.

![image](https://github.com/user-attachments/assets/7bfe0a27-568b-44aa-8cee-01fc54216b7a)

connectedComponentsWithStats함수를 통해 필요한 정보를 Mat 객체로 받는다(무게중심, 면적, 가로와 세로의 길이 등)

초기 Point 객체의 값을 설정해준다. 라인 중심에서 처음 시작하는 것을 전제 조건으로 하기 때문에 포인트 객체의 초기 값은 전처리 된 frame 객체의 중심이다.

findMinIndex 함수를 통해 따라가는 객체의 인덱스를 구하고 포인트 객체 값을 재설정 해준다.

drawBoundingBox 함수를 사용하여 객체에 바운딩 박스를 그린다. (빨강 : 따라가는 라인, 파랑 : 다른 라인 , 노랑 : 노이즈) 객체가 검출되지 않는 경우 객체의 마지막 무게 중심을 그려준다

![image](https://github.com/user-attachments/assets/b6b88c3a-993c-47b4-9244-43960dcbc2da)

getErr함수를 사용하여 에러값을 구해주고 구한 값을 각 vel 값에서 빼주어서 각 vel 값을 구한다 rvel 같은 경우 모터가 반대로 달려있기 때문에 RMP값에 -1을 곱하여 설정해준다.

앞에서 구한 vel 값을 dxl.setVelocity를 통하여 모터에 값을 준다.('s'를 입력하여 mode가 true인 경우)

시간 측정을 멈추고 ctrl_c_pressed가 true면 코드를 종료한다

이후  pc로 전처리한 프레임을 전송한 후 에러값, 각 vel값 , 측정한 시간(tm 객체)을 출력한다.


코드 종료시

![image](https://github.com/user-attachments/assets/2f123118-85b8-4369-8696-52c639972f63)


앞에 코드가 정상적으로 작동하여 while문이 종료 되었다면 dxl.close()를 해주고 코드를 종료한다.(없을시 코드가 종료되어서 모터는 계속 회전함)

Makefile

![image](https://github.com/user-attachments/assets/f958eca2-5bb7-41ca-bef7-da9191b814f9)












