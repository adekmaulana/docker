FROM alpine:latest AS builder

RUN ls /etc

RUN apk update \
  && apk add --no-cache sudo curl \
  && apk add --no-cache alpine-sdk \
  && adduser -s /bin/ash -G abuild -D builder \
  && addgroup builder wheel \
  && echo "Set disable_coredump false" >>/etc/sudo.conf \
  && echo "%wheel ALL=(ALL) NOPASSWD: ALL" >>/etc/sudoers \
  && chmod 0440 /etc/sudoers
RUN sudo -u builder sh -c "cd ~ && echo /home/builder/key.rsa | abuild-keygen \
  && echo PACKAGER_PRIVKEY=/home/builder/key.rsa >>/home/builder/.abuild/abuild.conf \
  && sudo cp ~/key.rsa.pub /etc/apk/keys \
  && git clone --depth 1 --branch master https://git.alpinelinux.org/aports \
  && cd aports/community/aria2 \
  && curl -LJO https://gist.githubusercontent.com/adekmaulana/1b9549f11238f6ef489f322ef2c33ee8/raw/fbbb53d06976c25bcd280c545e3155dafdd9309d/APKBUILD > APKBUILD \
  && abuild checksum && abuild -r"


FROM alpine:latest

COPY --from=builder /home/builder/packages/community/x86_64/aria2-1* /tmp

RUN apk add --allow-untrusted --no-cache /tmp/aria2-1* \
  && mkdir -p /srv/aria2 /etc/aria2

VOLUME ["/srv/aria2", "/etc/aria2"]

ENTRYPOINT ["aria2c"]
