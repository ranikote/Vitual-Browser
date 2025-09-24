# Base image
ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

# Firefox download URL
ARG SRC_URL="https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US"

# Install Firefox and dependencies
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends openbox xz-utils libgtk-3-0 libdbus-glib-1-2 wget; \
    \
    # Download Firefox (follows redirects)
    wget -O /tmp/firefox-setup.tar.gz "${SRC_URL}" -L; \
    \
    # Verify archive is gzip (optional)
    file /tmp/firefox-setup.tar.gz; \
    \
    # Extract Firefox
    mkdir -p /usr/lib/firefox; \
    tar -xzf /tmp/firefox-setup.tar.gz -C /usr/lib/firefox --strip-components=1; \
    rm -f /tmp/firefox-setup.tar.gz; \
    ln -s /usr/lib/firefox/firefox /usr/bin/firefox; \
    \
    # Create Firefox profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    \
    # Clean up
    apt-get --purge autoremove -y xz-utils wget; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

# Copy configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml

