ARG DISTRO=alpine:3.12
FROM $DISTRO
# python3 shared with most images
RUN apk add --no-cache \
    python3 py3-pip bash \
  && pip3 install --upgrade pip
# Image specific layers under this line
RUN apk add --no-cache clamav rsyslog wget clamav-libunrar

COPY conf /etc/clamav
COPY start.py /start.py
COPY health.sh /health.sh

EXPOSE 3310/tcp
VOLUME ["/mailu"]

# https://stackoverflow.com/questions/38164939/can-we-mount-sub-directories-of-a-named-volume-in-docker
# Expects /data 
RUN mkdir -p /mailu/filter \
    && ( [ -d /data ] && rmdir /data ) \
    && ln -sf /mailu/filter /data

CMD /start.py

HEALTHCHECK --start-period=350s CMD /health.sh
