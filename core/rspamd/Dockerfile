ARG DISTRO=alpine:3.12
FROM $DISTRO
# python3 shared with most images
RUN apk add --no-cache \
    python3 py3-pip git bash py3-multidict \
  && pip3 install --upgrade pip

# Shared layer between nginx, dovecot, postfix, postgresql, rspamd, unbound, rainloop, roundcube
RUN pip3 install socrate==0.2.0

# Image specific layers under this line
RUN apk add --no-cache rspamd rspamd-controller rspamd-proxy rspamd-fuzzy ca-certificates curl

RUN mkdir /run/rspamd

COPY conf/ /conf
COPY start.py /start.py

EXPOSE 11332/tcp 11334/tcp 11335/tcp
VOLUME ["/mailu"]

# https://stackoverflow.com/questions/38164939/can-we-mount-sub-directories-of-a-named-volume-in-docker
# Expects /filter && /dkim && /var/lib/rspamd
RUN mkdir -p /mailu/filter /mailu/dkim /mailu/overrides/rspamd \
    && ( [ -d /var/lib/rspamd ] && rmdir /var/lib/rspamd ) \
    && ( [ -d /dkim ] && rmdir /dkim ) \
    && ( [ -d /etc/rspamd/override.d ] && rmdir /etc/rspamd/override.d ) \
    && ln -sf /mailu/filter /var/lib/rspamd \
    && ln -sf /mailu/dkim /dkim \
    && ln -sf /mailu/overrides/rspamd /etc/rspamd/override.d

CMD /start.py

HEALTHCHECK --start-period=350s CMD curl -f -L http://localhost:11334/ || exit 1
