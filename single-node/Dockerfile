#https://airflow.apache.org/docs/apache-airflow/stable/installation.html#prerequisites
#Image based on: https://github.com/apache/airflow/blob/master/Dockerfile

#ARG responsible for store the Linux base image 
ARG LINUX_BASE_IMAGE="debian:buster"
FROM ${LINUX_BASE_IMAGE}

#SHELL command responsible for cancel the process if any result is other than 0 
SHELL ["/bin/bash", "-o", "pipefail", "-e", "-u", "-x", "-c"]

#ARGs responsible for all Airflow installation parameters
ARG AIRFLOW_VERSION="2.0.1"
ARG AIRFLOW_EXTRAS="apache.livy, cncf.kubernetes, apache.spark, amazon, sentry, celery, http, sftp"
ARG AIRFLOW_HOME=/opt/airflow
ARG AIRFLOW_UID="50000"
ARG AIRFLOW_GID="50000"
ARG AIRFLOW_PIP_VERSION=20.2.4

# Make sure noninteractive debian install is used and language variables set
ENV DEBIAN_FRONTEND=noninteractive LANGUAGE=C.UTF-8 LANG=C.UTF-8 LC_ALL=C.UTF-8 \
    LC_CTYPE=C.UTF-8 LC_MESSAGES=C.UTF-8

# Store into an ARG the all packages listed on and two additional (gnupg2 and wget)
ARG DEV_APT_DEPS="\
 curl\
 freetds-bin \
 krb5-user \
 ldap-utils \
 libffi6 \
 libsasl2-2 \
 libsasl2-modules \
 libssl1.1 \
 locales  \
 lsb-release \
 sasl2-bin \
 sqlite3 \
 unixodbc \
 #manually added
 gnupg2 \
 #manually added
 wget"
ENV DEV_APT_DEPS=${DEV_APT_DEPS}

# Install all packages listed before
RUN apt-get update
RUN apt-get install -y --no-install-recommends \
 ${DEV_APT_DEPS} \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# POSTGRESQL reference #https://www.postgresql.org/download/linux/debian/
# This container don't use the postgresql yet, but I will reuse this dockerfile for the cluster configuration
# so I already install the postgresql but it is not necessary for this container runs
RUN echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN wget --no-check-certificate --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
RUN apt-get update
RUN apt -y install postgresql-client-13

# Install PIP and set it to the recommended version on airflow doc
# You can check on https://airflow.apache.org/docs/apache-airflow/stable/installation.html#installation-tools
ENV AIRFLOW_PIP_VERSION=${AIRFLOW_PIP_VERSION}
RUN apt -y install python3-pip \
    && pip3 install --no-cache-dir --upgrade pip==${AIRFLOW_PIP_VERSION}
WORKDIR ${AIRFLOW_HOME}

# Configure all airflow paths, create the user airflow and set the correct permision for that user
ARG AIRFLOW_USER_HOME_DIR=/home/airflow
ENV AIRFLOW_USER_HOME_DIR=${AIRFLOW_USER_HOME_DIR}
ENV AIRFLOW_HOME=${AIRFLOW_HOME}
ENV AIRFLOW_UID=${AIRFLOW_UID}
ENV AIRFLOW_GID=${AIRFLOW_GID}
RUN addgroup --gid "${AIRFLOW_GID}" "airflow" && \
    adduser --quiet "airflow" --uid "${AIRFLOW_UID}" \
        --gid "${AIRFLOW_GID}" \
        --home "${AIRFLOW_USER_HOME_DIR}"

#Copy all shell scripts responsible to start airflow 
COPY *.sh ${AIRFLOW_USER_HOME_DIR}
#COPY start-webserver.sh ${AIRFLOW_USER_HOME_DIR}

# Create all dir needed and give the correct permission
RUN mkdir -p ${AIRFLOW_HOME}; \
    mkdir -p "${AIRFLOW_HOME}/dags"; \
    mkdir -p "${AIRFLOW_HOME}/logs"; \
    chown -R "airflow:root" "${AIRFLOW_USER_HOME_DIR}";\
    find "${AIRFLOW_HOME}" -executable -print0 | xargs --null chmod g+x && \
        find "${AIRFLOW_HOME}" -print0 | xargs --null chmod g+rw

# Finally install the Apache Airflow with all packages listed before
RUN pip3 install "apache-airflow[${AIRFLOW_EXTRAS}]==${AIRFLOW_VERSION}"

# Command responsible to start the airflow db and create some config files
RUN airflow db init 

# Set the correct permission to the some missing files 
RUN chown "airflow:root" /usr/local/bin/airflow;\
    chown -R "airflow:root" "${AIRFLOW_HOME}"

# Create a default user to login at the web page
RUN airflow users create -u airflow  -f airflow -l admin -e airflow -p airflow -r Admin

# Install the supervisor to run all services needed by airflow and copy the config file
RUN pip3 install supervisor
COPY supervisord.conf /etc/supervisor/supervisord.conf

# Set the correct user to execute the airflow
USER ${AIRFLOW_UID}

# Expose port 8080 to access the webserver
EXPOSE 8080

# Run the supervisord to start all process need by airflow
ENTRYPOINT [ "/usr/local/bin/supervisord" ]