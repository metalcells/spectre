### At first perform source build ###
FROM spectreproject/spectre-builder-fedora:1.0 as build
MAINTAINER HLXEasy <hlxeasy@gmail.com>

# Build parameters
ARG BUILD_THREADS="6"

# Runtime parameters
ENV BUILD_THREADS=$BUILD_THREADS

COPY . /spectre

RUN cd /spectre \
 && mkdir db4.8 leveldb tor \
 && patch < Docker/Fedora/QT5BinaryPath.patch \
 && ./autogen.sh \
 && ./configure \
        --enable-gui \
        --with-qt5=/usr/include/qt5 \
 && make -j${BUILD_THREADS}

### Now upload binaries to GitHub ###
FROM spectreproject/github-uploader:latest
MAINTAINER HLXEasy <hlxeasy@gmail.com>

ARG GITHUB_TOKEN=1234567
ARG SPECTRECOIN_RELEASE=latest
ARG SPECTRECOIN_REPOSITORY=spectre
ARG REPLACE_EXISTING_ARCHIVE=''
#ENV GITHUB_TOKEN=${GITHUB_TOKEN}
ENV ARCHIVE=Spectrecoin-${SPECTRECOIN_RELEASE}-Fedora.tgz

RUN mkdir -p /filesToUpload/usr/local/bin

COPY --from=build /spectre/src/spectrecoind /filesToUpload/usr/local/bin/
COPY --from=build /spectre/src/spectre /filesToUpload/usr/local/bin/spectrecoin

RUN cd /filesToUpload \
 && tar czf ${ARCHIVE} . \
 && github-release upload \
        --user spectrecoin \
        --security-token "${GITHUB_TOKEN}" \
        --repo "${SPECTRECOIN_REPOSITORY}" \
        --tag "${SPECTRECOIN_RELEASE}" \
        --name "${ARCHIVE}" \
        --file "/filesToUpload/${ARCHIVE}" \
        ${REPLACE_EXISTING_ARCHIVE} \
 && rm -rf /filesToUpload/* \
 && export GITHUB_TOKEN=---
