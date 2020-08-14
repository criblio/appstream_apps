FROM centos:8

RUN yum -y update
RUN yum -y install epel-release
RUN yum -y update epel-release
RUN yum repolist
RUN yum -y install git

# lcov!!
RUN yum -y install wget
RUN yum -y install perl
RUN wget http://downloads.sourceforge.net/ltp/lcov-1.14-1.noarch.rpm
RUN rpm -ihv lcov-1.14-1.noarch.rpm

ARG ssh_prv_key
ARG ssh_pub_key

# Authorize SSH Host
RUN mkdir -p /root/.ssh && \
    chmod 0700 /root/.ssh && \
    ssh-keyscan github.com > /root/.ssh/known_hosts

# Add the keys and set permissions
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa && \
    echo "$ssh_pub_key" > /root/.ssh/id_rsa.pub && \
    chmod 600 /root/.ssh/id_rsa && \
    chmod 600 /root/.ssh/id_rsa.pub

 # Add bitbuckets key
RUN ssh-keyscan bitbucket.org >> /root/.ssh/known_hosts

# Remove host checking
RUN echo "Host bitbucket.org\n\tStrictHostKeyChecking no\n" >> /root/.ssh/config

RUN git clone git@bitbucket.org:cribl/scope.git

# Remove SSH keys
RUN rm -rf /root/.ssh/

WORKDIR /scope
#RUN cd scope && git checkout feature/CRIBL-2979-add-ability-to-load-static-ap
RUN pwd

#ENTRYPOINT ["./scope_env.sh", "build"]
#ENTRYPOINT ["tail", "-f", "/dev/null"]
