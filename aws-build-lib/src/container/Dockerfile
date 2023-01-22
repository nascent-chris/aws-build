# The FROM_IMAGE arg can be set to either an Amazon Linux 2 image or a
# Lambda image
ARG FROM_IMAGE
FROM $FROM_IMAGE

# This is already in the Lambda image but not in the AL2 image
RUN yum update -y
RUN yum install -y gcc unzip


# Install Rust
ARG RUST_VERSION
RUN curl -o /rustup.sh --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs
RUN CARGO_HOME=/cargo RUSTUP_HOME=/rustup sh /rustup.sh -y --profile minimal --default-toolchain $RUST_VERSION
RUN . /cargo/env
# run rustup update to ensure we have the latest version of rust
RUN CARGO_HOME=/cargo RUSTUP_HOME=/rustup /cargo/bin/rustup update

RUN curl -LO https://github.com/protocolbuffers/protobuf/releases/download/v3.15.8/protoc-3.15.8-linux-x86_64.zip
RUN unzip protoc-3.15.8-linux-x86_64.zip -d /protoc
RUN chmod 777 /protoc -R
RUN /protoc/bin/protoc --version
ENV PROTOC=/protoc/bin/protoc

ARG DEV_PKGS
RUN if [[ ! -z "$DEV_PKGS" ]] ; then yum install -y $DEV_PKGS ; fi

# Add the build script
ADD build.sh /build.sh
RUN chmod +x /build.sh

VOLUME ["/code"]

# Change to the project directory.
ARG PROJECT_PATH
WORKDIR /code/"$PROJECT_PATH"

# Run the auxiliary install script
# RUN /aws_build.sh

ENTRYPOINT ["/build.sh"]
