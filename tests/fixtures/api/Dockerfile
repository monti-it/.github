# Self-test fixture for container-scan.yml and iac-scan.yml.
# Built from scratch so the image scan stays green over time (no OS packages
# to age into new CVEs), and runs as a non-root user so the IaC scan's
# HIGH "image user should not be root" check passes.
FROM scratch
COPY hello.txt /hello.txt
USER 65532:65532
