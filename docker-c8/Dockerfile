FROM rockylinux:8

# Add labels
LABEL org.opencontainers.image.source=https://github.com/stfc/grid-workernode
LABEL org.opencontainers.image.authors=gstsm@stfc.atlassian.net
LABEL org.opencontainers.image.description="A specialised container image optimised for high-performance batch job execution in RAL's computing environment. \
It includes essential software, libraries, and dependencies, ensuring efficient, and scalable job processing, benefiting researchers and organisations."

# Basic dependencies
RUN dnf -y install \
    zip \
    curl \
    freetype \
    expat \
    gcc \
    glibc-headers \
    time \
    man \
    unzip \
    quota \
    attr \
    tcsh \
    numactl \
    bc && \
    # CVMFS (for SUM tests only; install before adding other repos to ensure we have the latest version)
    dnf -y install https://ecsft.cern.ch/dist/cvmfs/cvmfs-release/cvmfs-release-latest.noarch.rpm \
                   https://download.opensuse.org/repositories/home:/cvmfs:/contrib-egi/AlmaLinux_8/noarch/cvmfs-config-egi-2.7-1.8.obs.el8.noarch.rpm && \
    # Install CVMFS
    dnf -y install cvmfs && \
    # Install EPEL repository
    dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && \
    # Install HEP_OSlibs
    dnf -y install https://linuxsoft.cern.ch/wlcg/centos8/x86_64/wlcg-repo-1.0.0-1.el8.noarch.rpm && \
    dnf -y install --enablerepo=devel HEP_OSlibs && \
    # Boost (need to ensure gfal CLI from CVMFS will work for some VOs)
    dnf -y install boost-date-time boost-filesystem boost-graph boost-iostreams boost-program-options \
                   boost-regex boost-serialization boost-signals boost-system boost-test \
                   boost-thread boost-wave && \
    # Install Apptainer
    dnf -y install apptainer && \
    # Update & tidy up
    dnf -y update && \
    dnf clean all && \
    rm -rf /var/cache/dnf

# Create all possible pool accounts
RUN curl -s https://raw.githubusercontent.com/stfc/grid-workernode/master/resources/pool_accounts/create.sh | bash -

# Disable overlay and loop device management in Apptainer
RUN sed -i '/enable overlay/c\enable overlay = no' /etc/apptainer/apptainer.conf && \
    sed -i '/enable underlay/c\enable underlay = yes' /etc/apptainer/apptainer.conf && \
    sed -i '/max loop devices/c\max loop devices = 0' /etc/apptainer/apptainer.conf && \
    # Deny setuid in Apptainer
    sed -i '/allow setuid/c\allow setuid = no' /etc/apptainer/apptainer.conf
