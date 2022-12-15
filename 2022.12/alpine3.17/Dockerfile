FROM alpine:3.17

RUN addgroup -S raku && adduser -S raku -G raku

ARG rakudo_version=2022.12-01
ENV rakudo_version=${rakudo_version}

RUN buildDeps=' \
        bash \
        gcc \
        gnupg \
        libc-dev \
        make \
        perl \
    ' \
    \
    url="https://rakudo.org/dl/star/rakudo-star-${rakudo_version}.tar.gz" \
    keyserver="hkps://keys.openpgp.org" \
    keyfp="3E7E3C6EAF916676AC549285A2919382E961E2EE" \
    tmpdir="$(mktemp -d)" \
    && set -eux \
    && export GNUPGHOME="$tmpdir" \
    && apk add --no-cache --virtual .build-deps $buildDeps \
    && apk add --no-cache readline \
    && mkdir ${tmpdir}/rakudo \
    \
    && wget ${url}.asc -O ${tmpdir}/rakudo.tar.gz.asc \
    && wget $url -O ${tmpdir}/rakudo.tar.gz \
    \
    && gpg --batch --keyserver $keyserver --recv-keys $keyfp \
    && gpg --batch --verify ${tmpdir}/rakudo.tar.gz.asc ${tmpdir}/rakudo.tar.gz \
    \
    && tar xzf ${tmpdir}/rakudo.tar.gz --strip-components=1 -C ${tmpdir}/rakudo \
    && ( \
        cd ${tmpdir}/rakudo \
        && bash bin/rstar install -p /usr \
    ) \
    && rm -rf $tmpdir \
    && apk del --no-network .build-deps

ENV PATH=$PATH:/usr/share/perl6/core/bin:/usr/share/perl6/site/bin:/usr/share/perl6/vendor/bin

CMD ["raku"]
