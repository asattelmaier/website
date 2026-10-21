# Not pinned to a digest on purpose: every build uses the currently patched ubuntu:24.04, like the CI runner.
FROM ubuntu:24.04



#==============================
# Copy binary and assets
#==============================
COPY --chmod=500 dist-newstyle /opt/website/dist
COPY src/Static /opt/website/Static
RUN find /opt/website/dist -type f -executable -name "website" -exec cp {} /opt/website/website \;
RUN rm -rf /opt/website/dist



#==============================
# Create log files
#==============================
WORKDIR /opt/website

RUN mkdir log \
  && touch log/access.log \
  && touch log/error.log



#==============================
# Create user
#==============================
RUN useradd -ms /bin/bash website \
  && chown -R website:website /opt/website

USER website

ENTRYPOINT ["./website", "localhost", "8000", "."]
