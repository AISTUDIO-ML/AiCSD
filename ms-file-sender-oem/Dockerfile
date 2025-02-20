########################################################################
 # Copyright (c) Intel Corporation 2023
 # SPDX-License-Identifier: BSD-3-Clause
########################################################################

#build stage
FROM golang:1.21.3-alpine3.17 AS builder

RUN apk add --update --no-cache make=4.3-r1 gcc=12.2.1_git20220924-r4 libc-dev=0.7.2-r3 libsodium-dev=1.0.18-r2 zeromq-dev=4.3.4-r1
WORKDIR /app

COPY go.mod ./
RUN go mod download all
COPY . .

ARG MAKE="make file-sender-oem"
RUN $MAKE

#final stage
FROM alpine:3.17
# TODO: Change Copyright to your company if open sourcing or remove label
LABEL license='SPDX-License-Identifier: BSD-3-Clause' \
  copyright='Copyright (c) 2023: Intel Corporation'
LABEL name='ms-file-sender-oem' \ 
      version="${MSVERSION}"


# COPY --from=builder /app/Attribution.txt /Attribution.txt
COPY --from=builder /app/ms-file-sender-oem/LICENSE /LICENSE
COPY --from=builder /app/ms-file-sender-oem/res/ /res/
COPY --from=builder /app/ms-file-sender-oem/ms-file-sender-oem /ms-file-sender-oem

# dumb-init is required as security-bootstrapper uses it in the entrypoint script
RUN apk add --update --no-cache ca-certificates=20230506-r0 zeromq=4.3.4-r1 dumb-init=1.2.5-r2

EXPOSE 59782

ENTRYPOINT ["/ms-file-sender-oem"]
CMD ["-cp=consul.http://edgex-core-consul:8500", "--registry", "--confdir=/res", "-s"]