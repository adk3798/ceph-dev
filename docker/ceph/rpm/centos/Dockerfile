FROM centos:7

RUN yum install -y bind-utils centos-release-scl curl epel-release iputils net-tools yum yum-utils \
    && yum clean all
RUN yum install -y jq \
    && yum clean all

# For dev mode: compile frontend app into 'dist' directory.
RUN yum install -y devtoolset-8-gcc-c++ git python2-pip \
    && yum clean all
RUN pip install -U nodeenv pip tox \
    && nodeenv venv -n 10.16.0
RUN ln -s /venv/bin/node /usr/local/bin/node \
    && ln -s /venv/bin/npm /usr/local/bin/npm \
    && ln -s /venv/bin/npx /usr/local/bin/npx

# For dev. mode: run integration tests.
RUN yum install -y python2-devel \
    && yum clean all

# For dev. mode: run e2e tests.
COPY e2e/google-chrome.repo /etc/yum.repos.d
RUN yum install -y google-chrome-stable \
    && yum clean all

ARG REPO_URL
COPY rpm/set-ceph-repo.sh /root
RUN /root/set-ceph-repo.sh

RUN yum install -y ceph ceph-fuse ceph-mgr-dashboard ceph-radosgw \
    && yum clean all

RUN rm -rf /var/cache/yum/*

RUN mkdir -p /ceph/build /ceph/src

ARG VCS_BRANCH=master
RUN curl -LsS https://raw.githubusercontent.com/ceph/ceph/"$VCS_BRANCH"/src/vstart.sh \
    -o /ceph/src/vstart.sh \
    && chmod +x /ceph/src/vstart.sh

WORKDIR /ceph

ENV PATH="/opt/rh/devtoolset-8/root/usr/bin:$PATH"
