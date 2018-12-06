# Install OpenCV on Ubuntu16.04

## Required Packages

* GCC 4.4.x or later
* CMake 2.8.7 or higher
* Git
* GTK+2.x or higher, including headers (libgtk2.0-dev)
* pkg-config
* Python 2.6 or later and Numpy 1.5 or later with developer packages (python-dev, python-numpy)
* ffmpeg or libav development packages: libavcodec-dev, libavformat-dev, libswscale-dev
* [optional] libtbb2 libtbb-dev
* [optional] libdc1394 2.x
* [optional] libjpeg-dev, libpng-dev, libtiff-dev, libjasper-dev, libdc1394-22-dev
* [optional] CUDA Toolkit 6.5 or higher

The packages can be installed using a terminal and the following commands or by using Synaptic Manager:

```bash
[compiler] sudo apt-get install build-essential
[required] sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
[optional] sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

## Getting OpenCV Source Code

You can use the latest stable OpenCV version or you can grab the latest snapshot from our [Git repository](https://github.com/opencv/opencv.git).

### Getting the Latest Stable OpenCV Version

* Go to our [downloads page](http://opencv.org/releases.html).
* Download the source archive and unpack it.

### Getting the Cutting-edge OpenCV from the Git Repository

Launch Git client and clone [OpenCV repository](http://github.com/opencv/opencv). If you need modules from [OpenCV contrib repository](http://github.com/opencv/opencv_contrib) then clone it as well.

For example

```bash
cd ~/<my_working_directory>
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

### Building OpenCV from Source Using CMake

1. Create a temporary directory, which we denote as `<cmake_build_dir>`, where you want to put the generated Makefiles, project files as well the object files and output binaries and enter there.

    For example

    ```
    cd ~/opencv
    mkdir build
    cd build
    ```
  
2. Configuring. Run `cmake [<some optional parameters>] <path to the OpenCV source directory>`

    For example

    ```bash
    cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
    ```

    In my situation, I used follow command, set **`BUILD_TIFF=ON`** to avoid `LIBTIFF_4.0 link errors`, [see more about this error](http://answers.opencv.org/question/35642/libtiff_40-link-errors/)

    ```bash
     cmake -D CMAKE_BUILD_TYPE=Release \
           -D CMAKE_INSTALL_PREFIX=/usr/local \
           -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
           -D BUILD_DOCS=ON \  # if you don't need doc, delete this
           -D BUILD_EXAMPLES=ON \  # if you don't need doc, delete this
           -D BUILD_TIFF=ON ..
    ```

    or cmake-gui

    * set full path to OpenCV source code, e.g. /home/user/opencv
    * set full path to `<cmake_build_dir>`, e.g. /home/user/opencv/build
    * set optional parameters
    * run: “Configure”
    * run: “Generate”

> **Note**
Use `cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local ..` , without spaces after -D if the above example doesn't work.

3. Description of some parameters
   
    * build type: `CMAKE_BUILD_TYPE=Release\Debug`
    * to build with modules from opencv_contrib set `OPENCV_EXTRA_MODULES_PATH` to `<path to opencv_contrib/modules/>`
    * set `BUILD_DOCS` for building documents
    * set `BUILD_EXAMPLES` to build all examples
  
4. [optional] Building python. Set the following python parameters
    * **PYTHON2(3)_EXECUTABLE** = `<path to python>`
    * **PYTHON_INCLUDE_DIR** = `/usr/include/python<version>`
    * **PYTHON_INCLUDE_DIR2** = `/usr/include/x86_64-linux-gnu/python<version>`
    * **PYTHON_LIBRARY** = `/usr/lib/x86_64-linux-gnu/libpython<version>.so`
    * **PYTHON2(3)_NUMPY_INCLUDE_DIRS** = `/usr/lib/python<version>/dist-packages/numpy/core/include/`

5. [optional] Building java.

    * Unset parameter: **BUILD_SHARED_LIBS**
    * It is useful also to unset **BUILD_EXAMPLES**, **BUILD_TESTS**, **BUILD_PERF_TESTS** - as they all will be statically linked with OpenCV and can take a lot of memory.

6. Build. From build directory execute make, it is recommended to do this in several threads

    For example

    ```bash
    make -j7 # runs 7 jobs in parallel
    ```
    
    This will cost a lot of time.

7. [optional] Building documents. Enter `<cmake_build_dir/doc/>` and run make with target "doxygen"

    For example

    ```bash
    cd ~/opencv/build/doc/
    make -j7 doxygen
    ```

    I get error when executing this commend, but you can download doxygen from [here](https://docs.opencv.org/)

8. To install libraries, execute the following command from build directory

    ```bash
    sudo make install
    ```

9. [optional] Running tests

    * Get the required test data from OpenCV extra repository.
    
    For example

    ```bash
    git clone https://github.com/opencv/opencv_extra.git
    ```

    > **Attention**: this repo is large, clone is very very slow....terriable! Maybe you should download from git repository

    * set OPENCV_TEST_DATA_PATH environment variable to `<path to opencv_extra/testdata>`.
    * execute tests from build directory.

    For example

    ```bash
    <cmake_build_dir>/bin/opencv_test_core
    ```

> **Note**
If the size of the created library is a critical issue (like in case of an Android build) you can use the install/strip command to get the smallest size possible. The stripped version appears to be twice as small. However, we do not recommend using this unless those extra megabytes do really matter.
