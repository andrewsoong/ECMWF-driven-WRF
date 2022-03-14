# ECMWF-driven-WRF
Using ECMWF prediction pruducts to drive WRF-ARW

Here,We use ECMF data to run WRF-ARW.These data were downloaded from CMA（CMACAST） and this datasets was not got for free.
Note that，You need to install Grib——API from ECMWF with the support of Netcdf and Jasper！
And Grib-API-V1.10.4 is recommended！！！
How to install grib-api ？

This tool is from ECMWF（you can download it from here：https://software.ecmwf.int/wiki/display/GRIB/Home）.
Install like this：
  > tar zxvf grib_api-1.10.4.tar.gz
  > mkdir /usr/local/grib_api
  > cd grib_api-1.10.4
  > ./configure --prefix=/usr/local/grib_api --with-netcdf=/usr/local/netcdf --disable-jpeg
  > make
  > make check
  > make install

and edit .bashrc：
export GRIB_API_HOME=/usr/local/grib_api
export PATH=$GRIB_API_HOME/bin:$PATH
save，and source it.


1，How to transfer ECMF data ：

grib_set -r -s packingType=grid_simple W_NAFP_C_ECMF_20150428054553_P_C1D04280000042800001.bin 042800.grb


2，Transfer 042800.grb into 042800.grib1：
grib_set -f -s editionNumber=1 042800.grb 042800.grib1

Now，042800.grib1 data can be used by WPS.

But,ECMF data which was used was short of SLP ,soil moisture and soil temprature. So， We have to use  SLP，SM，ST from T639 model 
outputs or GFS outputs.
Take T639 model outputs as an example:
Before using T639 data，we have to replace a F90 file rd_grib2.f90 in ：WPS/ungrib/src in order to modify a bug in reading and Ungrib T639 data. 
This rd_grib2.f90 file can be downloaded here（http://bbs.06climate.com/forum.php?mod=viewthread&tid=38302）or this dictory.
And recompile WPS.
Vtable file can be downloaded here（http://bbs.06climate.com/forum.php?mod=viewthread&tid=38302） with SLP and ST, SM only .

Then，Use metgrid.exe to obtain MET data like this:
 You may neet to edit namelist.wps：
 &metgrid
fg_name = 'FILE','T639FILE',

Then，we can obtain MET data.
Note that，before run real.exe，you may need to edit namelist.input：
num_metgrid_levels                  = 18, # because ECMF data has only 18 levels in vertical direction
num_metgrid_soil_levels             = 4,
Now you can run ./real.exe and ./wrf.exe as usual.

2015-09-23
Andrew Soong

Hohhot，China

If you have any qustions,contact me please.
E-mail:haiqingsong2010@163.com






