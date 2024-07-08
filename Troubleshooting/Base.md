# Base Trouble shooting
gstreamer python binding으로 애플리케이션을 빌드하면서 겪었던  
오류들의 원인과 해결방법들에 대해서 정리한 목록입니다.  
Plug-in에 대한 오류들은 Plug-in.md에서 다룹니다.

## Environment
1. PyGObject, Python, Gstreamer 버전 문제

`: GStreamer-CRITICAL **: 13:50:27.161: gst_mini_object_unref: assertion 'mini_object != NULL' failed`

Gstreamer python binding으로 gstreamer pipeline을 빌드할 때
PyGObject라는 객체를 사용한다.  

PyGObject의 버전과 Python 버전, gstreamer 버전의 호환성을 체크한 뒤
빌드하는 것이 권장된다.  

호환성을 지키지 않았을 경우 위와 같은 gst_object 레벨에서 에러가 발생하는데  
이는 파이썬 애플리케이션 레벨에서는 해결이 불가능함.

2. Container 빌드시 libffi Import 오류  
`ImportError: libffi.so.6: cannot open shared object file: No such file or directory`
Python runtime이 원하는 libffi버전과 Ubuntu22.04 LTS에 설치된 libff 버전이 달라서 생긴 오류.
🔖 높은 버전의 lib를 가르키도록 symlink를 걸어주면 된다.  
🔖 낮은버전의 lib symlink → 높은 버전의 lib  
https://stackoverflow.com/questions/61875869/ubuntu-20-04-upgrade-python-missing-libffi-so-6  


## Element 

1. linking 에러
`: GStreamer-CRITICAL **: 13:50:27.574: gst_pad_query_caps: assertion 'GST_IS_PAD (pad)' failed`

Element간 링크가 되어있지 않으면 발생


## GMainloop
1. GMainloop의 메인 스레드 점유 문제
pipeline을 만들 때 GMainloop를 꼭 써야하는 것은 아니지만,   
하나의 프로세스에서 쓰게된다면 SubThread로 분리하여 메인 스레드를 점유하지 않게 해야함.


## Callback
1. FileIO callback 함수의 Permission Denied
시그널에 연결된 callback함수의 동작에 파일 접근이 포함된 경우,  
파일 접근 권한에 따라 거부될 수 있음.  
Docker container환경이라면 마운팅된 디렉토리에 rwx등의 권한을 부여한다.

2. FileIO callback 함수의 File Write 경로 오류  
`0:00:01.099616517   277 0x7ea5b40044f0 WARN           multifilesinkgstmultifilesink.c:810:gst_multi_file_sink_write_buffer:<multifilesink2> error: Error while writing to file.`  
  미리 디렉토리를 구성한다.  
