# jetson-ffmpeg
L4T Multimedia API for ffmpeg

**1.build and install library**

    git clone https://github.com/jocover/jetson-ffmpeg.git
    cd jetson-ffmpeg
    mkdir build
    cd build
    cmake ..
    make
    sudo make install
    sudo ldconfig
	
**2.patch ffmpeg and build**

    git clone git://source.ffmpeg.org/ffmpeg.git -b release/4.2 --depth=1
    cd ffmpeg
    wget https://github.com/jocover/jetson-ffmpeg/raw/master/ffmpeg_nvmpi.patch
    git apply ffmpeg_nvmpi.patch
    ./configure --enable-nvmpi
    make

**3.using**

### Supports Decoding
  - MPEG2
  - H.264/AVC
  - HEVC
  - VP8
  - VP9
  
**example**

    ffmpeg -c:v h264_nvmpi -i input_file -f null -
	
### Supports Encoding
  - H.264/AVC
  - HEVC
  
**example**

    ffmpeg -i input_file -c:v h264_nvmpi <output.mp4>

**example HD**

    #H264 encode using nvmpi [speed=4.7x (180% CPU) NVenc 192-512MHz, NVdec OFF]
    ffmpeg -i input_file.mpg -c:v h264_nvmpi output.mp4

    #MPEG2 decode, H264 encode (both using nvmpi) [speed=3.1x (130% CPU) NVenc 716MHz, NVdec 716MHz]
    ffmpeg -c:v mpeg2_nvmpi -i input_file.mpg -c:v h264_nvmpi output.mp4

    #MPEG2 decode, H264 encode (both using nvmpi) croping HD
    ffmpeg -c:v mpeg2_nvmpi -i input_file.mpg -c:v h264_nvmpi -vf crop=1920:800:0:140 -vb 2048k -map 0:1 -map 0:2 -map 0:3 -map 0:4 output.mp4

note: `-vb` little less than specified, e.g. `-vb 2048k` get `1700 kb/s`, put `-vb 2500k` get `2000 kb/s`

** speed **

- single FFmpeg: 4.8x (ARM => h264_nvmpi), 3.2x (mpeg2_nvmpi => h264_nvmpi)
- two    FFmpeg (mpeg2_nvmpi => h264_nvmpi): 2.5x each, i.e. 5.2x real in //
- three  FFmpeg (mpeg2_nvmpi => h264_nvmpi): 1.9x each, i.e. 5.8x real in //
- four   FFmpeg (mpeg2_nvmpi => h264_nvmpi): 1.4x each, i.e. 5.9x real in //


