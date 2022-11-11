## Aware Client DAM

### Overview

A ThreadX DAM (downloadable application module) to establish CoAP (Constrained Application Protocol) communication between Aware 9205 Device and the Aware Cloud.

### Prerequisites

1. Foxconn MDM9205 Hardware
2. [MDM920x ThreadX Cellular IoT SDK](https://createpoint.qti.qualcomm.com/dashboard/public/productkit#public/product-kit/search)
3. [Snapdragon LLVM 4.0.x Compiler](https://www.qualcomm.com/support/software-tools/qualcomm-snapdragon-llvm-arm-toolchain-for-windows/4d3201e9-bc33-468b-b086-3fb0ca985f5c/overview)
4. [Python 2.7.9](https://www.python.org/downloads/release/python-279/)
5. [QFLOG - Qualcomm Flashing and Logging Tool](https://createpoint.qti.qualcomm.com/tools/#suite/4562/62181)

### Configure the Batch Script Variables

Ensure that the LLVM toolchain and Python installation path is correctly updated in the build_aware_app.bat script

```
set LLVM_ROOT_PATH= <~LLVM>\4.0.3
set PYTHON_PATH=C:\Python27\python.exe
```

### Compile the Application

To compile the source files and link object files to libraries in SDK/common lib folder, run the build_aware_app.bat script from command prompt.

- If the compilation is successful, the message "Aware Client DAM is built at location" is printed.

- If the compilation fails, all errors are displayed in the command window.


### Device setup Instructions
1.	Ensure that the latest build of ThreadX (TX.2.1) is loaded. LTE IoT SDK works only with TX.2.1 and later versions.
2.	Set NV#65768 to 82 and reboot the device to use SER5 COM port. This is required to enable QFLOG tool for flashing and debugging.
3.	To find the SER5 COM port number, navigate to Device Manager from the windows machine.
4.	Push the /datatx/qflog_config file in alternate file system via PCAT - EFS explorer and reset the device. This text file contains numerical value 1 content.
5.	Concurrent WWAN and GNSS operations are not supported on MDM9205 devices, an arbitrator is designed to arbitrate concurrencies between WWAN and GNSS operations. NV#74188 is used to set the initial configuration of app priority.
-	Byte1: version = 1
-	Byte2: marb_app_priority = 0 (0 – GNSS, 1 - WWAN)
![GNSS NVRAM Parameter Setup](https://user-images.githubusercontent.com/104608589/201330586-5d214c38-c567-4c64-85b6-134e93092c0f.png)
6.	Ensure to copy dss.conf in /location/dss.conf folder. This configuration file is used to indicate the WWAN data connection parameters for downloading the GNSS assistance service, XTRA assistance data and NTP download
7.	Ensure to copy location.conf in /location/location.conf folder. The primary configuration file /location/location.conf contains all the essential location configuration items



### Sideload the Application

After the application is successfully compiled, the aware_client_v1.bin file is generated in the bin folder.

To sideload the aware client dam onto the device, run the following QFLOG command with the PUSH

```
QFLOG.py -p <COM_PORT> PUSH -f <absolute bin path>
```

In order to start the aware client on bootup, push oem_app_path.ini file that contains the path of the binary

1. Create a oem_app_path.ini file
2. Add the name of the binary in this format to the file: aware_client_v1.bin
3. Copy the oem_app_path.ini file to the device by running the following QFLOG command and reboot the device

```
QFLOG.py -p <COM_PORT> PUSH -f <absolute oem_app_path.ini path>
```

4. To View the aware client DAM logs, run the following QFLOG command

```
QFLOG.py -p <COM_PORT> VIEW_LOGS
```
