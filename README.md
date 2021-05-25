# This is an auto installer [tensorflow,opencv,cuda11] script : 
`copy the code from here , and add the +x permission to run it with ./ `
```bash
#!/bin/bash
# auth : Omar Benabdejlil 

red="\033[1;31m"
green="\033[1;32m"
yellow="\033[1;33m"
blue='\033[0;34m'  
purple='\033[1;35m'
cyan='\033[1;36m'  
off="\033[0m"
user=$( echo $USER )

function cuda(){
echo -e"${blue} downloading dependencies .. ${off}"
sleep 1
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.3.1/local_installers/cuda-repo-ubuntu2004-11-3-local_11.3.1-465.19.01-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-11-3-local_11.3.1-465.19.01-1_amd64.deb
sudo apt-key add /var/cuda-repo-ubuntu2004-11-3-local/7fa2af80.pub
sudo apt-get update
echo -e "${red}[+] installing cuda 11 ..${off}"
sudo apt-get -y install cuda


}

function opencv() {

echo -e "${blue}[+] instaling opencv ..${off}"
sleep 1
sudo apt update
sudo apt install libopencv-dev python3-opencv

python3 -c "import cv2; print(cv2.__version__)"
   if [[ $? -ne 0 ]]; then
   echo -e "${green}[+]the bib is imported ! ${off}"
   else
   echo -e "${red}[-]Watch out ! there is a problem ! ${off}"

   fi

   sleep 1 
echo -e "${red}[+] installing dependencies ..${off}"

    sudo apt install build-essential cmake git pkg-config libgtk-3-dev \
    libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
    libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
    gfortran openexr libatlas-base-dev python3-dev python3-numpy \
    libtbb2 libtbb-dev libdc1394-22-dev libopenexr-dev \
    libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev

    echo -e "${blue}[+] Clone the OpenCV’s and OpenCV contrib repositories: ..${off}"
    sleep 1 
    mkdir ~/opencv_build && cd ~/opencv_build
    git clone https://github.com/opencv/opencv.git
    git clone https://github.com/opencv/opencv_contrib.git

    echo -e "${red}[+]create temporary build directory and navigate to it ..${off}"

    cd ~/opencv_build/opencv
    mkdir -p build && cd build
    
    
echo -e "${yellow}[!]-Set up the OpenCV build with CMake ! ${off}"
sleep 1

#--checking CMAKE tool ! --
echo -e "${cyan}[!] checking cmake tool ..${off}"
sleep 1
if ! which cmake > /dev/null; then
   echo -e "${red}[-]cmake is not installed ! want to Install? (y/n) \c ${off}"
   read REPLY
   if "$REPLY" = "y"; then
    sudo apt-get install build-essential libssl-dev
    cd /tmp
    wget https://github.com/Kitware/CMake/releases/download/v3.20.0/cmake-3.20.0.tar.gz
    tar -zxvf cmake-3.20.0.tar.gz
    cd cmake-3.20.0
    ./bootstrap
    make
    sudo make install
    cmake --version
    sleep 3 && clear

   fi
fi

cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_C_EXAMPLES=ON \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_GENERATE_PKGCONFIG=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_build/opencv_contrib/modules \
    -D BUILD_EXAMPLES=ON ..

    echo -e "${blue}[!]-start compilation precess .. ${off}"
    sleep 1
    make -j8
    echo -e "${red}[+]-installing opencv.. ${off}"

    sudo make install

    }
function tensorflow(){
    if ! which python3 > /dev/null; then
   echo -e "${red}[-]python3 is not installed ! want to Install? (y/n) \c ${off}"
   read REPLY
   if "$REPLY" = "y"; then
    sudo apt-get install python3
    sleep 2 && clear

   fi
fi

    echo -e "${blue}[+]install python 3 .. ${off}"
    sudo apt install python3-venv python3-dev
    mkdir my_tensorflowcd my_tensorflow
    python3 -m venv venv
    source venv/bin/activate
    pip install --upgrade pip
    pip install --upgrade TensorFlow
echo -e "${blue}[!]verify the installation .. ${off}"
    python -c 'import TensorFlow as tf; print(tf.__version__)'
    deactivate
}


while [ "$1" != "" ]; do
    PARAM=`echo $1 | awk -F= '{print $1}'`

    case $PARAM in
     -h | --help)
    
echo -e ""
echo -e "${purple}+------------------------------------------------------------------------------------+${off}
-h  | --help :${blue}Help menu.${off}
-o  | --opencv :${blue}install opencv only .${off}
-t  | --tensorflow :${blue}install tensorflow only .${off}
-c  | --cuda:${blue}install cuda 11 only .${off}
-a  | --all:${blue}install all features ${off}[${yellow}cuda${off},${yellow}tensorflow${off},${yellow}opencv${off}]${off}
${purple}+------------------------------------------------------------------------------------+${off}
${cyan}                                                                Auth : Omar Benabdejlil ${off}
"   
    ;;

    -o  | --opencv)
   

   opencv

     ;;

     -t | --tensorflow)
      tensorflow
     ;;

      -c | --cuda) 
     cuda

      ;;

      -a  | --all)
        tensorflow
        opencv
        cuda
      ;;
        *)
            echo -e "${red}[-]- ERROR: unknown parameter \"$PARAM\" ${off}"
         
            exit 1
            ;;
    esac
    shift
done
```
