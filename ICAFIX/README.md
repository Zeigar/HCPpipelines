# HCP Pipelines ICAFIX subdirectory.

This directory contains [HCP] and [Washington University] official versions
of scripts related to [FSL]'s [FIX].

See Examples/Scripts/IcaFixProcessingBatch.sh for an example launching
script.

Note that `FSL_FIXDIR` should be set to the standard [FIX]
installation. You may need to modify your FIX installation to fit your
environment. In particular, the ${FSL_FIXDIR}/settings.sh file may
need modification.  (The settings.sh.WUSTL_CHPC2 file in this
directory is the settings.sh file that is used on the WUSTL "CHPC2"
cluster).

# Supplemental instructions for installing fix

Some effort (trial and error) is required to install the versions of R packages specified on the [FIX User Guide] page, so below are instructions obtained from working installations of fix.  Note that [FIX]'s minimum supported version of R is 3.3.0.

### Ubuntu 14.04

```bash
#superuser permissions are required for all steps as written, you can use "sudo -s" to obtain a root-privileged shell

#cran includes packages of R for ubuntu (and other linux distros) which are in sync with cran
#this repo should install a 3.4.x version - 3.5.x doesn't seem to be able to install the specified package versions
echo deb http://cloud.r-project.org/bin/linux/ubuntu trusty/ >> /etc/apt/sources.list
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 0x51716619E084DAB9

#install build dependencies for the R packages we need
apt-get update && apt-get install build-essential bc r-base-core gfortran libblas-dev liblapack-dev libcurl4-openssl-dev libssl-dev libssh2-1-dev --no-install-recommends

#use R to install devtools, then use its install_version for the rest
echo '
  install.packages("devtools", repos = "http://cloud.r-project.org/");
  require(devtools);
  install_version("kernlab", version = "0.9-24", repos = "http://cloud.r-project.org/");
  install_version("ROCR", version = "1.0-7", repos = "http://cloud.r-project.org/");
  install_version("class", version = "7.3-14", repos = "http://cloud.r-project.org/");
  install_version("party", version = "1.0-25", repos = "http://cloud.r-project.org/");
  install_version("e1071", version = "1.6-7", repos = "http://cloud.r-project.org/");
  install_version("randomForest", version = "4.6-12", repos = "http://cloud.r-project.org/");
' | R --vanilla
```

### Generic approach, tested on 3.3.x and 3.4.x

```bash
#superuser permissions are required for most steps as written, you can use "sudo -s" to obtain a root-privileged shell

#PICK ONE:
#1) fedora/redhat/centos dependencies for R packages
yum -y groupinstall 'Development Tools'
yum -y install blas-devel lapack-devel qt-devel mesa-libGLU openssl-devel libssh-devel

#2) debian/ubuntu dependencies
apt-get update && apt-get install -y build-essential libblas-dev liblapack-dev qt5-default libglu1-mesa libcurl4-openssl-dev libssl-dev libssh2-1-dev --no-install-recommends

#R and recommended R packages must already be installed
PACKAGES="mvtnorm_1.0-8 modeltools_0.2-22 zoo_1.8-4 sandwich_2.5-0 strucchange_1.5-1 TH.data_1.0-9 survival_2.43-3 multcomp_1.4-8 coin_1.2-2 bitops_1.0-6 gtools_3.8.1 gdata_2.18.0 caTools_1.17.1.1 gplots_3.0.1 kernlab_0.9-24 ROCR_1.0-7 class_7.3-14 party_1.0-25 e1071_1.6-7 randomForest_4.6-12"
MIRROR="http://cloud.r-project.org"

for package in $PACKAGES
do
    wget "$MIRROR"/src/contrib/Archive/$(echo "$package" | cut -f1 -d_)/"$package".tar.gz || \
        wget "$MIRROR"/src/contrib/"$package".tar.gz
    R CMD INSTALL "$package".tar.gz
done
```

<!-- References -->

[HCP]: http://www.humanconnectome.org
[Washington University]: http://www.wustl.edu
[FSL]: http://fsl.fmrib.ox.ac.uk/fsl/fslwiki
[FIX]: http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FIX
[FIX User Guide]: https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FIX/UserGuide
