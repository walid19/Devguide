# EKF2 sdlog2 log replay \(superseded\)

The method described by this page cannot be used for PX4 Firmware version after commit 75f6b4eb0c8f1dc4a348b0d4160b0fdf32459cf8 and has been superseded by the method described in the [System-wide Replay topic.](https://dev.px4.io/advanced-replay.html#ekf2-replay "System-wide replay topic.") 

This page shows you how you can tune the parameters of the EKF2 estimator by using the replay feature on a real .px4log flight log created using the sdlog2 program.

## Introduction

A developer has the possibility to do replay on particularly logged data for estimation analysis. The remainder of this page will explain which parameters have to be set in order to benefit from this feature and how to correctly deploy it.

## sdlog2 logger \(.px4log\)

### Prerequisites

* set the parameter **SYS\_LOGGER** to sdlog2 \(default\) and reboot vehicle \(0 = sdlog2 and 1 = ulog\).
* set the parameter **EKF2\_REC\_RPL** to 1. This will tell the estimator to publish **special** replay messages for logging. 
* if available, set the parameter **SDLOG\_PRIO\_BOOST** to a value contained in the set {0, 1, 2, 3}. A value of 0 means that the onboard logging app has a default \(low\) scheduling priority. A low scheduling priority can lead to a loss of logging messages. If you find that your log file contains 'gaps' due to skipped messages then you can increase this parameter to a maximum value of 3. Testing has shown that a minimum value of 2 is required in order to avoid loss of data.

### Deployment

Once you have a real flight log \(.px4log\) created with the above settings then you can **run** a replay on it by using the following command in the root directory of your PX4 Firmware:

```
make posix_sitl_replay replay logfile=<absolute_path_to_log_file>/my_log_file.px4log
```

Once the command has executed check the terminal for the location and name of the replayed log file.   
This file should be located in

```
<path to Firmware>/build_posix_sitl_replay/src/firmware/posix/rootfs/
```

The output replayed file is called **replay\_replayed.px4log**, which can be used to analyze the estimator performance.

### Changing tuning parameters for a replay

When you run the replay the first time, a replay\_replayed.px4log file will be generated using the default EKF2 parameter values from the actual flight.   
After that you can change any EKF2 parameter in **replay\_params.txt**, which is located in the same directory as your output file.

For example setting the noise value for the gyro bias would require the following line.

```
EKF2_GB_NOISE 0.001
```

Once some EKF2 parameters have been changed, a new replay\_replayed.px4log file can be generated using the same command given in [Deployment](deployment).

