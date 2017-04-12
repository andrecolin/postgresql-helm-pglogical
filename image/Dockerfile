FROM ubuntu:16.04

# set conatiner like parameters
ENV DEBIAN_FRONTEND noninteractive
ENV container docker

# update and install dependency packages
RUN apt-get update -qq && \
    apt-get dist-upgrade -y && \
    apt-get install -y wget

# add postgres and 2ndquandrant repo
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN echo "deb [arch=amd64] http://packages.2ndquadrant.com/pglogical/apt/ xenial-2ndquadrant main" > /etc/apt/sources.list.d/2ndquadrant.list

# add the postgres and 2ndquandrant key for pglogical extenson
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
RUN wget --quiet -O - http://packages.2ndquadrant.com/pglogical/apt/AA7A6805.asc | apt-key add -

# update to pull in repos and install pglogical
RUN apt-get update && \
    apt-get install -y postgresql postgresql-contrib postgresql-9.6-pglogical wget

# Run the rest of the commands as the ``postgres`` user created by the ``postgres-9.6`` package when it was ``apt-get installed``
USER postgres

# Create a PostgreSQL role named ``docker`` with ``docker`` as the password and
# then create a database `docker` owned by the ``docker`` role.
# Note: here we use ``&&\`` to run commands one after the other - the ``\``
#       allows the RUN command to span multiple lines.
# TODO:// move to kubernetes config set to do this job

RUN /etc/init.d/postgresql start &&\
    psql --command "CREATE USER test WITH SUPERUSER PASSWORD 'test';" &&\
    createdb -O test test

# Adjust PostgreSQL configuration so that remote connections to the
# database are possible.
RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/9.6/main/pg_hba.conf

# And add ``listen_addresses`` to ``/etc/postgresql/9.3/main/postgresql.conf``
RUN echo "listen_addresses='*'" >> /etc/postgresql/9.6/main/postgresql.conf

# Expose the PostgreSQL port
EXPOSE 5432

# Add VOLUMEs to allow backup of config, logs and databases
VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

# Set the default command to run when starting the container
CMD ["/usr/lib/postgresql/9.6/bin/postgres", "-D", "/var/lib/postgresql/9.6/main", "-c", "config_file=/etc/postgresql/9.6/main/postgresql.conf"]


