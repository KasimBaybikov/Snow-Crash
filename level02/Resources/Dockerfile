FROM alpine

RUN apk update && apk upgrade
RUN apk add tcpdump
RUN apk add wireshark
COPY ./level02.pcap /
RUN apk add vim
