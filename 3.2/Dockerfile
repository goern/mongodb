FROM centos:centos7
MAINTAINER SoftwareCollections.org <sclorg@redhat.com>

# MongoDB image for OpenShift.
#
# Volumes:
#  * /var/lib/mongodb/data - Datastore for MongoDB
# Environment:
#  * $MONGODB_USER - Database user name
#  * $MONGODB_PASSWORD - User's password
#  * $MONGODB_DATABASE - Name of the database to create
#  * $MONGODB_ADMIN_PASSWORD - Password of the MongoDB Admin

ENV MONGODB_VERSION=3.2 \
    HOME=/var/lib/mongodb

LABEL io.k8s.description="MongoDB is a scalable, high-performance, open source NoSQL database." \
      io.k8s.display-name="MongoDB 3.2" \
      io.openshift.expose-services="27017:mongodb" \
      io.openshift.tags="database,mongodb,rh-mongodb3.2"

EXPOSE 27017

# This image must forever use UID 184 for mongodb user and GID 998 for the
# mongodb group, so our volumes are safe in the future. This should *never*
# change, the last test is there to make sure of that.
# Due to the https://bugzilla.redhat.com/show_bug.cgi?id=1206151,
# the whole /var/lib/mongodb/ dir has to be chown-ed.
RUN yum install -y centos-release-scl && \
    useradd -m -N -u 184 mongodb && \
    INSTALL_PKGS="bind-utils gettext iproute rsync tar openssl" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all && \
    rpm -Uvh http://repo.mongodb.org/yum/redhat/7/mongodb-org/3.2/x86_64/RPMS/mongodb-org-mongos-3.2.3-1.el7.x86_64.rpm && \
    rpm -Uvh http://repo.mongodb.org/yum/redhat/7/mongodb-org/3.2/x86_64/RPMS/mongodb-org-server-3.2.3-1.el7.x86_64.rpm && \
    rpm -Uvh http://repo.mongodb.org/yum/redhat/7/mongodb-org/3.2/x86_64/RPMS/mongodb-org-shell-3.2.3-1.el7.x86_64.rpm && \
    rpm -Uvh http://repo.mongodb.org/yum/redhat/7/mongodb-org/3.2/x86_64/RPMS/mongodb-org-tools-3.2.3-1.el7.x86_64.rpm && \
    mkdir -p /var/lib/mongodb/data /var/opt/rh/rh-mongodb32/lib/mongodb && chown -R mongodb.0 /var/lib/mongodb/ /var/opt/rh/rh-mongodb32/lib/mongodb && \
    # Loosen permission bits to avoid problems running container with arbitrary UID
    chmod g+w -R /var/opt/rh/rh-mongodb32/lib/mongodb && \
    chmod -R g+rwx /var/lib/mongodb

# Get prefix path and path to scripts rather than hard-code them in scripts
ENV CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/mongodb \
    MONGODB_PREFIX=/opt/rh/rh-mongodb32/root/usr \
    MONGODB_PREFIX=/opt/mongodb32

ADD root /

# Container setup
RUN touch /etc/mongod.conf && chown mongodb:0 /etc/mongod.conf && /usr/libexec/fix-permissions /etc/mongod.conf

VOLUME ["/var/lib/mongodb/data"]

USER 184

ENTRYPOINT ["container-entrypoint"]
CMD ["run-mongod"]
