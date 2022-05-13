# raspberrypi-opencv
## 1. 기존 OpenCV 버전 제거

아래처럼 보이면 Opencv 이전 버전이 설치가 않되어 있는 상태이다.
```
pi@raspberrypi~$ pkg-config --modversion opencv
긴 말이 나오면 됨
```

## 2. OpenCV 컴파일 전 필요한 패키지 설치

build-essential 패키지는 C/C++컴파일러와 관련 라이브러리, make같은 도구들이 포함되어 있다.
cmake는 컴파일 옵션이나 빌드된 라이브러리에 포함시킬 OpenCV 모듈 설정등을 위해 필요하다.
```
~$ sudo apt install build-essential cmake
```

특정 포맷의 이미지 파일을 불러오거나 저장하기 위해 필요한 패키지들
```
~$ sudo apt install libjpeg-dev libtiff5-dev libjasper-dev libpng-dev
```

특정 코덱의 비디오 파일/스트리밍을 읽어오거나 기록하기 위해 필요한 FFmpeg 관련 패키지들
```
~$ sudo apt install libavcodec-dev libavformat-dev libswscale-dev libxvidcore-dev libx264-dev libxine2-dev
```

Video4Linux 패키지는 리눅스에서 실시간 비디오 캡쳐를 지원하기 위한 디바이스 드라이버와 API를 포함
```
~$ sudo apt install libv4l-dev v4l-utils
```

특정 코덱의 비디오 파일/스트리밍을 읽어오거나 기록하기 위해 필요한 GStreamer 관련 패키지
```
~$ sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

OpenCV에서는 highgui 모듈을 사용하여 자체적으로 윈도우 생성하여 이미지나 영상을 보여준다.
윈도우 생성 등의 GUI를 위해 gtk 또는 qt를 선택해서 사용가능한다. 글에서는 gtk2를 사용한다.
```
~$ sudo apt install libgtk2.0-dev
```

그외 선택 가능한 패키지, QT를 사용할려면 cmake 옵션에 WITH_GTK 대신에 WITH_QT를 추가해야 한다.
```
libtk-3-dev
libgtk4-dev
libgtk5-dev
```

OpenGL 지원하기 위해 필요한 라이브러리
```
~$ sudo apt install mesa-utils libgl1-mesa-dri libgtgl2.0-dev libgtkglext-dev
```

OpenCV 최적화를 위해 사용되는 라이브러리들
```
~$ sudo apt install libatlas-base-dev gfortran libeigen3-dev
```

python2.7-dev와 python3-dev패키지는 파이썬을 위한 헤더파일과 라이브러리가 포함된 패키지들
Numpy는 매트릭스 연산등을 빠르게 처리할 수 있어서 OpenCV Python에서 사용
```
~$ sudo apt install python3-dev python3-numpy
```

## 3.OpenCV 설정과 컴파일 및 설치
소스 코드를 저장할 임시 디렉토리를 생성하여 이동 후 진행
```
~$ mkdir opencv
~$ cd opencv
~/opencv $
```

OpenCv 4.5.1 소스코드를 다운로드 받아 압축을 풀어준다.
```
~$ wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
~$ unzip opencv.zip
```

opencv_contrib(extra modules) 소스코드를 다운로드 받아 압축을 푼다.
SURF 등을 사용하기 위해 필요
```
~$ wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
~$ unzip opencv_contrib.zip
```

다음처럼 두 개의 디렉토리가 생성
```
~/opencv $ ls -d*/
opencv-4.5.1 opencv_contrib-4.5.1
```

opencv-4.5.1 디렉토리로 이동하여 build 디렉토리를 생성하고 build 디렉토리로 이동
컴파일은 build 디렉토리에서 이룬다.
```
/opencv $ cd opencv-4.5.1
/opencv-4.5.1 $ mkdir build
/opencv-4.5.1 $ cd build
/build $
```

cmake를 사용하여 OpenCV 컴파일 설정을 해준다. 복사해서 터미널에 붙여넣기 해준다.
다음 옵션은 빌드할 OpenCV 버전에 맞추어 아래 경로중 "4.5.1" 부분을 변경한다.
-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.5.1/modules
```
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=OFF -D WITH_IPP=OFF -D WITH_1394=OFF -D BUILD_WITH_DEBUG_INFO=OFF -D BUILD_DOCS=OFF -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=OFF -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D ENABLE_NEON=ON -D ENABLE_VFPV3=ON -D WITH_QT=OFF -D WITH_GTK=ON -D WITH_OPENGL=ON -D OPENCV_ENABLE_NONFREE=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.5.1/modules -D WITH_V4L=ON -D WITH_FFMPEG=ON -D WITH_XINE=ON -D ENABLE_PRECOMPILED_HEADERS=OFF -D BUILD_NEW_PYTHON_SUPPORT=ON -D OPENCV_GENERATE_PKGCONFIG=ON ../
```

다음처럼 cmake 실행 중에 추가적인 다운로드가 있다.
라즈베리파이에 인터넷이 연결된 상태에서 진행
```
-- xfeatures2d/boostdesc: Download: boostdesc_bgm.i
-- xfeatures2d/boostdesc: Download: boostdesc_bgm_bi.i
-- xfeatures2d/boostdesc: Download: boostdesc_bgm_hd.i
-- xfeatures2d/boostdesc: Download: boostdesc_binboost_064.i
-- xfeatures2d/boostdesc: Download: boostdesc_binboost_128.i
-- xfeatures2d/boostdesc: Download: boostdesc_binboost_256.i
-- xfeatures2d/boostdesc: Download: boostdesc_lbgm.i
-- xfeatures2d/vgg: Download: vgg_generated_48.i
-- xfeatures2d/vgg: Download: vgg_generated_64.i
-- xfeatures2d/vgg: Download: vgg_generated_80.i
-- xfeatures2d/vgg: Download: vgg_generated_120.i
-- data: Download: face_landmark_model.dat
```

다음과 같은 메시지가 보이면 정상적으로 된 것이다.
```
-- Configuring done
-- Generating done
-- Build files have been written to: /home/pi/opencv/opencv-4.5.1/build
```

진행하기 전에 스왑(swap) 공간을 늘려줘야 멀티코어를 사용하여 컴파일시 메모리 부족으로 에러가 나지 않습니다.
( 참고 https://www.pyimagesearch.com/2017/10/09/optimizing-opencv-on-the-raspberry-pi/ )

/etc/dphys-swapfile 파일을 열어서 
```
~$ sudo nano /etc/dphys-swapfile
```
CONF_SWAPSIZE 변수값을 100에서 2048로 수정한다.
Ctrl+O를 눌러 저장하고 Ctrl+X를 눌러 빠져나온다.
```
# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
# CONF_SWAPSIZE=100
CONF_SWAPSIZE=2048
```
스왑 서비스 재시작하여 변경된 설정을 반영시켜주면 스왑 크기가 대략 20배가 된다.
현재는 Swap이 100메가이다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ free
               total        used        free      shared  buff/cache   available
Mem:         3930860      179928     1751316       36172     1999616     3554424
Swap:         102396           0      102396
```

스왑 관련 서비스를 재시작한다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ free
               total        used        free      shared  buff/cache   available
Mem:         3930860      179928     1751316       36172     1999616     3554424
Swap:         102396           0      102396
```

Swap이 2048메가로 바뀐다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ free
               total        used        free      shared  buff/cache   available
Mem:         3930860      181864      109168       36872     3639828     3551788
Swap:        2097148           0     2097148
```

이제 make 명령을 사용하여 컴파일을 시작한다. -j4 옵션을 주고 우선 진행한다.
```
~$ time make -j4
```

Raspberry Pi에 방열판을 달았을 뿐인데 멈춤없이 진행이 된다.
```
real    66m51.708s
user    227m4.180s
sys     15m52.754s
```
혹, python라이브러리 빌드하는 부분에서 오랫동안 멈춤현상이 있는 경우, Ctrl+C를 눌러 중지하고
make 명령으로 다시 진행해서 완료하면 된다.
```
~$ make
```
*
이제 컴파일 결과물을 설치한다.
```
~$ sudo make install
```
샘플 코드들은 /usr/local/share/opencv4/samples/ 위치에 복사된다.
opencv  라이브러리를 찾을 수 있도록 다음 명령을 실행한다. 
```
~$ sudo ldconfig
```

/etc/dphys-swapfile 파일을 열어서 
```
~$ sudo nano /etc/dphys-swapfile
```

CONF_SWAPSIZE 변수값을 다시 100으로 수정한다.
```
# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
CONF_SWAPSIZE=100
```

스왑 서비스 재시작하여 변경된 설정을 반영시켜주면 스왑 크기가 원래대로 돌아온다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ sudo /etc/init.d/dphys-swapfile restart
[ ok ] Restarting dphys-swapfile (via systemctl): dphys-swapfile.service.

pi@raspberrypi:~/opencv/opencv-4.5.1/build $ free
               total        used        free      shared  buff/cache   available
Mem:         3930860      181020     2235192       36256     1514648     3556824
Swap:         102396           0      102396
```

## 4.OPenCV 설치 결과 확인 
Opencv 4.x에서는 옵션으로 opencv대신에 opencv4를 사용하면 pkg-config를 사용하여 컴파일할 수 있다.

설치시 Opencv 4.5.1에서 GStreamer를 디폴트로 사용하게 되었는데 문제가 있어서 수정을 했다.
다음 경고가 나오면서 영상이 보이지 않았다.
```
[ WARN:0] global /home/pi/opencv/opencv-4.5.1/modules/videoio/src/cap_gstreamer.cpp (935) open OpenCV | GStreamer warning: Cannot query video position: status=0, value=-1, duration=-1
```

현재 위치로 코드를 가져옵니다. 
```
~$ cp  /usr/local/share/opencv4/samples/cpp/videocapture_basic.cpp .
```

파일을 열어서 다음 부분을 수정합니다. Ctrl + O를 눌러 저장하고  Ctrl + X를 눌러 빠져옵니다. 
```
$  nano videocapture_basic.cpp
int apiID = cv::CAP_V4L2;
```
빌드한다.
```
$ g++ -o videocapture_basic videocapture_basic.cpp $(pkg-config opencv4 --libs --cflags)
```

테스트를 하기 위해 필요한 웹캠 또는 Raspbery Pi Camera(pi camera)를 Raspberry에 연결한다.

대부분의 웹캠은 연결후 dmesg|tail 명령어로 해당 웹캠을 확인시 다음처럼 문제없이 인식이 된다.
인식이 안되면 해당 웹캠을 위한 디바이스 드라이버를 설치해줘야 한다.

/dev/video0 디바이스 파일이 생성되었는지 확인
```
~$ ls/dev/video*
/dev/video0
```

파이 카메라의 경우에는 다음 명령으로 /dev/video0 장치에 만들어줘야 한다.
```
sudo modprode bcm2835-v4l2
```
파이 카메라의 경우 추가로 소스코드를 수정해야 한다. open함수의 아규먼트를 -1로 해야 정상적으로 동작한다. 0으로 하면
카메라를 열지 못하고 무한 대기 상태에 빠지게 된다.
```
//cap/open(deviceID+apiID);
cap.open(-1)
```

터미널 프로그램으로 MobaXterm을 사용하면 SSH 접속 상태에서 실행해보면 바로 실행 결과를 확인 가능한다.

실행시켜보면 웹캠 영싱이 보인다.
```
~$ ./videocapture_basic
```

python 3에서 opencv라이브러리를 사용가능한지는 확인한다.
OpenCv버전이 출력되어야 한다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ python3
Python 3.9.2 (default, Mar 12 2021, 04:06:34)
[GCC 10.2.1 20210110] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'4.5.1'
>>> quit()
```

예제 코드를 실행한다. 웹캠 영상을 볼수있다.
```
$ python3 /usr/local/share/opencv4/samples/python/video.py
```
C++때와 같이 에러가 나지만 영상은 보인다.
```
[ WARN:0] global /home/pi/opencv/opencv-4.5.1/modules/videoio/src/cap_gstreamer.cpp (935) open OpenCV | GStreamer warning: Cannot query video position: status=0, value=-1, duration=-1
```

직접 예제를 작성하면 코드가 더 간단해지지만 Opencv에서 있는 것으로 진행한다.(수정)
```
$ cp /usr/local/share/opencv4/samples/python/video.py .
$ cp /usr/local/share/opencv4/samples/python/tst_scene_render.py .
$ cp /usr/local/share/opencv4/samples/python/common.py .
$ chmod +w video.py
```

video.py파일을 열어서
```
~$ nano video.py
```

188번째 줄을 당므처럼 수정한다. Ctrl+O로 저장한 후, Ctrl+X로 종류한다.
```
  cap=cv.VideoCapture(source, cv.CAP_V4L2)
```

경고문 없이 영상이 바로 보인다.
```
~$ python video.py
~$ python3 video.py
```
끄는 방법은 Ctrl+c

이제 필요 없어진 컴파일에 사용했던 opencv소스코드 디렉토리를 삭제한다.
```
pi@raspberrypi:~/opencv/opencv-4.5.1/build $ cd
pi@raspberrypi:~ $ rm -rf opencv
```
다시 키는 법
```
mkdir opencv
cd opencv
cd opencv-4.5.1
mkdir build
cd build
```
끄는 법
```
cd
rm -rf opencv
```

끝
