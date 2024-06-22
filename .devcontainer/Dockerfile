#adapted from https://github.com/JuliaGPU/julia-ngc
# ARG IMAGE=nvidia/cuda:12.1.1-devel-ubuntu20.04
# FROM nvidia/cudagl:11.3.0-devel
# FROM nvidia/cuda:11.2.2-cudnn8-devel-ubuntu20.04
FROM postgres:13.12-bookworm

ARG JULIA_RELEASE=1.10
ARG JULIA_VERSION=1.10.4
# ARG JULIA_VERSION=1.9.0-rc3

ENV DEBIAN_FRONTEND=noninteractive

# julia

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install --yes --no-install-recommends \
                    # basic stuff
                    curl ca-certificates nano && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN curl -s -L https://julialang-s3.julialang.org/bin/linux/x64/${JULIA_RELEASE}/julia-${JULIA_VERSION}-linux-x86_64.tar.gz | \
    tar -C /usr/local -x -z --strip-components=1 -f -


ENV JULIA_DEPOT_PATH=/usr/local/share/julia
COPY startup.jl /usr/local/share/julia/config/
RUN mkdir $HOME/data

ENV JULIA_HISTORY=/data/logs/repl_history.jl
#TODO set up dynamically after start
ENV LD_LIBRARY_PATH=""

# RUN python3 /workspace/download_data.py


# setup enviromental variables for Julia multithreading keeping one interactive thread
COPY setup_threads.sh  /etc/profile.d/


RUN apt-get update && apt-get install -y --no-install-recommends \
        pkg-config \
        libglvnd-dev libglvnd-dev \
        libgl1-mesa-dev libgl1-mesa-dev \
        libegl1-mesa-dev libegl1-mesa-dev \
        libgles2-mesa-dev libgles2-mesa-dev && \
    rm -rf /var/lib/apt/lists/*




# RUN useradd -u 1001 jm
USER postgres


# # Create the directory for the server configuration file
# RUN mkdir -p /var/lib/postgresql/data

# # Copy the server configuration file to the appropriate location
# COPY postgresql.conf /var/lib/postgresql/data/postgresql.conf
# # FROM nvidia/cuda:12.1.1-cudnn8-devel-ubuntu22.04


mkdir -p /var/lib/postgresql/data
touch /var/lib/postgresql/data/postgresql.conf
# RUN chown -R postgres:postgres /var/lib/postgresql/data
RUN chmod 777 /var/lib/postgresql/data

RUN mkdir -p /var/lib/postgresql/data && \
    touch /var/lib/postgresql/data/PG_VERSION && \
    /var/lib/postgresql/data/postgresql.conf && \
    chmod 700 /var/lib/postgresql/data
# postgres -D "/var/lib/postgresql/data"


# RUN apt-get update && apt-get install -y postgresql postgresql-client
WORKDIR "/workspace"
COPY examples /workspace/examples

ENTRYPOINT ["/usr/local/bin/julia"]
CMD ["sleep", "infinity"]



# /usr/local/bin/julia