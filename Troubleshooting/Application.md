# Media
미디어 스트리밍 상에서 생긴 문제들의 원인과 해결방법을 설명.  
영상 코덱에 대한 선행 지식이 좀 필요하다,

1. Pixel 계단현상, 인코딩 불안정  

`x264enc :0::<x264enc> non-strictly-monotonic PTS`    
`x264enc :0::<x264enc> VBV underflow (frame 2159, -22724688 bits)`


Encoding 결과물 불안정

https://gstreamer-devel.narkive.com/LJuu7yM3/dynamic-pipeline-non-strictly-monotonic-pts-warning-with-x264enc-after-switching-source
-> re-timestamping.

https://gstreamer.freedesktop.org/documentation/x264/index.html?gi-language=c#x264enc-page
https://gstreamer-devel.narkive.com/LJuu7yM3/dynamic-pipeline-non-strictly-monotonic-pts-warning-with-x264enc-after-switching-source
https://gstreamer-devel.narkive.com/vblWYGxi/best-settings-for-x264enc-for-a-live-stream

x264enc 인코딩 속도가 상대적으로 Feeding속도보다 빨라서

bufferunderflow를 유발하며 충분하지 못한 데이터로 인코딩 영상을 만들어서 깨짐.

element property를 조절하든, 중간에 버퍼를 설치하든 해서 속도를 조정해야함.

공급 속도와 소비 속도를 조절해야함.

네트워크 및 영상 소스의 제원에 따라 달라질 수 있어서 여러 환경에서의 테스트가 필요.


2. Ignoring DTS

`mpegtsmux mpegtsmux.c:1318:mpegtsmux_clip_inc_running_time:mpegtsmux:sink_65 ignoring DTS going backward`
h264가 아닌 raw 포멧으로 변횐되었다가 복원되면서 timestamp정보 유실  
