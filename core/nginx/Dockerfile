ARG DISTRO=alpine:3.12
FROM $DISTRO
# python3 shared with most images
RUN apk add --no-cache \
    python3 py3-pip git bash py3-multidict \
  && pip3 install --upgrade pip

# Shared layer between nginx, dovecot, postfix, postgresql, rspamd, unbound, rainloop, roundcube
RUN pip3 install socrate==0.2.0

# Image specific layers under this line
RUN apk add --no-cache certbot nginx nginx-mod-mail openssl curl \
 && pip3 install watchdog

COPY conf /conf
COPY static /static
COPY *.py /

EXPOSE 80/tcp 443/tcp 110/tcp 143/tcp 465/tcp 587/tcp 993/tcp 995/tcp 25/tcp 10025/tcp 10143/tcp
VOLUME ["/mailu"]

# https://stackoverflow.com/questions/38164939/can-we-mount-sub-directories-of-a-named-volume-in-docker
# Expects /certs && /overrides
RUN mkdir -p /mailu/certs /mailu/overrides 
RUN [ -d /certs ] && rmdir /certs ) || true
RUN ln -sf /mailu/certs /certs 
RUN [ -d /overrides ] && rmdir /overrides || true
RUN ln -sf /mailu/overrides /overrides

CMD /start.py

HEALTHCHECK CMD curl -k -f -L http://localhost/health || exit 1
