# Stage 1: Build stage
FROM node:20-alpine AS build

USER root

# CVE-2026-31431 mitigation (Copy Fail)
RUN echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf && \
    rmmod algif_aead 2>/dev/null || true

# Skip downloading Chrome for Puppeteer (saves build time)
ENV PUPPETEER_SKIP_DOWNLOAD=true

# Install latest Flowise globally (specific version can be set: flowise@1.0.0)
RUN npm install -g flowise

# Stage 2: Runtime stage
FROM node:20-alpine

# Install runtime dependencies
RUN apk add --no-cache chromium git python3 py3-pip make g++ build-base cairo-dev pango-dev curl

# CVE-2026-31431 mitigation (Copy Fail)
RUN echo "install algif_aead /bin/false" > /etc/modprobe.d/disable-algif.conf && \
    rmmod algif_aead 2>/dev/null || true

# Set the environment variable for Puppeteer to find Chromium
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

# Copy Flowise from the build stage
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin /usr/local/bin

ENTRYPOINT ["flowise", "start"]
