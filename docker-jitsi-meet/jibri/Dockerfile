ARG JITSI_REPO=jitsi
FROM ${JITSI_REPO}/base-java

#ARG CHROME_RELEASE=latest
#ARG CHROMEDRIVER_MAJOR_RELEASE=latest
ARG CHROME_RELEASE=78.0.3904.97
ARG CHROMEDRIVER_MAJOR_RELEASE=78

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN \
	apt-dpkg-wrap apt-get update \
	&& apt-dpkg-wrap apt-get install -y jibri libgl1-mesa-dri \
	&& apt-cleanup
	&&	apt-get update && apt install linux-image-extra-virtual
	

RUN \
	[ "${CHROME_RELEASE}" = "latest" ] \
	&& curl -4s https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
	&& echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" > /etc/apt/sources.list.d/google-chrome.list \
	&& echo "options snd-aloop enable=1,1,1,1,1 index=0,1,2,3,4" > /etc/modprobe.d/alsa-loopback.conf
	&& echo "snd-aloop" >> /etc/modules
	&& lsmod | grep snd_aloop
	&& apt-dpkg-wrap apt-get update \
	&& apt-dpkg-wrap apt-get install -y google-chrome-stable \
	&& apt-cleanup \
	|| true

RUN \
        [ "${CHROME_RELEASE}" != "latest" ] \
        && curl -4so "/tmp/google-chrome-stable_${CHROME_RELEASE}-1_amd64.deb" "http://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_${CHROME_RELEASE}-1_amd64.deb" \
	&& apt-dpkg-wrap apt-get update \
        && apt-dpkg-wrap apt-get install -y "/tmp/google-chrome-stable_${CHROME_RELEASE}-1_amd64.deb" \
	&& apt-cleanup \
	|| true

RUN \
	[ "${CHROMEDRIVER_MAJOR_RELEASE}" = "latest" ] \
	&& CHROMEDRIVER_RELEASE="$(curl -4Ls https://chromedriver.storage.googleapis.com/LATEST_RELEASE)" \
	|| CHROMEDRIVER_RELEASE="$(curl -4Ls https://chromedriver.storage.googleapis.com/LATEST_RELEASE_${CHROMEDRIVER_MAJOR_RELEASE})" \
	&& curl -4Ls "https://chromedriver.storage.googleapis.com/${CHROMEDRIVER_RELEASE}/chromedriver_linux64.zip" \
	| zcat >> /usr/bin/chromedriver \
	&& chmod +x /usr/bin/chromedriver \
	&& chromedriver --version

RUN \
        apt-dpkg-wrap apt-get update \
        && apt-dpkg-wrap apt-get install -y jitsi-upload-integrations jq \
		&& apt-cleanup

COPY rootfs/ /

VOLUME /config

