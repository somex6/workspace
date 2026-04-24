
FROM python:3.12-slim@sha256:PLACEHOLDER

LABEL maintainer="platform-team" \
      org.opencontainers.image.title="python-base" \
      org.opencontainers.image.description="base image" \
      org.opencontainers.image.version="${VERSION}" \
      org.opencontainers.image.source="https://github.com/your-org/images"

# Install tini for proper signal handling + minimal tools
RUN apt-get update && apt-get install -y --no-install-recommends \
        tini \
        ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    # CIS: Remove SUID/SGID bits from all binaries
    && find / -perm /6000 -type f -exec chmod a-s {} \; 2>/dev/null || true

# CIS: Create non-root user (no home dir write access beyond /app)
RUN groupadd -r nodeuser && useradd -r -g nodeuser -s /sbin/nologin nodeuser

WORKDIR /app

# CIS: Restrict ownership
RUN chown -R nodeuser:nodeuser /app

USER nodeuser

# Tini as PID 1 for proper signal handling
ENTRYPOINT ["/usr/bin/tini", "--"]

# Overridable healthcheck (applications should override this)
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD python -c "import sys; sys.exit(0)"
