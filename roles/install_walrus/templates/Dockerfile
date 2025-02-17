# Start with a base image of Ubuntu 24.04
FROM ubuntu:22.04

# Argument for the binary hash
ARG BINARY_VERSION
ARG BINARY_URL

# Install wget and any other dependencies
RUN apt-get update && apt-get install -y wget

# Set working directory
WORKDIR /usr/local/bin

# Download the sui binary
RUN wget ${BINARY_URL}/walrus-node-${BINARY_VERSION} -O walrus-node
RUN wget ${BINARY_URL}/walrus-${BINARY_VERSION} -O walrus

# Make the sui binary executable
RUN chmod +x walrus-node
RUN chmod +x walrus