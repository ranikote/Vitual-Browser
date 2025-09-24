# Base image
ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

# Set Firefox download URL
ARG SRC_URL="https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US"

# Install Firefox and dependencies
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends openbox bzip2 libgtk-3-0 libdbus-glib-1-2 wget; \
    \
    # Download Firefox with redirect handling
    wget -O /tmp/firefox-setup.tar.bz2 "${SRC_URL}" -L; \
    \
    # Verify the archive
    bzip2 -t /tmp/firefox-setup.tar.bz2 || { echo "Error: Firefox archive is invalid"; exit 1; }; \
    \
    # Extract Firefox
    mkdir -p /usr/lib/firefox; \
    tar -xjf /tmp/firefox-setup.tar.bz2 -C /usr/lib/firefox --strip-components=1; \
    rm -f /tmp/firefox-setup.tar.bz2; \
    ln -s /usr/lib/firefox/firefox /usr/bin/firefox; \
    \
    # Create Firefox profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    \
    # Clean up
    apt-get --purge autoremove -y bzip2 wget; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

# Copy configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml

