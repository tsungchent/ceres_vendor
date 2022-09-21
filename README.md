ceres catkin wrapper
=====

This repository contains ament files to checkout and build ceres and its dependencies.
In order to use the ceres covariance computation, you must use an PIC version of suitesparse:

* Check out the following repositories:
  * https://github.com/tsungchent/suitesparse_vendor.git
  * https://github.com/tsungchent/glog_vendor.git
  * https://github.com/tsungchent/gflags_vendor.git
  * https://github.com/tsungchent/eigen_vendor.git
