# ============================================================
# Golden Base Image: Java 21 (Temurin JRE, CIS-Hardened)
# ============================================================
# IMPORTANT: Replace @sha256:PLACEHOLDER with a real digest:
#   docker pull eclipse-temurin:21-jre-jammy
#   docker inspect --format='{{index .RepoDigests 0}}' eclipse-temurin:21-jre-jammy
# ============================================================
FROM eclipse-temurin:21-jre-jammy@sha256:PLACEHOLDER

LABEL maintainer="platform-team" \
      org.opencontainers.image.title="golden/java-base" \
      org.opencontainers.image.description="CIS-hardened Java 21 Temurin JRE base image" \
      org.opencontainers.image.version="${VERSION}" \
      org.opencontainers.image.source="https://github.com/your-org/golden-images"

RUN apt-get update && apt-get install -y --no-install-recommends \
        tini \
        ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    # CIS: Remove SUID/SGID bits
    && find / -perm /6000 -type f -exec chmod a-s {} \; 2>/dev/null || true

# CIS: Non-root user
RUN groupadd -r appuser && useradd -r -g appuser -s /sbin/nologin appuser

WORKDIR /app

RUN chown -R appuser:appuser /app

USER appuser

ENTRYPOINT ["/usr/bin/tini", "--"]

HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
    CMD java -version 2>&1 | grep -q "21" && exit 0 || exit 1
