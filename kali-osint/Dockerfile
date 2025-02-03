FROM kalilinux/kali-rolling as builder

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /opt

# Install only the build dependencies
RUN apt update && apt install -y \
    python3 python3-pip python3-venv \
    build-essential python3-dev libopenblas-dev gfortran \
    libxml2-dev libxslt-dev libssl-dev git && \
    rm -rf /var/lib/apt/lists/*

# Create virtual environment
RUN python3 -m venv /opt/venv

# Clone OSINT tools
RUN git clone --depth=1 https://github.com/sundowndev/PhoneInfoga.git /opt/PhoneInfoga && \
    git clone --depth=1 https://github.com/sherlock-project/sherlock.git /opt/sherlock && \
    git clone --depth=1 https://github.com/xillwillx/skiptracer.git /opt/skiptracer

# Modify skiptracer's requirements.txt to fix compatibility issues
RUN sed -i '/pprint==0.1/d' /opt/skiptracer/requirements.txt && \
    sed -i 's|git+git@github.com:xillwillx/skiptracer.git|git+https://github.com/xillwillx/skiptracer.git|' /opt/skiptracer/requirements.txt && \
    sed -i 's|numpy==1.19.0|numpy>=1.20.0|' /opt/skiptracer/requirements.txt

# Install only required dependencies
RUN /bin/bash -c "source /opt/venv/bin/activate && \
    pip install --upgrade pip wheel setuptools && \
    if [ -f /opt/PhoneInfoga/requirements.txt ]; then pip install --no-cache-dir -r /opt/PhoneInfoga/requirements.txt || true; fi && \
    if [ -f /opt/sherlock/requirements.txt ]; then pip install --no-cache-dir -r /opt/sherlock/requirements.txt || true; fi && \
    if [ -f /opt/skiptracer/requirements.txt ]; then pip install --no-cache-dir -r /opt/skiptracer/requirements.txt || true; fi"

# Final stage
FROM kalilinux/kali-rolling

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /opt

# Install only runtime dependencies
RUN apt update && apt install -y \
    python3 python3-venv \
    proxychains tor \
    libopenblas0 \
    netcat-traditional \
    && rm -rf /var/lib/apt/lists/* \
    && apt clean autoclean \
    && apt autoremove -y

# Copy virtual environment and tools from builder
COPY --from=builder /opt/venv /opt/venv
COPY --from=builder /opt/PhoneInfoga /opt/PhoneInfoga
COPY --from=builder /opt/sherlock /opt/sherlock
COPY --from=builder /opt/skiptracer /opt/skiptracer

# Create healthcheck script
COPY <<-"EOF" /usr/local/bin/healthcheck.sh
#!/bin/bash
nc -z localhost 22 || exit 1
EOF

RUN chmod +x /usr/local/bin/healthcheck.sh

# Create entrypoint script
COPY <<-"EOF" /usr/local/bin/entrypoint.sh
#!/bin/bash
# Start SSH service if installed
if [ -f /etc/init.d/ssh ]; then
    service ssh start
fi

# Keep container running
tail -f /dev/null
EOF

RUN chmod +x /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]