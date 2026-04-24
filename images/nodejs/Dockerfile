# syntax=docker/dockerfile:1
# ============================================================
# Golden Base Image: Node.js 22 LTS (CIS-Hardened)
# ============================================================
# IMPORTANT: Replace @sha256:PLACEHOLDER with a real digest:
#   docker pull node:22-slim
#   docker inspect --format='{{index .RepoDigests 0}}' node:22-slim
# ============================================================
FROM node:22-slim@sha256:PLACEHOLDER

LABEL maintainer="platform-team" \
      org.opencontainers.image.title="node-base" \
      org.opencontainers.image.description="CIS-hardened mage" \
      org.opencontainers.image.version="${VERSION}" \
      org.opencontainers.image.source="https://github.com/your-org/images"

RUN apt-get update && apt-get install -y --no-install-recommends \
        tini \
        ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    # CIS: Remove SUID/SGID bits
    && find / -perm /6000 -type f -exec chmod a-s {} \; 2>/dev/null || true

# CIS: Use existing node user (uid 1000) — no SUID/SGID
RUN groupmod -g 1000 node && usermod -u 1000 -g 1000 -s /sbin/nologin node

WORKDIR /app

RUN chown -R node:node /app

USER node

ENTRYPOINT ["/usr/bin/tini", "--"]

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD node -e "process.exit(0)"
