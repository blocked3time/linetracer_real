젯슨과 opencv를 활용한 라인 트레이서 

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
찾은 객체의 거리가 MINDISTANCE보다 큰 경우 값을 
